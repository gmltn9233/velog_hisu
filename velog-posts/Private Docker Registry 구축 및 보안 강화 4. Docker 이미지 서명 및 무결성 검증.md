<h2 id="docker-이미지-서명-및-무결성-검증">Docker 이미지 서명 및 무결성 검증</h2>
<h3 id="1-예시-이미지-만들기">1. 예시 이미지 만들기</h3>
<h4 id="11-dockerfile-만들기">1.1. Dockerfile 만들기</h4>
<pre><code class="language-sh">touch Dockerfile</code></pre>
<h4 id="12-dockerfile-내용-작성하기">1.2. Dockerfile 내용 작성하기</h4>
<pre><code class="language-sh">nano Dockerfile</code></pre>
<pre><code class="language-Dockerfile"># 1️⃣ 베이스 이미지 선택
FROM ubuntu:latest

# 2️⃣ 컨테이너에서 실행될 명령어 추가
RUN apt-get update &amp;&amp; apt-get install -y curl

# 3️⃣ 컨테이너 실행 시 기본 명령어 설정
CMD [&quot;echo&quot;, &quot;Hello, Docker!&quot;]</code></pre>
<h4 id="13-docker-이미지-빌드">1.3. Docker 이미지 빌드</h4>
<pre><code class="language-sh">docker build -t hisu9233/singed-image:latest .
docker build -t hisu9233/unsinged-image:latest .</code></pre>
<h3 id="2-docker-content-trust-활성화">2. Docker Content Trust 활성화</h3>
<pre><code class="language-sh">export DOCKER_CONTENT_TRUST=1</code></pre>
<h4 id="21-서명-키-생성">2.1. 서명 키 생성</h4>
<pre><code class="language-sh">docker trust key generate my-key</code></pre>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/055dc068-17d3-46a6-8529-7ac8dc857cc3/image.png" /></p>
<h4 id="22-서명자-추가">2.2. 서명자 추가</h4>
<pre><code class="language-sh">docker trust signer add --key my-key.pub my-signer hisu9233/signed-image</code></pre>
<ul>
<li>my-signer는 서명자의 이름</li>
<li>hisu9233/signed-image는 서명할 이미지 이름</li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/d6e1275b-7623-4997-bf75-fa31bad61361/image.png" /></p>
<h4 id="23-docker-이미지-빌드">2.3. Docker 이미지 빌드</h4>
<pre><code class="language-sh">docker stop private-registry
docker rm private-registry</code></pre>
<h3 id="3-서명된-이미지-push">3. 서명된 이미지 Push</h3>
<pre><code class="language-sh">docker push hisu9233/signed-image:latest</code></pre>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/a3a54578-bc7a-4058-8b04-48a5cdd7e6df/image.png" /></p>
<p>에러 발생
Docker Content Trust 활성화 시 HTTPS가 필수적인데, 이전 기본 인증 설정에서 TSL 설정이 빠졌다.</p>
<h4 id="컨테이너-삭제">컨테이너 삭제</h4>
<pre><code class="language-sh">docker stop private-registry
docker rm private-registry</code></pre>
<h4 id="컨테이너-재생성">컨테이너 재생성</h4>
<pre><code class="language-sh">docker run -d \
  -p 5000:5000 \
  --restart=always \
  --name private-registry \
  -v $(pwd)/certs:/certs \
  -v $(pwd)/auth:/auth \
  -e REGISTRY_HTTP_ADDR=0.0.0.0:5000 \
  -e REGISTRY_HTTP_TLS_CERTIFICATE=/certs/domain.crt \
  -e REGISTRY_HTTP_TLS_KEY=/certs/domain.key \
  -e REGISTRY_AUTH=htpasswd \
  -e REGISTRY_AUTH_HTPASSWD_REALM=&quot;Registry Realm&quot; \
  -e REGISTRY_AUTH_HTPASSWD_PATH=/auth/htpasswd \
  registry:2</code></pre>
<h4 id="서명된-이미지-push">서명된 이미지 push</h4>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/c4da650b-2919-400c-b45c-7ca46e35366b/image.png" /></p>
<p>서명 데이터가 없다는 에러 발생</p>
<h4 id="해결-notary-서버-직접-실행">해결) Notary 서버 직접 실행</h4>
<p>현재 Docker Notary 서버가 실행되지 않아서 서명 데이터를 저장할 수 없음</p>
<ul>
<li>로컬 Notray 서버 띄우기<pre><code class="language-sh">docker run -d \
-p 4443:4443 \
--name notary-server \
-v $(pwd)/certs:/certs \
-e NOTARY_SERVER_TLS_CERTIFICATE=/certs/domain.crt \
-e NOTARY_SERVER_TLS_KEY=/certs/domain.key \
notary</code></pre>
</li>
<li>환경 변수 설정<pre><code class="language-sh">export DOCKER_CONTENT_TRUST_SERVER=https://localhost:4443</code></pre>
</li>
</ul>