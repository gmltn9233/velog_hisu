<h2 id="🎯현-상황">🎯현 상황</h2>
<p>카페부 서비스 개발 중 빠른 배포를 위해 <code>Github Actions</code>를 이용한 CD 파이프라인을 구축하였다.</p>
<p>하지만 <strong>AI Repository</strong>에서 배포시, 빠르면 8분, 느리면 12분까지 소요되면서, 비정상적인 배포 속도를 보여줬다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/193b4378-a247-4cbe-9176-325fc00742d0/image.png" /></p>
<p>이러한 속도는 <strong>지속적 배포라는 CD 도입</strong>의 취지를 무의미하게 만들고, Agile한 개발 사이클에도 영향을 줄 수 있다고 판단하여 원인을 찾아보았다.</p>
<h2 id="🔍원인">🔍원인</h2>
<h4 id="기존에-작성했던-dockerfile">기존에 작성했던 Dockerfile</h4>
<pre><code class="language-docker"># Python 3.12.7 기반 이미지
FROM python:3.12.7-slim

# 작업 디렉토리 생성
WORKDIR /app

# 필수 시스템 유틸 설치
RUN apt-get update &amp;&amp; apt-get install -y curl tar &amp;&amp; rm -rf /var/lib/apt/lists/*

# 구글 관련 패키지 설치
RUN pip install google-genai

# requirements.txt 복사 및 설치
COPY ai_project/requirements.txt ./requirements.txt
RUN pip install --no-cache-dir -r requirements.txt

# 전체 코드 복사
COPY . .

# embedding_model.tar.gz 다운로드 및 압축 해제
RUN curl -L -o embedding_model.tar.gz https://storage.googleapis.com/ai_model_cafeboo/embedding_model.tar.gz &amp;&amp; \
    tar -xzf embedding_model.tar.gz -C /app/ai_project/models &amp;&amp; \
    rm embedding_model.tar.gz

# moderation_model 추가
RUN curl -L -o /app/ai_project/models/best_model.pt https://storage.googleapis.com/ai_model_cafeboo/moderation_model/best_model.pt


# FastAPI 실행 (내부 통신용)
CMD [&quot;uvicorn&quot;, &quot;ai_project.main:app&quot;, &quot;--host&quot;, &quot;0.0.0.0&quot;, &quot;--port&quot;, &quot;8000&quot;, &quot;--log-level&quot;, &quot;debug&quot;]</code></pre>
<p>기본적인 흐름은 아래와 같다.</p>
<ol>
<li>필수 시스템 패키지 및 Python 의존성 설치</li>
<li>전체 프로젝트 코드 복사</li>
<li>사전 학습된 임베딩, 검열 모델 다운로드 및 압축 해제 (github 용량 이슈)</li>
<li>애플리케이션 실행 환경 설정</li>
</ol>
<p>코드를 살펴보면 알 수 있듯이 해당 Dockerfile은 <strong>단일 스테이지</strong>에서 모든 작업을 순차적으로 수행하고 있다.</p>
<h3 id="💡스테이지란">💡스테이지란?</h3>
<p><strong>Dockerfile</strong>에서 <strong>스테이지(Stage)</strong>란, <strong>FROM</strong> 명령어로 시작되는 빌드 단계를 의미한다. </p>
<p><strong>Docker</strong>는 각 <strong>FROM</strong> 블록을 하나의 스테이지로 간주하며, 멀티스테이지 빌드에서는 여러 스테이지를 순차적으로 실행하고, 최종적으로 필요한 파일만 추려서 최종 이미지에 포함할 수 있다.</p>
<p><strong>멀티 스테이지</strong>를 이용하여 스테이지를 나누면 <strong>Docker 빌드를 크게 3가지 측면에서 최적화</strong> 할 수 있다.</p>
<h4 id="1-불필요한-파일을-최종-이미지에서-제거">1. 불필요한 파일을 최종 이미지에서 제거</h4>
<p><strong>단일 스테이지:</strong> 모델 압축 파일, 빌드 도구, 캐시 파일 등 빌드에만 필요한 파일도 전부 포함되므로 이미지 용량이 커짐</p>
<p><strong>멀티 스테이지:</strong> 최종 실행에 필요한 산출물만 선택해서 복사하여 필터링</p>
<pre><code class="language-docker"># base (빌드 전용)
FROM node:18 AS build
RUN npm install &amp;&amp; npm run build

# final (실행용)
FROM nginx:alpine
COPY --from=build /app/dist /usr/share/nginx/html</code></pre>
<p>-&gt; node, npm, node_modules 는 결과물에 포함되지 않음</p>
<h4 id="2-docker-캐시-효율-증가">2. Docker 캐시 효율 증가</h4>
<pre><code class="language-docker">FROM openjdk:8-jdk-alpine

ARG JAR_FILE=target/*.jar

COPY ${JAR_FILE} app.jar

ENTRYPOINT [&quot;java&quot;, &quot;-jar&quot;, &quot;/app.jar&quot;]</code></pre>
<p>위와 같은 Dockerfile이 있을때, <strong>Docker</strong> 는 <strong>명령어 단위로 Layer를 쌓으면서 이미지를 생성</strong>한다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/10f2d298-6664-4c6d-81e3-3cc02a5a00a4/image.png" /></p>
<p>하지만 매번 이 <strong>Layer</strong>를 다 실행하면 속도가 매우 느리기 때문에, Docker는 <strong><code>Docker Cache</code></strong>를 통해 문제를 해결한다. </p>
<p><strong>Layer</strong>에서 변한 것이 없다면 <strong>기존의 Layer를 재사용</strong>해서 build 과정에서 속도를 높이고, 중복된 Layer를 방지하여 저장공간의 효율을 높인다.</p>
<p>예를 들어 <code>COPY ${JAR_FILE} app.jar</code> 의 경우 app.jar이 변경되지 않으면 이전의 Cache를 적용하여 기존 레이어를 덮어쓰는 방식이다.</p>
<pre><code class="language-docker"># 빌드 전용
FROM gradle:7.6-jdk17 AS builder
COPY . /app
WORKDIR /app
RUN gradle build --no-daemon

# 실행 전용
FROM openjdk:17-alpine
COPY --from=builder /app/build/libs/app.jar /app.jar
ENTRYPOINT [&quot;java&quot;, &quot;-jar&quot;, &quot;/app.jar&quot;]</code></pre>
<p>그래서 위와 같이 멀티 스테이지 구조를 사용하면, builder 스테이지에서 <code>COPY . /app</code> 에서 파일이 변경되지 않으면 <strong>Gradle 빌드 결과물(app.jar) 도 캐시가 적용</strong>된다. </p>
<p>즉, <code>COPY --from=builder</code>는 출력 결과물만 가져오며, 실행 이미지 입장에서는 Gradle이나 소스코드 변경과 완전히 분리된 상태이므로 <strong>캐시 적중률이 높아지게 된다.</strong></p>
<h4 id="3-실행-이미지에서-보안-위험-최소화">3. 실행 이미지에서 보안 위험 최소화</h4>
<p>curl, tar, apt, gcc, wget 등은 <strong>빌드에는 필요하지만 실행 환경에는 불필요하고 위험 요소</strong>이기에, 멀티 스테이지를 통해 이런 툴이 포함되지 않는 안전한 프로덕션 이미지를 만들 수 있다.</p>
<p>예를 들어, apt, bash, vim 등 관리용 툴이 남아있으면 의도치 않게 <strong>내부 구조 노출 위험</strong>이 있으며, gcc, make 등이 있다면 <strong>악성 코드를 컨테이너 안에서 실시간으로 컴파일하거나 실행</strong>할 수 있다.</p>
<h2 id="🛠️해결방법">🛠️해결방법</h2>
<p>기존의 Dockerfile을 아래와 같은 멀티 스테이지 환경으로 분리하였다.</p>
<h3 id="1-base-os--python-환경">1. Base: OS + Python 환경</h3>
<pre><code class="language-docker">FROM python:3.12.7-slim AS base
WORKDIR /app
RUN apt-get update \
  &amp;&amp; apt-get install -y curl tar \
  &amp;&amp; rm -rf /var/lib/apt/lists/*</code></pre>
<p>Python + 기본 유틸(curl, tar)만 포함한 최소 베이스</p>
<h3 id="2-deps-pip-requirements-설치">2. Deps: pip requirements 설치</h3>
<pre><code class="language-docker">FROM base AS deps
COPY ai_project/requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt</code></pre>
<p>의존성 설치만 따로 분리해서, 코드가 바뀌어도 pip install 캐시는 유지됨</p>
<h3 id="3-models-모델-파일-다운로드만-수행">3. Models: 모델 파일 다운로드만 수행</h3>
<pre><code class="language-docker">FROM deps AS models
WORKDIR /app/ai_project/models
RUN curl -L -o embedding_model.tar.gz https://storage.googleapis.com/ai_model_cafeboo/embedding_model.tar.gz \
 &amp;&amp; tar -xzf embedding_model.tar.gz \
 &amp;&amp; rm embedding_model.tar.gz
 &amp;&amp; curl -L -o best_model.pt https://storage.googleapis.com/ai_model_cafeboo/moderation_model/best_model.pt</code></pre>
<p>tar.gz 같은 중간 압축파일은 이 스테이지에만 존재 → 최종 이미지엔 포함되지 않음</p>
<h3 id="4-final-최종-이미지---실행에-필요한-것만-복사">4. Final: 최종 이미지 - 실행에 필요한 것만 복사</h3>
<pre><code class="language-docker">FROM models AS final

WORKDIR /app

# (1) 의존성 복사
COPY --from=deps /usr/local/lib/python3.12 /usr/local/lib/python3.12
COPY --from=deps /usr/local/bin /usr/local/bin

# (2) 모델 복사
COPY --from=models /app/ai_project/models /app/ai_project/models

# (3) 코드 복사
COPY . .

# 실행
CMD [&quot;uvicorn&quot;, &quot;ai_project.main:app&quot;, &quot;--host&quot;, &quot;0.0.0.0&quot;, &quot;--port&quot;, &quot;8000&quot;, &quot;--log-level&quot;, &quot;debug&quot;]</code></pre>
<p>오직 실행에 필요한 라이브러리, 모델, 코드만 포함</p>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/e7cf30af-79cf-4f5a-9d61-700993647982/image.png" /></p>
<p>하지만 이것만으로는 유의미하게 배포가 빨라지지 않았다.
원인을 파악하기 위해 로그를 살펴보자.</p>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/92738320-4848-4941-a2a1-7ca06bf8a5e6/image.png" /></p>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/e06ed6b9-b980-412d-8557-ceee86e18751/image.png" /></p>
<p>여전히 필요한 패키지를 다시 다운받느라 오랜 시간이 소요되는것을 확인할 수 있다.
(<strong>전체 7m 29s 중</strong> Build &amp; Push Docker Image 단계에서 <strong>6m 33s</strong>)</p>
<h3 id="왜-캐시가-적용이-되지않았을까">왜 캐시가 적용이 되지않았을까?</h3>
<p><strong><code>Github Actions</code></strong>의 러너는 매번 새로운 가상 환경에서 실행된다. 그래서 모든 작업이 새롭게 다시 시작되고, Github에서 일부 환경을 위한 캐싱을 제공하지만, <strong>Docker 레이어에 대한 캐싱은 기본적으로 제공되지 않는다.</strong></p>
<p>이때, <strong><code>BuildKit</code></strong>을 사용해서 해당 문제를 해결 할 수 있다.</p>
<h4 id="💡buildkit이란">💡BuildKit이란?</h4>
<p><strong>BuildKit</strong>은 Docker의 차세대 빌드 엔진으로, 기존 docker build 명령의 성능과 유연성을 극적으로 개선하기 위해 만들어졌다. </p>
<p>BuildKit은 <strong>병렬 레이어 빌드, 캐시, 멀티 플랫폼 빌드, 시크릿 관리 등</strong> 다양한 추가 기능을 지원한다.</p>
<p><strong>buildx</strong> 를 통해서 BuildKit 을 CLI에서 활용할 수 있다.</p>
<pre><code class="language-bash"># BuildKit 기반 buildx 설정
- name: Set up QEMU Buildx
  uses: docker/setup-buildx-action@v2

# Buildx 캐시 디렉토리 설정
- name: Cache Buildx
  uses: actions/cache@v3
  with:
    path: /tmp/.buildx-cache
    key: ${{ runner.os }}-buildx-${{ github.sha }}
    restore-keys: |
      ${{ runner.os }}-buildx-

# 빌드 시 BuildKit 캐시 적용
- name: Build &amp; Push Docker Image
  run: |
    docker buildx build \
      --push \
      --cache-from=type=local,src=/tmp/.buildx-cache \
      --cache-to=type=local,dest=/tmp/.buildx-cache-new,mode=max \
      -t ${REGION}-docker.pkg.dev/${PROJECT_ID}/ai/cafeboo-ai:${{ github.sha }} .</code></pre>
<p>기존 일반적인 <strong>docker build 방식에서 buildx를 이용하여 캐싱을 사용하도록 deploy 파일을 변경</strong>해주었다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/55107dc1-a587-46e7-a5e5-85f205bf7bb2/image.png" /></p>
<p>제일 처음에 빌드시에는, 캐시가 존재하지 않기에 이전과 비슷하게 8분정도 소요됨을 확인할 수 있다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/05463eb6-b159-4c90-87db-e9e9e3464390/image.png" /></p>
<p>하지만 이후 다시 빌드하면 캐시가 적용되어 4분 이내로 소요시간이 단축되었다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/9f907082-db01-4af6-a50a-572a2d75751d/image.png" /></p>
<p>로그를 확인해보니 레이어간 캐시가 잘 적용되는 모습이다!</p>
<h3 id="참고">참고</h3>
<p><a href="https://velog.io/@qf9ar8nv/Docker-Cache%EB%A5%BC-%ED%86%B5%ED%95%B4-build-%EC%B5%9C%EC%A0%81%ED%99%94-%ED%95%98%EA%B8%B0">Docker-Cache를-통해-build-최적화-하기</a></p>
<p><a href="https://kimjingo.tistory.com/63">[Docker] Dockerfile - Multi-stage build(멀티스테이지 빌드)
</a></p>
<p><a href="https://tech.kakaoent.com/front-end/2022/220414-docker-cache/">GitHub Actions에서 도커 캐시를 적용해 이미지 빌드하기
</a></p>