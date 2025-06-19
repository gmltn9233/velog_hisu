<h2 id="✏️현-상황">✏️현 상황</h2>
<p>클라우드 환경에서 내부망에 접근하기 위해 <strong><code>openVPN</code></strong>을 이용하고 있었다.
<img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/63b0aec9-4f0a-4471-b064-3d849d67904b/image.png" /></p>
<p>하지만 기존 설치된 버전은 상업용 버전인 <code>OpenVPN Access Server</code> 으로, 무료 버전에서는 <strong>동시 접속이 2명까지만 지원</strong>되었다.</p>
<p>현재 내부망에 위치하는 <code>Grafana</code>에서 로그를 확인하며 디버깅을 진행하고 있었기에, BE, AI 개발팀 4명이 <strong>동시에 로그를 확인할 수 없는 문제가 발생</strong>하였고, 동시 접속자 제한이 없는 오픈소스 에디션인 <strong><code>OpenVPN Community Edition</code></strong> 으로 전환하게 되었다.</p>
<h3 id="🔍비교">🔍비교</h3>
<table>
<thead>
<tr>
<th>항목</th>
<th>Community Edition (CE)</th>
<th>Access Server (AS)</th>
</tr>
</thead>
<tbody><tr>
<td><strong>라이선스</strong></td>
<td>GPLv2 (오픈소스, 무료)</td>
<td>상용 라이선스 (2명까지 무료, 이후 유료)</td>
</tr>
<tr>
<td><strong>설치 방식</strong></td>
<td>직접 구성 (CLI 중심)</td>
<td>자동 설치 스크립트 및 UI 기반 설정 지원</td>
</tr>
<tr>
<td><strong>관리 인터페이스</strong></td>
<td>없음 (CLI, 수동 설정)</td>
<td>웹 기반 Admin UI 제공</td>
</tr>
<tr>
<td><strong>클라이언트 구성 배포</strong></td>
<td>.ovpn 파일 수동 배포</td>
<td>웹 포털 통해 자동 다운로드 가능</td>
</tr>
<tr>
<td><strong>업데이트 및 패치</strong></td>
<td>수동으로 직접 관리</td>
<td>자동 업데이트 지원</td>
</tr>
</tbody></table>
<h2 id="🛠️설치">🛠️설치</h2>
<h3 id="🖥️-서버-환경">🖥️ 서버 환경</h3>
<ul>
<li>Ubuntu 20.04 LTS (GCP VM 또는 일반 리눅스 서버)</li>
<li>OpenVPN Community Edition</li>
</ul>
<h3 id="1-패키지-설치">1. 패키지 설치</h3>
<pre><code class="language-bash">sudo apt-get update
sudo apt-get install -y openvpn easy-rsa</code></pre>
<h3 id="2-pki-및-인증서-발급">2. PKI 및 인증서 발급</h3>
<pre><code class="language-bash"># 작업 디렉토리 생성
make-cadir ~/openvpn-ca
cd ~/openvpn-ca

# PKI 디렉터리 초기화
./easyrsa init-pki

# CA 생성
./easyrsa build-ca nopass

# 서버 키·CSR 생성 및 서명
./easyrsa gen-req server nopass
./easyrsa sign-req server server

# 클라이언트 키·CSR 생성 및 서명
./easyrsa gen-req client1 nopass
./easyrsa sign-req client client1

# DH 및 CRL 생성
./easyrsa gen-dh
./easyrsa gen-crl

# 생성된 파일들을 복사
sudo cp pki/ca.crt /etc/openvpn/
sudo cp pki/issued/server.crt /etc/openvpn/
sudo cp pki/private/server.key /etc/openvpn/
sudo cp pki/dh.pem /etc/openvpn/
sudo cp pki/crl.pem /etc/openvpn/</code></pre>
<h3 id="3-tls-crypr-v2-키-생성">3. TLS-Crypr-v2 키 생성</h3>
<pre><code class="language-bash">cd /etc/openvpn
# 서버용 키 생성
sudo openvpn --genkey tls-crypt-v2-server tls-crypt-v2-server.key

# 클라이언트용 래핑 키 생성
sudo openvpn \
  --tls-crypt-v2 tls-crypt-v2-server.key \
  --genkey tls-crypt-v2-client tls-crypt-v2-client.key

sudo chown root:root tls-crypt-v2-*.key
sudo chmod 600    tls-crypt-v2-*.key</code></pre>
<h3 id="4-서버-설정etcopenvpnserverconf">4. 서버 설정(/etc/openvpn/server.conf)</h3>
<pre><code class="language-bash">port 1194
proto udp
dev tun

