<h3 id="디렉토리-구조">디렉토리 구조</h3>
<pre><code class="language-bash">my-app/
│── .github/
│   └── workflows/
│       └── deploy.yml       # GitHub Actions 설정
│── src/
│   ├── server.js            # Node.js 서버 코드
│── package.json             # 프로젝트 설정
│── Dockerfile               # Docker 설정
│── .dockerignore            # Docker 빌드 제외 파일
│── README.md                # 프로젝트 설명</code></pre>
<h3 id="serverjs">server.js</h3>
<pre><code class="language-js">const express = require(&quot;express&quot;);
const app = express();

app.get(&quot;/&quot;, (req, res) =&gt; {
  res.send(&quot;Hello, CI/CD with Docker and GitHub Actions!&quot;);
});

const PORT = 80;
app.listen(PORT, () =&gt; {
  console.log(`Server is running on port ${PORT}`);
});</code></pre>
<h3 id="packagejson">package.json</h3>
<pre><code class="language-json">{
  &quot;name&quot;: &quot;my-app&quot;,
  &quot;version&quot;: &quot;1.0.0&quot;,
  &quot;description&quot;: &quot;Simple Node.js server&quot;,
  &quot;main&quot;: &quot;server.js&quot;,
  &quot;scripts&quot;: {
    &quot;start&quot;: &quot;node server.js&quot;
  },
  &quot;dependencies&quot;: {
    &quot;express&quot;: &quot;^4.18.2&quot;
  }
}</code></pre>
<h3 id="docker-file">Docker file</h3>
<pre><code class="language-dockerfile"># 베이스 이미지
FROM node:18-alpine

# 작업 디렉토리 설정
WORKDIR /app

# 패키지 설치 및 소스 복사
COPY package.json .
RUN npm install
COPY . .

# 컨테이너 시작 명령어
CMD [&quot;npm&quot;, &quot;start&quot;]</code></pre>
<h2 id="github-actions-설정-cicd-자동화">Github Actions 설정 (CI/CD 자동화)</h2>
<h3 id="deployyml">deploy.yml</h3>
<pre><code class="language-yaml">name: CI/CD with Docker and EC2

on:
  push:
    branches:
      - main

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout Repository
        uses: actions/checkout@v3

      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v2

      - name: Log in to Docker Hub
        uses: docker/login-action@v2
        with:
          username: ${{ secrets.DOCKER_HUB_USERNAME }}
          password: ${{ secrets.DOCKER_HUB_ACCESS_TOKEN }}

      - name: Build and Push Docker Image
        run: |
          docker build -t ${{ secrets.DOCKER_HUB_USERNAME }}/my-app:latest .
          docker push ${{ secrets.DOCKER_HUB_USERNAME }}/my-app:latest

      - name: Deploy to EC2
        uses: appleboy/ssh-action@v0.1.7
        with:
          host: ${{ secrets.EC2_HOST }}
          username: ${{ secrets.EC2_USER }}
          key: ${{ secrets.EC2_SSH_KEY }}
          script: |
            docker pull ${{ secrets.DOCKER_HUB_USERNAME }}/my-app:latest  # 최신 이미지 가져오기
            docker stop my-app || true  # 기존 컨테이너 중지
            docker rm my-app || true  # 기존 컨테이너 삭제
            docker run -d --name my-app -p 80:80 ${{ secrets.DOCKER_HUB_USERNAME }}/my-app:latest  # 최신 버전 실행</code></pre>
<table>
<thead>
<tr>
<th>Secret Name</th>
<th>Value</th>
</tr>
</thead>
<tbody><tr>
<td>DOCKER_HUB_USERNAME</td>
<td>Docker Hub ID</td>
</tr>
<tr>
<td>DOCKER_HUB_ACCESS_TOKEN</td>
<td>Docker Hub Personal Access Token</td>
</tr>
<tr>
<td>EC2_HOST</td>
<td>EC2 퍼블릭 IP</td>
</tr>
<tr>
<td>EC2_USER</td>
<td>ubuntu</td>
</tr>
<tr>
<td>EC2_SSH_KEY</td>
<td>EC2 SSH Private Key (start 부터 end까지)</td>
</tr>
</tbody></table>