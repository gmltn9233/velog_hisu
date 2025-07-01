<h2 id="✏️-현-상황">✏️ 현 상황</h2>
<p>현재 <code>Grafana</code>를 통해 클라우드 환경의 각 서비스에서 발생하는 로그들을 확인하고 있다.</p>
<p>하지만 <code>Grafana</code>가 위치한 모니터링 서버가 내부망에 위치해있기에, <code>Grafana UI</code>에 접속하기 위해서는 <strong>VPN</strong>을 통해 접속해야하는 상황이다.
<img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/e3e3a5ab-a5dc-478c-b9a8-8f07514348e1/image.png" /></p>
<p>하지만 <code>Grafana</code>에서 간헐적으로 연결이 끊기며 데이터가 보이지 않는 증상이 발생했다. </p>
<h2 id="🐛-시도">🐛 시도</h2>
<h4 id="1-grafana-세션-유효-시간-연장">1. Grafana 세션 유효 시간 연장</h4>
<pre><code class="language-ini">login_maximum_inactive_lifetime_duration = 24h
login_maximum_lifetime_duration = 7d</code></pre>
<p><code>Grafana UI</code>는 사용자가 로그인하면 세션 쿠키가 브라우저에 저장되고, 요청마다 이 쿠키를 헤더에 실어 보내며 인증 상태를 유지한다. </p>
<p>이 세션이 빨리 만료되는것이 문제인가 싶어, 비활동 시 세션 유지 시간과, 세션 생성 후 유지 시간을 늘려보았지만, 같은 증상이 발생하였다. </p>
<h4 id="2-live-기능-비활성화">2. Live 기능 비활성화</h4>
<pre><code class="language-ini">[live]
enabled = false</code></pre>
<p><code>Grafana</code> 에선 <strong>WebSocket 기반의 실시간 데이터 스트림</strong>을 제공한다. </p>
<p>혹시 대시보드가 늘어나면서 웹 소켓 연결이 많아지면서 리소스가 할당되어 서버가 느려진건가 싶어 Live 기능을 비활성화 해보았지만, 같은 증상이 발생하였다. </p>
<h4 id="3-인증-쿠키의-보안-속성-조정">3. 인증 쿠키의 보안 속성 조정</h4>
<pre><code class="language-ini">cookie_secure = false</code></pre>
<p>해당 속성은 true(기본값)일 경우, 인증 쿠키는 <strong>HTTPS</strong> 연결에서만 브라우저가 서버로 전송한다. </p>
<p>하지만 지금 <strong>HTTP</strong> 연결로 <code>Grafana UI</code> 를 사용하고 있기에, 해당 속성을 false로 변경하였지만 동일한 증상이 발생하였다.</p>
<h2 id="🌧️원인">🌧️원인</h2>
<p>원인을 찾지 못하던 와중, 연결이 끊길때마다 <strong><code>VPN</code></strong>을 재접속하면 해당 증상이 사라지는 현상을 발견하였다. 그래서 원인을 VPN에서 찾아보았다.</p>
<h3 id="vpn-환경에서의-mtu">VPN 환경에서의 MTU</h3>
<p><strong>VPN 환경</strong>에서는 데이터를 암호화하고 터널링하기 위해 기존 IP 패킷 위에 새로운 헤더를 덧붙이기 때문에, 일반적인 네트워크보다 <strong>MTU(Most Transmission Unit)</strong>가 작다.</p>
<blockquote>
<p>💡<strong>MTU(Maximum Transmission Unit) 란?</strong>
네트워크를 통해 한번에 전송할 수 있는 최대 패킷 크기를 말한다. MTU를 초과하여 전송할 경우, <code>Fragmentation</code>이 일어난다.</p>
</blockquote>
<p>하지만 클라이언트와 서버간의 통신이 MTU 보다 더 큰 패킷을 주고 받으려 할 경우, 패킷이 분할되거나 손실되는 현상이 발생한다.</p>
<p>이 과정에서 브라우저의 세션 쿠키가 유실되어 서버에서는 <code>user token not found</code> 등의 인증 실패로 G<strong>rafana UI 연결이 끊어지는 현상이 발생</strong>했던 것이다.</p>
<h2 id="🛠️-해결">🛠️ 해결</h2>
<p>일반적인 <code>Ethernet</code> 네트워크의 기본 MTU는 보통 <strong>1500 bytes</strong>이지만,VPN은 헤더가 추가되므로 이보다 작은 값으로 명시해주어 MTU를 조정해주었다.</p>
<pre><code class="language-conf"># openvpn.conf

# VPN 터널의 최대 전송 단위를 1400 바이트로 제한
tun-mtu 1400

# TCP 연결의 MSS 값을 자동으로 조정
mssfix 1360</code></pre>
<blockquote>
<p>💡 <strong>MSS(Maximum Segment Size) 란?</strong>
TCP 세그먼트에서 데이터(payload)만을 의미하는 최대 크기
MSS = MTU - IP 헤더(20B) - TCP 헤더(20B)</p>
</blockquote>
<p>mssfix는 서버가 클라이언트에 전달하는 TCP SYN 패킷을 가로채어, MSS 값을 조정한다.</p>
<h2 id="✅-결론">✅ 결론</h2>
<p>VPN 환경에서는 <strong>MTU</strong> 문제로 인해 세션 쿠키가 유실되며 Grafana 인증 오류가 발생할 수 있다.</p>
<p><code>tun-mtu</code>와 <code>mssfix</code> 설정을 통해 패킷 크기를 명시적으로 제한함으로써 <strong>세션 쿠키 유실을 방지</strong>할 수 있다.</p>