<h2 id="icmp란">ICMP란?</h2>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/581d67c7-d6cd-4c31-ad93-1d85c10334a9/image.png" /></p>
<blockquote>
<p><strong>ICMP(Internet Control Message Protocol):</strong> 인터넷 제어 메시지 프로토콜, 오류 메시지를 전송받는데 주로 쓰임</p>
</blockquote>
<h3 id="icmp-패킷-헤더-구조">ICMP 패킷 헤더 구조</h3>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/96afcdd0-055e-47ff-884e-9a579cf7f30e/image.png" /></p>
<p>ICMP Type: ICMP의 메시지를 구별
ICMP Code: 메시지 내용에 대한 추가 정보
ICMP Checksum: ICMP 값의 변조 여부를 확인
ICMP 메시지1,2: ICMP TYPE에 따라 내용이 가변적으로 들어가는 내용</p>
<p>결국 ICMP 는 TYPE에 따라 종류가 다양하다.
<img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/fe680e27-eafd-4a6a-870e-66ef17fa2033/image.png" /></p>
<h2 id="icmp-redirect-attack">ICMP Redirect Attack</h2>
<p><strong><code>ICMP Redirect</code></strong> 란 라우터가 더 효율적인 경로가 있을 때 경로를 재설정하기 위해 전송되는 <code>ICMP TYPE(5)</code>으로, <code>ICMP Redirect Attack</code>은 이 메시지를 악용한다.</p>
<p>공격자가 자기가 라우터인척 메시지를 보내서, <strong>모든 메시지를 자기에게 오게</strong>하고 정상적인 흐름처럼 보여준다.</p>
<h4 id="정상적인-icmp-redirect-패킷-흐름도">정상적인 ICMP Redirect 패킷 흐름도</h4>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/3ea70e77-85d6-42f1-b86b-040d21d4d2d0/image.png" /></p>
<p>위 처럼 더 효율적인 경로가 있을 경우, 자신이 더 효율적이라고 알린다.</p>
<h4 id="악의적인-icmp-redirect-attack">악의적인 ICMP Redirect Attack</h4>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/2aed18f7-4fac-4206-81ff-0ee058a93392/image.png" /></p>
<p>위처럼 <code>ATTACKER</code>가 자신이 라우터인 척, 더 효율적인 경로라고 자기를 이용하라고 보낸다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/42568b84-30f3-4b14-ab21-91ef8db1cb3e/image.png" /></p>
<p>그럼 PC1은 <code>ATTACKER</code>가 더 효율적인 라우터인 줄 알고, <code>ATTACKER</code>를 라우터로 사용하고, <code>ATTACKER</code>는 PC1과 Target 사이의 모든 패킷을 정상적인 것 처럼 주고 받고 하면서 모든 패킷을 볼 수 있게 된다.</p>
<h2 id="대응-방법">대응 방법</h2>
<p><code>ICMP Redirect</code> 설정인 <code>accept_redirects</code> 를 0으로 설정한다.</p>
<pre><code>sysctl -w net.ipv6.conf.all.accept_redirects=0</code></pre><p>대부분의 OS에서 보안상의 이유로 이미 설정이 되어있다!</p>