# 인증서와 키 파일 경로
ca /etc/openvpn/ca.crt
cert /etc/openvpn/server.crt
key /etc/openvpn/server.key
dh /etc/openvpn/dh.pem

# 제어 채널 암호화 (TLS-Crypt v2)
tls-crypt-v2 /etc/openvpn/tls-crypt-v2-server.key
cipher AES-256-CBC

# vpn 네트워크 설정
server 172.27.224.0 255.255.240.0
ifconfig-pool-persist ipp.txt

# 사설망 라우팅
push &quot;route 10.30.1.3 255.255.255.255&quot;
push &quot;route 10.10.1.0 255.255.255.0&quot;
push &quot;route 10.10.2.0 255.255.255.0&quot;
push &quot;route 10.20.10.0 255.255.255.0&quot;
push &quot;route 10.20.11.0 255.255.255.0&quot;
push &quot;route 10.20.20.0 255.255.255.0&quot;
push &quot;route 10.20.21.0 255.255.255.0&quot;
push &quot;route 10.30.2.0 255.255.255.0&quot;

# DNS
push &quot;dhcp-option DNS 8.8.8.8&quot;

# 연결 유지 및 사용자 권한
keepalive 10 120
persist-key
persist-tun

# 상태 및 로깅
status openvpn-status.log
# log-append /var/log/openvpn.log
verb 3
explicit-exit-notify 1

# 데이터 채널 암호화 설정
data-ciphers AES-256-GCM:AES-256-CBC
data-ciphers-fallback AES-256-CBC</code></pre>
<h3 id="5-클라이언트-프로파일-작성-키">5. 클라이언트 프로파일 작성 (키)</h3>
<pre><code class="language-bash">client
dev tun
proto udp
remote 34.64.32.144 1194
resolv-retry infinite
nobind
persist-key
persist-tun

remote-cert-tls server
auth SHA256
cipher AES-256-CBC
verb 3

# 서버만 지정한 사설망만 라우팅
data-ciphers           AES-256-GCM:AES-256-CBC
data-ciphers-fallback  AES-256-CBC

&lt;ca&gt;
# ~/client-configs/files/ca.crt
…CA 인증서…
&lt;/ca&gt;

&lt;cert&gt;
# ~/client-configs/files/client1.crt
…클라이언트 인증서…
&lt;/cert&gt;

&lt;key&gt;
# ~/client-configs/files/client1.key
…클라이언트 개인키…
&lt;/key&gt;

&lt;tls-crypt-v2&gt;
-----BEGIN OpenVPN tls-crypt-v2 client key-----
# /etc/openvpn/tls-crypt-v2-client.key 내용 전체
…생성된 client key…
-----END OpenVPN tls-crypt-v2 client key-----
&lt;/tls-crypt-v2&gt;</code></pre>
<h3 id="6-네트워크-세팅">6. 네트워크 세팅</h3>
<h4 id="61-ip-포워딩-설정">6.1 IP 포워딩 설정</h4>
<pre><code class="language-bash">sudo nano /etc/sysctl.conf

#net.ipv4.ip_forward=1  주석 제거 후 저장

# 적용
sudo sysctl -p</code></pre>
<h4 id="62-natmasquerade-설정">6.2 NAT(MASQUERADE) 설정</h4>
<pre><code class="language-bash"># 패키지 설치
sudo apt update
sudo apt install iptables -y

# 외부 네트워크 인터페이스 확인
ip a

# NAT 룰 추가
sudo iptables -t nat -A POSTROUTING -s 172.27.224.0/20 -o ens4 -j MASQUERADE

# iptables 세팅 영구 저장
sudo apt install iptables-persistent -y</code></pre>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/d087509c-463d-4239-b6a2-10ab1e0446cf/image.png" /></p>
<p><strong>ens4</strong>가 <code>10.30.1.4/32</code>(내부망)에 연결된 네트워크 인터페이스 임을 확인</p>
<p>vpn 사용자들은 tun0을 통해 들어오므로 <code>172.27.244.0/20</code> 대역을 연결해줌</p>
<h3 id="7-서버·클라이언트-동시-기동--연결">7. 서버·클라이언트 동시 기동 &amp; 연결</h3>
<pre><code class="language-bash"># 서버 재시작 (VM 내부)
sudo systemctl start openvpn@server</code></pre>
<pre><code class="language-bash"># 클라이언트 연결 (사용자 터미널)
sudo openvpn --config ~/cafeboo.ovpn --verb 4</code></pre>
<p>혹은 OpenVPN Connect 에서 연결</p>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/066f266c-dba8-4fdb-bb6b-40d6eaf00965/image.png" /></p>
<p>Done.</p>