<h2 id="기본-인증basic-authentication-설정">기본 인증(Basic Authentication) 설정</h2>
<h3 id="1-사용자-계정-설정">1. 사용자 계정 설정</h3>
<h4 id="11-auth-폴더-생성">1.1. auth 폴더 생성</h4>
<pre><code class="language-sh">mkdir auth</code></pre>
<h4 id="12-htpasswd-파일-생성">1.2. htpasswd 파일 생성</h4>
<pre><code class="language-sh">htpasswd -Bc auth/htpasswd user1</code></pre>
<p>username: user1 을 의미
<img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/e5c70679-0271-49d2-a330-cdef33daa2a2/image.png" /></p>
<h3 id="2-인증-적용하여-registry-실행">2. 인증 적용하여 Registry 실행</h3>
<h4 id="21-기존-registry-정지-및-삭제">2.1. 기존 Registry 정지 및 삭제</h4>
<pre><code class="language-sh">docker stop private-registry
docker rm private-registry</code></pre>
<h4 id="22-기본-인증-적용하여-실행">2.2. 기본 인증 적용하여 실행</h4>
<pre><code class="language-sh">docker run -d \
  -p 5000:5000 \
  --restart=always \
  --name private-registry \
  -v $(pwd)/certs:/certs \
  -v $(pwd)/auth:/auth \
  -e REGISTRY_AUTH=htpasswd \
  -e REGISTRY_AUTH_HTPASSWD_REALM=&quot;Registry Realm&quot; \
  -e REGISTRY_AUTH_HTPASSWD_PATH=/auth/htpasswd \
  registry:2</code></pre>
<h3 id="3-인증-확인">3. 인증 확인</h3>
<h4 id="31-로그인-시도">3.1. 로그인 시도</h4>
<pre><code class="language-sh">docker push localhost:5000/nginx</code></pre>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/565ff7ef-ca2d-47ce-9fe2-79ec405721ca/image.png" /></p>
<p>로그인 없이 이미지를 push하려 하면 위와 같이 실패한다.</p>
<h4 id="32-로그인-후-이미지-push-테스트">3.2. 로그인 후 이미지 push 테스트</h4>
<pre><code class="language-sh">docker login localhost:5000</code></pre>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/51a19e01-841b-4c7b-b498-26b596a7313a/image.png" /></p>
<p>로그인 후 정상적으로 이미지가 push 되는것을 확인할 수 있다.</p>