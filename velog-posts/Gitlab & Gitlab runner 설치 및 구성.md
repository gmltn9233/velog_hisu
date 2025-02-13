<ul>
<li>본 과정은 <code>Docker Desktop</code> 환경에서 이루어진 실습입니다.</li>
</ul>
<h2 id="gitlab-설정">Gitlab 설정</h2>
<h3 id="1-docker-compose-설치-확인">1. Docker compose 설치 확인</h3>
<p><strong>Docker Desktop</strong>을 설치하면 기본적으로 <strong>Docker compose</strong>가 포함되어 있다.</p>
<pre><code class="language-sh">docker --version
docker compose version</code></pre>
<p>✅ 정상 출력 예시</p>
<pre><code class="language-sh">Docker version 24.0.5, build a8cfb1b
Docker Compose version v2.19.1</code></pre>
<h3 id="2-docker-composeyml-파일-생성">2. docker-compose.yml 파일 생성</h3>
<p>Gitlab을 실행할 디렉토리를 만들고 이동한다.</p>
<pre><code class="language-sh">mkdir -p ~/gitlab &amp;&amp; cd ~/gitlab</code></pre>
<pre><code class="language-sh">nano docker-compose.yml</code></pre>
<p>docker-compose.yml 파일을 생성하고 아래 내용을 입력한다.</p>
<ul>
<li><p>Gitlab용 docker-compose.yml</p>
<pre><code class="language-yaml">version: '3.8'
services:
gitlab:
  image: gitlab/gitlab-ce:latest
  container_name: gitlab
  restart: always
  hostname: gitlab.local
  ports:
    - &quot;80:80&quot;
    - &quot;443:443&quot;
    - &quot;22:22&quot;
  volumes:
    - ./config:/etc/gitlab
    - ./logs:/var/log/gitlab
    - ./data:/var/opt/gitlab
  environment:
    GITLAB_OMNIBUS_CONFIG: |
      external_url 'http://localhost'
  shm_size: '256m'</code></pre>
</li>
<li><p>gitlab 현재 폴더 확인
<img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/2f9644b9-088d-4465-aed8-c3a588630e42/image.png" /></p>
</li>
<li><p>Docker compose로 gitlab 실행</p>
<pre><code class="language-sh">docker compose up -d</code></pre>
</li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/d287e6c5-2cb2-411c-835b-85c5c82244a2/image.png" /></p>
<ul>
<li>실행된 컨테이너 확인</li>
</ul>
<pre><code class="language-sh">docker ps</code></pre>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/d107fd7a-c859-45ea-bf2e-073391b8c689/image.png" /></p>
<ul>
<li><p>초기 비밀번호 확인</p>
<pre><code class="language-sh">docker exec -it gitlab cat /etc/gitlab/initial_root_password</code></pre>
</li>
<li><p>로그인
id: root
pw: 초기 비밀번호 복사 후 붙여넣기
<img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/9fddae12-4c4c-4a33-a4b8-32e0e48773ac/image.png" /></p>
</li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/4ecb7fc2-fcfd-4bc3-8797-b816e678fea1/image.png" /></p>
<h2 id="gitlab-runner">Gitlab runner</h2>
<h3 id="디렉토리-설정">디렉토리 설정</h3>
<pre><code class="language-sh">mkdir -p ~/gitlab-runner/config &amp;&amp; cd ~/gitlab-runner</code></pre>
<h3 id="docker-composeyml-파일-생성">docker-compose.yml 파일 생성</h3>
<pre><code class="language-sh">version: '3.9'
services:
  gitlab-runner:
    image: gitlab/gitlab-runner:v16.0.2
    container_name: gitlab-runner
    restart: always
    volumes:
      - ./config:/etc/gitlab-runner
      - //var/run/docker.sock:/var/run/docker.sock</code></pre>
<h3 id="컨테이너-실행">컨테이너 실행</h3>
<pre><code class="language-sh">docker compose up -d</code></pre>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/cd22b7ce-579e-460f-9614-8a32e69fe646/image.png" /></p>
<h3 id="runners-생성">Runners 생성</h3>
<ul>
<li><p>Gitlab Admin Area 이동 -&gt; Runners 생성
<img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/4be9c081-d7e0-469c-9666-8d51fdd426b1/image.png" /></p>
</li>
<li><p>Run untagged jos 체크
<img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/efa2c703-bd9b-41c6-8441-001b5089fa17/image.png" /></p>
</li>
</ul>
<h3 id="runner-등록">Runner 등록</h3>
<p><strong>Step1</strong> 에 나오는 커맨드 복사</p>
<ul>
<li>Gitlab-runner 파드 접속</li>
</ul>