<h2 id="registry-보안-설정-tslssl-인증-적용">Registry 보안 설정 (TSL/SSL 인증 적용)</h2>
<h3 id="💡들어가기-앞서">💡들어가기 앞서</h3>
<h4 id="🔍ssl이란">🔍SSL이란?</h4>
<p><strong>SSL(Secure Scokets Layer)</strong>은 암호화 기반 인터넷 보안 프로토콜이다. 전달되는 모든 데이터를 암호화하고 특정한 유형의 사이버 공격도 차단한다. SSL은 TLS(Transport Layer Security) 암호화의 전신이기도 한다.</p>
<p>SSl/TLS 를 사용하는 웹사이트 URL은 HTTP 대신 HTTPS가 사용된다.
<img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/3bd20017-906b-4ac1-b233-0b8106e6a1b6/image.png" /></p>
<h4 id="🔍tsl이란">🔍TSL이란?</h4>
<p>TLS 는 SSL의 업데이트 버전으로 SSL의 최종버전인 3.0과 TLS의 최초버전의 차이는 크지않으며, 이름이 바뀐것은 SSL을 개발한 Netscape가 업데이트에 참여하지 않게 되어 소유권 변경을 위해서였다고 한다.
결과적으로 TLS는 SSL의 업데이트 버전이며 명칭만 다르다고 볼 수 있다.</p>
<h4 id="ssltls의-작동-방식">SSL/TLS의 작동 방식</h4>
<p>SSL은 개인정보 보호를 제공하기 위해, 웹에서 전송되는 데이터를 암호화 한다. 따라서, 데이터를 가로채려해도 거의 복호화가 불가능하다.
SSL은 클라이언트와 서버간에 핸드셰이크를 통해 인증이 이루어진다. 또한 데이터 무결성을 위해 데이터에 디지털 서명을 하여 데이터가 의도적으로 도착하기 전에 조작된 여부를 확인한다.</p>
<h3 id="1-자체-서명된-ssl-인증서-생성">1. 자체 서명된 SSL 인증서 생성</h3>
<h4 id="11-인증서-폴더-생성">1.1. 인증서 폴더 생성</h4>
<pre><code class="language-sh">mkdir -p certs</code></pre>
<h4 id="12-openssl을-사용하여-키-및-인증서-생성">1.2. OpenSSL을 사용하여 키 및 인증서 생성</h4>
<pre><code class="language-sh">openssl req -newkey rsa:4096 -nodes -sha256 -keyout certs/domain.key -x509 -days 365 -out certs/domain.crt</code></pre>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/12e6f653-9225-4c44-8c4a-fb0f48a25509/image.png" /></p>
<ul>
<li>Common Name (CN) 입력시 loacalhost 입력 (나머지는 공백(엔터) 입력하면 넘어감)</li>
<li>-days 365: 인증서 유효기간 1년</li>
<li>-keyout: 개인 키 저장</li>
<li>-out: 인증서 저장</li>
</ul>
<h3 id="2-인증서를-registry에-적용하여-https-활성화">2. 인증서를 Registry에 적용하여 HTTPS 활성화</h3>
<h4 id="21-기존-registry-컨테이너-정지-및-삭제">2.1. 기존 Registry 컨테이너 정지 및 삭제</h4>
<pre><code class="language-sh">docker stop private-registry
docker rm private-registry</code></pre>
<h4 id="22-tls-적용하여-registry-재실행">2.2. TLS 적용하여 Registry 재실행</h4>
<pre><code class="language-sh">docker run -d \
  -p 5000:5000 \
  --restart=always \
  --name private-registry \
  -v $(pwd)/certs:/certs \
  -e REGISTRY_HTTP_ADDR=0.0.0.0:5000 \
  -e REGISTRY_HTTP_TLS_CERTIFICATE=/certs/domain.crt \
  -e REGISTRY_HTTP_TLS_KEY=/certs/domain.key \
  registry:2</code></pre>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/14dc9f00-055b-4cbb-974e-7737e214f7b9/image.png" /></p>
<h4 id="23-tls-적용-여부-확인">2.3. TLS 적용 여부 확인</h4>
<pre><code class="language-sh">curl -k https://localhost:5000/v2/_catalog</code></pre>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/7d27d9d8-61fa-48b1-aa35-a69a8db40ad2/image.png" /></p>
<h3 id="참고-자료">참고 자료</h3>
<p><a href="https://f-lab.kr/insight/understanding-https-ssl-tls-20240722?gad_source=1&amp;gclid=CjwKCAiAtsa9BhAKEiwAUZAszYcBEUIOShIEkTmpdxO170I-w2OkbUF6yx-AuFmeneJPypKaR0iCBxoCVK4QAvD_BwE">https://f-lab.kr/insight/understanding-https-ssl-tls-20240722?gad_source=1&amp;gclid=CjwKCAiAtsa9BhAKEiwAUZAszYcBEUIOShIEkTmpdxO170I-w2OkbUF6yx-AuFmeneJPypKaR0iCBxoCVK4QAvD_BwE</a></p>
<p><a href="https://kanoos-stu.tistory.com/46">https://kanoos-stu.tistory.com/46</a></p>