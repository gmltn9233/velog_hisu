<h1 id="계층-구조">계층 구조</h1>
<p><strong>TCP/IP 계층</strong>은 네 개의 계층을 가지고 있으며 OSI 7계층과 많이 비교한다.
<img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/3f4af3ab-ba01-4b70-92b6-2b17e902677e/image.png" /></p>
<p>위 그림 처럼 <strong>TCP/IP 계층</strong>과 달리 <strong>OSI 계층</strong>은 애플리케이션 계층을 세 개로 쪼개고, 링크 계층을 데이터 링크 계층, 물리 계층으로 표현하는 것이 다르며, 인터넷 계층을 네트워크 계층으로 부른다.</p>
<h2 id="osi-7계층이란">OSI 7계층이란?</h2>
<blockquote>
<p><strong>OSI 계층</strong>의 경우 국제 표준화 기구인 ISO에서 <strong>네트워크 통신을 체계적으로 정립한 표준 모델</strong>로 실제로 사용되진 않고 참조 모델로만 활용한다. <strong>실제 인터넷에서 사용되고 있는 게층은 TCP/IP 4계층이다.</strong></p>
</blockquote>
<h2 id="tcpip-4계층">TCP/IP 4계층</h2>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/ba4faa3c-e288-4111-92c5-0ba03278cd4d/image.png" /></p>
<ul>
<li>각 계층은 각기 맡은 역할을 수행하고, 계층 사이에는 정해진 정보만을 전달한다.</li>
<li>특정 계층이 변경되었을 때 다른 계층이 영향을 받지 않는다.</li>
</ul>
<hr />
<h2 id="애플리케이션-계층application-layer">애플리케이션 계층(Application Layer)</h2>
<p>FTP, HTTP, SSH, SMTP, DNS등 <strong>응용 프로그램이 사용되는 프로토콜 계층이며 웹 서비스, 이메일 등 서비스를 실질적으로 사람들에게 제공하는 층이다.</strong></p>
<h3 id="ftp">FTP</h3>
<p><strong>장치와 장치간의 파일을 전송</strong>하는데 사용되는 표준 통신 프로토콜</p>
<h3 id="ssh">SSH</h3>
<p>보안되지 않은 네트워크에서 네트워크 서비스를 안전하게 운영하기 위한 <strong>암호화 네트워크 프로토콜</strong></p>
<h3 id="http">HTTP</h3>
<p><strong>World Wide Web</strong>을 위한 데이터 통신의 기초이자 웹 사이트를 이용하는데 쓰는 프로토콜</p>
<h3 id="smtp">SMTP</h3>
<p><strong>전자 메일 전송</strong>을 위한 인터넷 표준 통신 프로토콜</p>
<h3 id="dns">DNS</h3>
<p><strong>도메인 이름과 IP 주소를 매핑</strong>해주는 서버</p>
<hr />
<h2 id="전송계층transport-layer">전송계층(Transport Layer)</h2>
<blockquote>
<p><strong>송신자와 수신자를 연결하는 통신 서비스</strong>를 제공하며 연결 지향 데이터 스트림 지원, 신뢰성, 흐름 제어를 제공할 수 있으며 <strong>애플리케이션과 인터넷 계층 사이의 데이터가 전달될 때 중계하는 역할</strong>, 대표적으로 TCP와 UDP가 있다.</p>
</blockquote>
<h3 id="tcp">TCP</h3>
<p><strong>패킷 사이의 순서를 보장</strong>하고 연결지향 프로토콜을 사용해서 연결을 하여 신뢰성을 구축해서 <strong>수신여부를 확인</strong>하며 <strong><code>'가상회선 패킷 교환 방식'</code></strong>을 사용</p>
<h3 id="udp">UDP</h3>
<p><strong>순서를 보장하지 않고 수신 여부를 확인하지 않으며</strong> 단순히 데이터를 주는 <strong><code>'데이터그램 패킷 교환 방식'</code></strong>을 사용</p>
<h3 id="가상회선-패킷-교환-방식">가상회선 패킷 교환 방식</h3>
<p>각 패킷에는 <strong>가상회선 식별자</strong>가 포함되며 모든 패킷을 전송하면 가상회선이 해제되고 패킷들은 전송된 <strong>'순서대로'</strong> 도착하는 방식
<img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/951df819-78d1-454f-a034-8327707d829e/image.png" /></p>
<h3 id="데이터그램-패킷-교환-방식">데이터그램 패킷 교환 방식</h3>
<p><strong>패킷이 독립적으로 이동</strong>하며 최적의 경로를 선택하여 가는데, 하나의 메시지에서 분할된 여러 패킷은 서로 다른 경로로 전송될 수 있으며 도착한 '<strong>순서가 다를 수 있는</strong>' 방식
<img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/4ec5732c-b04d-4105-8a1c-1626b6961765/image.png" /></p>
<h3 id="tcp-연결-성립-과정">TCP 연결 성립 과정</h3>
<p><strong>TCP</strong>는 신뢰성을 확보할 때 <strong><code>'3-way handshake'</code></strong>라는 작업을 진행한다.
<img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/695ad37f-60a3-4472-bd3f-fd5aa4ae6687/image.png" /></p>
<p>위 그림처럼 클라이언트가 서버와 통신할 때 다음과 같은 <strong>세 단계</strong>의 과정을 거친다.</p>
<blockquote>
<p><strong>1. SYN 단계:</strong> 클라이언트는 서버에 클라이언트의 <strong>ISN</strong>을 담아 SYN을 보낸다.
<strong>2. SYN + ACK 단계:</strong> 서버는 클라이언트의 SYN을 수신하고 <strong>서버의 ISN</strong>을 보내며 승인번호로 클라이언트의 ISN+1을 보낸다.
<strong>3. ACK 단계:</strong> 클라이언트는 서버의 ISN+1한 값인 승인 번호를 담아 <strong>ACK</strong>를 서버에 보낸다.</p>
</blockquote>
<blockquote>
<ul>
<li><strong>ICN이란?</strong> - 새로운 TCP 연결의 첫 번째 패킷에 할당된 임의의 시퀀스 번호를 의미한다.</li>
</ul>
</blockquote>
<ul>
<li><strong>SYN이란?</strong> - SYNchronization의 약자, 연결 요청 플래그</li>
<li><strong>ISN이란?</strong> - ACKnowledgement의 약자, 응답 플래그</li>
</ul>
<p>이렇게 <strong><code>3-way handshake</code></strong> 과정 이후 <strong>신뢰성</strong>이 구축되고 데이터 전송을 시작한다. <strong>UDP</strong>는 이 과정이 없기 때문에 신뢰성이 없는 계층이라고도 한다.</p>
<h3 id="tcp-연결-해제-과정">TCP 연결 해제 과정</h3>
<p><strong>TCP</strong>가 연결을 해제할 때는 <strong><code>4-way handshake</code></strong> 과정이 발생한다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/0b4fd163-946d-4e02-8b1a-2be6826d93a4/image.png" /></p>
<p><strong>1.</strong> 먼저 클라이언트가 연결을 닫으려고 할 때 <strong>FIN</strong>으로 설정된 세그먼트를 보낸다. 그리고 클라이언트는 <strong>FIN_WAIT_1</strong> 상태로 들어가고 서버의 응답을 기다린다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/0a02189c-7b8b-4e77-bec1-869eb8f1f43b/image.png" /></p>
<p><strong>2.</strong> 서버는 클라이언트로 <strong>ACK</strong> 승인 세그먼트를 보낸다. 그리고 <strong>CLOSE_WAIT</strong> 상태에 들어간다. 클라이언트가 세그먼트를 받으면 <strong>FIN_WAIT_2</strong> 상태에 들어간다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/18ed0180-ebcb-4266-9a3e-a8a3bf9d3d9c/image.png" /></p>
<p><strong>3.</strong> 서버는 ACK를 보내고 일정 시간 이후에 클라이언트에 <strong>FIN</strong>이라는 세그먼트를 보낸다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/00d89650-fa02-4032-ab65-2433dbe16c05/image.png" /></p>
<p><strong>4.</strong> 클라이언트는 <strong>TIME_WAIT</strong> 상태가 되고 다시 서버로 <strong>ACK</strong>를 보내서 서버는 CLOSED 상태가 된다. 이후 클라이언트는 어느 정도의 시간을 대기한 후 연결이 닫히고 클라이언트와 서버의 모든 자원의 연결이 해제된다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/207179c0-26eb-4ec3-929e-e78366549ceb/image.png" /></p>
<h3 id="time_wait">TIME_WAIT</h3>
<ul>
<li>왜 4번 과정에서 연결을 바로 닫지않고 <strong>TIME_WAIT</strong>으로 일정 시간 뒤에 닫을까?</li>
</ul>
<blockquote>
<p><strong>1. 지연 패킷에 발생할 경우를 대비:</strong> 패킷이 뒤늦게 도달하고 이를 처리하지 못한다면 <strong>데이터 무결성 문제가 발생</strong>한다.
<strong>2. 두 장치가 연결이 닫혔는지 확인:</strong> 만약 <strong>LAST_ACK</strong>상태에서 닫히게 되면 새로운 연결을 하려고 할 때 서버는 줄곧 <strong>LAST_ACK</strong>로 되어있기 때문에 접속 오류가 나타난다.</p>
</blockquote>
<blockquote>
<ul>
<li><strong>TIME_WAIT -</strong> 소켓이 바로 소멸되지 않고 일정 시간 유지되는 상태를 말한다.</li>
</ul>
</blockquote>
<ul>
<li><strong>데이터 무결성이란? -</strong> 데이터의 정확성과 일관성을 유지하고 보증하는 것</li>
</ul>
<hr />
<p>참고 및 출처: <a href="https://www.yes24.com/Product/Goods/109317449">면접을 위한 CS 전공지식 노트</a></p>