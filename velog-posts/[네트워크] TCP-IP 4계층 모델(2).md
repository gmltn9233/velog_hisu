<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/db937019-7d91-4319-91c6-02bb579933fe/image.png" /></p>
<hr />
<h2 id="인터넷-계층">인터넷 계층</h2>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/3d700909-b4e5-4903-bc2a-d3f2034339a9/image.png" /></p>
<blockquote>
<ul>
<li>장치로 부터 받은 <strong>네트워크 패킷을 IP 주소로 지정된 목적지로 전송</strong>하기 위해 사용되는 계층</li>
</ul>
</blockquote>
<ul>
<li><strong>IP, ARP, ICMP</strong> 등이 있으며 패킷을 수신해야할 상대의 주소를 지정하여 데이터를 전달함</li>
<li>상대방이 제대로 받았는지에 대해 보장하지 않는 <strong>비연결형적인 특징</strong></li>
</ul>
<h3 id="ipinternet-protocol">IP(Internet Protocol)</h3>
<p><strong>데이터 패킷</strong>이 네트워크를 통해 <strong>올바른 대상에 도착할 수 있도록</strong> 데이터 패킷을 라우팅하고 주소를 지정하기 위한 프로토콜 또는 규칙의 집합</p>
<h3 id="arpaddress-resolution-protocol">ARP(Address Resolution Protocol)</h3>
<p>같은 네트워크 대역에서 통신을 하기 위해 필요한 <strong>MAC 주소</strong>를 IP 주소를 이용해서 알아오는 프로토콜</p>
<blockquote>
<p>IP 주소만 입력해도 <strong>ARP 프로토콜</strong>이 상대방의 MAC 주소를 알아오고, 같은 네트워크 대역에서 MAC 주소를 이용해 통신을 하게 된다.</p>
</blockquote>
<ul>
<li><strong>MAC 주소란?</strong> - 컴퓨터나 노트북 등 각 장치에는 네트워크에 연결하기 위한 장치(LAN 카드)가 잇는데, 이를 구별하기 위한 <strong>식별번호</strong>를 말한다.</li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/327d1f01-f87f-4e13-96d7-852af5be2d6c/image.png" /></p>
<h3 id="icmpinternet-control-message-protocol">ICMP(Internet Control Message Protocol)</h3>
<p>IP 통신 에러 상황을 출발지로부터 전달하고 메시지를 제어하는 역할</p>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/8cba6be4-8a21-4422-9ac1-9d2124139842/image.png" /></p>
<ul>
<li><strong>Type:</strong> <code>ICMP</code> 메시지 종류</li>
<li><strong>Code:</strong> 메시지, Type 별 세부 코드 정보</li>
<li><strong>Checksum:</strong> <code>ICMP</code> 헤더 손상 여부 확인</li>
</ul>
<hr />
<h2 id="링크-계층">링크 계층</h2>
<p>전선, 광섬유, 무선 등으로 <strong>실질적으로 데이터를 전달하며 장치간에 신호를 주고받는 '규칙'을 정하는 계층,</strong> 네트워크 접근 계층이라고도 한다.</p>
<h3 id="전이중full-duplex-통신">전이중(full duplex) 통신</h3>
<p><strong>양쪽 장치가 동시에 송수신</strong>할 수 있는 방식을 말한다. 이는 송신로와 순시로로 나눠서 데이터를 주고 받으며 현대의 <strong>고속 이더넷</strong>은 이 방식을 기반으로 통신한다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/1e727435-1dc4-433f-a963-578e10e76fc2/image.png" /></p>
<h3 id="이더넷ethernet">이더넷(Ethernet)</h3>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/bf92a580-6a62-450a-a20f-564ac9815344/image.png" /></p>
<blockquote>
<p>컴퓨터 네트워크 기술의 하나로, 일반적으로 <strong>LAN, MAN 및 WAN</strong>에서 가장 많이 활용되는 기술 규격이다. <strong>CSMA/CD</strong> 기법을 채택한다.</p>
</blockquote>
<ul>
<li><strong>장점</strong><ul>
<li>설치 비용이 저렴하고 관리가 쉽다.</li>
<li>네트워크 구조가 간단하다.</li>
</ul>
</li>
<li><strong>단점</strong><ul>
<li>다량의 노드 존재 시 신호 때문에 충돌이 발생한다.</li>
<li>충돌이 많아지면 지연이 커진다.</li>
</ul>
</li>
</ul>
<h4 id="cmsacd">CMSA/CD</h4>
<p>데이터를 <strong>'보낸 이후'</strong> 충돌이 발생한다면 일정 시간 이후 재전송하는 방식으로 데이터를 보낼 때 충돌에 대비함</p>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/80efd9c0-852d-42bf-a566-f8b7cf626680/image.png" /></p>
<h3 id="반이중half-duplex-통신">반이중(half duplex) 통신</h3>
<p>양쪽 장치는 서로 통신할 수 있지만, 동시에는 통신할 수 없으며 <strong>한번에 한 방향만 통신할 수 있는 방식</strong></p>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/500be206-d8c9-47b9-bb97-5f13cce31937/image.png" /></p>
<hr />
<h2 id="계층-간-데이터-송수신-과정">계층 간 데이터 송수신 과정</h2>
<p>컴퓨터를 통해 다른 컴퓨터로 데이터를 요청한다면 어떠한 일이 일어날까?</p>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/77d46563-d4ee-4bfc-ba82-37ecc1ed1687/image.png" /></p>
<ol>
<li><code>애플리케이션 계층</code>에서 <code>전송 계층</code>으로 요청(request) 값들이 <strong>캡슐화 과정</strong>을 거쳐 전달된다.</li>
<li><code>링크 계층</code>을 통해 해당 <strong>서버와 통신</strong>을 한다.</li>
<li>해당 서버의 <code>링크 계층</code>으로부터 <code>애플리케이션 계층</code>까지 <strong>역캡슐화 과정</strong>을 거쳐 데이터가 전송된다.</li>
</ol>
<h3 id="캡슐화-과정">캡슐화 과정</h3>
<p>상위 계층의 헤더와 데이터를 하위 계층의 데이터 부분에 포함시키고 <strong>해당 계층의 헤더를 삽입</strong>하는 과정</p>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/1a47da5e-0b58-4ce2-a79a-dce2311f7e96/image.png" /></p>
<ul>
<li><code>애플리케이션 계층</code> -&gt; <code>전송계층</code>: <strong>세그먼트</strong></li>
<li><code>전송계층</code> -&gt; <code>인터넷 계층</code>: <strong>패킷</strong></li>
<li><code>인터넷 계층</code> -&gt; <code>링크 계층</code>: <strong>프레임</strong></li>
</ul>
<h3 id="역캡슐화-과정">역캡슐화 과정</h3>
<p>하위 계층에서 상위 계층으로 가며 <strong>각 계층의 헤더 부분을 제거하는 과정</strong></p>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/dede11b9-ec0b-42c2-9958-864576d9deac/image.png" /></p>
<hr />
<p>참고 및 출처: <a href="https://www.yes24.com/Product/Goods/109317449">면접을 위한 CS 전공지식 노트</a></p>