<h2 id="✏️현-상황">✏️현 상황</h2>
<p>운영 DB를 GUI 기반 SQL 툴인 <code>DBeaver</code>를 이용하여 관리하려 하고있는 상황</p>
<p>현재 <strong>GCP</strong> 기반의 <code>CloudSQL</code>으로 DB를 운영중인데, <strong>CloudSQL이 내부망의 Private subnet에 위치</strong>했기 때문에, <strong>VPN을 통해 내부망에 접속하여 접근하려고 시도</strong>했다.</p>
<p>먼저 간단하게 인프라를 설명하자면 아래와 같다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/d6e12dc7-b1d9-4e91-9109-6716636e8aee/image.png" /></p>
<p><strong>Shared VPC</strong>에 <strong>VPN</strong>이 위치하고 <strong>Prod VPC</strong>와 <code>NCC(Network Connectivity Center)</code>로 연결되어 VPN을 통해 내부망 접속이 가능하다.</p>
<p>하지만 <strong><code>Cloud SQL</code></strong>은 일반 VM과 달리, VPC 피어링이나 NCC를 통한 VPC 연결로는 접근할 수 없고, <strong>인스턴스가 종속된 VPC(Prod) 안에서만 Private IP로 접근이 가능하다는 제약</strong>이 있었다.</p>
<p>그렇기에 VPN을 통해서 접근이 불가능한 상황에 직면했다.</p>
<h2 id="🛠️해결">🛠️해결</h2>
<p>해결책으로 <strong>Cloud SQL Auth Proxy</strong> 를 사용하였다.</p>
<blockquote>
<p>💡Cloud SQL Auth Proxy 란?
Cloud SQL 인스턴스에 대한 보안 연결을 단순화 하기 위해 GCP에서 제공하는 프록시 도구
직접 VPC 피어링을 구성하지 않고도 안전하게 DB에 접속할 수 있다.</p>
</blockquote>
<p><strong>Cloud SQL Auth Proxy</strong>는 Cloud SQL 내부 API와 직접 통신하므로, 프록시가 위치한 곳이 GCP 내에 있고, 권한이 있으면 내부망처럼 접속이 가능하다.</p>
<h3 id="🔍-문제">🔍 문제</h3>
<p>하지만 여전히 private ip만 할당했을 시, 여전히 Prod VPC에서만 접근이 가능하고, VPN이 위치한 Shared VPC에서는 접근할 수 없는 문제가 발생했다.</p>
<p>그렇다고 Cloud SQL에 Public ip를 부여하자니, Cloud SQL을 내부망에 둔 의미가 없어지니 고민이 들었다.</p>
<p>하지만 Public ip를 할당하고, 승인된 네트워크 항목을 비워두게 되면, GCP 내부 API 인 Cloud SQL Auth Proxy만 접근이 가능하게 된다. </p>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/9979a821-6ffb-46ec-b8ab-710540234feb/image.png" /></p>
<p>또한, 접근하는 openVPN VM에서의 포트 방화벽도 vpn의 네트워크 대역으로 제한해두어, vpn을 통해서만 접속이 가능하게 만들어, 사실상 프라이빗 ip와 동일하게 작동하도록 하였다.</p>
<h4 id="☁️-cloud-sql-접근-요청-흐름">☁️ Cloud SQL 접근 요청 흐름</h4>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/dfc348e5-010f-45cb-a072-2df7dbe2067c/image.png" /></p>
<ol>
<li>VPN 을 작동시킨 후 Cloud SQL 접속 요청</li>
<li>Client 의 요청이 VPN VM 을 거쳐서 내부 요청으로 전환</li>
<li>OpenVPN VM 에서 CloudSQL Auth Proxy를 통해 CloudSQL 연결</li>
</ol>
<h4 id="🤔-의문점">🤔 의문점</h4>
<p>처음에는 Openvpn 서버의 방화벽이 vpn의 네트워크 대역인지 판단하여 걸러주니까, 내부망의 vpn 서버에서 발급한 키를 사용하지 않더라도 vpn대역대와 동일한 vpn만 키면 public ip로 접속이 가능한거 아닌가? 하는 의문이 생겼다.</p>
<blockquote>
<p>ex) 172.xx.xx.xx 대역대(vpn) 허용, 외부인이 같은 대역대 설정으로 vpn을 작동하여 172.xx.xx.xx 대역으로 접근</p>
</blockquote>
<p>하지만 <strong>Cloud SQL Auth Proxy</strong>는 요청이 GCP 네트워크 내부에서 실행되었는지 확인하고, 내부망으로 부터 들어온 요청이 아니면 필터링하기에 내부망의 VPN 키로 접속해야 DB 접근 요청이 가능하다.</p>
<p>예시로 현재는 내부망에 위치한 <strong>OpenVPN VM</strong>을 통해서 <strong>Cloud SQL Auth Proxy</strong>로 요청을 보내기 때문에, Cloud SQL Auth Proxy는 신뢰하는 <strong>내부망인 OpenVPN VM을 거쳐서 오는것을 확인하고 연결을 허용</strong>한다.</p>
<p>하지만 대역대만 동일한 외부의 VPN 서버를 거쳐서 오는 경우, <strong>인프라 레벨에서 요청 경로를 식별하여 외부로 감지된다면 연결을 차단</strong>한다.</p>
<h2 id="✅-결론">✅ 결론</h2>
<ul>
<li><p><strong>Cloud SQL</strong>은 Private IP 접근 시, 같은 VPC 내에서만 허용된다는 제약이 있다.</p>
</li>
<li><p><strong>Cloud SQL Auth Proxy</strong>를 활용하면, GCP 내부의 다른 VM에서도 보안 연결을 통해 접근 가능하다.</p>
</li>
<li><p><strong>단순한 IP 대역이나 VPN 설정만으로는 Cloud SQL에 접근할 수 없으며,</strong> <strong>반드시 GCP 내부 리소스 + 인증된 서비스 계정 기반 프록시 요청이어야만 접근이 가능하다.</strong></p>
</li>
</ul>