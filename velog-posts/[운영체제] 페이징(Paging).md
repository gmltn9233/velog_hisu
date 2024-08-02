<h2 id="페이징paging-이란">페이징(Paging) 이란?</h2>
<blockquote>
<p><strong>프로세스를 논리 주소의 고정된 페이지(Page)라고 불리는 블록들로 분할하여 메모리에 적재하는 것</strong></p>
</blockquote>
<ul>
<li>프로세스를 연속적으로 배치하지 않고 분할하기 때문에 <strong>외부단편화</strong>가 발생하지 않음</li>
<li>각각의 페이지는 물리 메모리의 프레임과 맵핑됨</li>
<li>고정 분할 방식으로 각 페이지의 사이즈는 동일하다.</li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/8c15637d-b971-46ec-9e49-20565ea0d01d/image.png" /></p>
<h2 id="외부-단편화external-fragmentation란">외부 단편화(External Fragmentation)란?</h2>
<blockquote>
<p>총 메모리 공간을 계산했을 때 요청을 처리할만한 <strong>충분한 메모리가 있음에도 불구하고, 가능한 공간들이 연속적이지 않아 프로세스를 할당할 수 없는 문제</strong></p>
</blockquote>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/21071425-3706-4954-b98f-9b010c5f657f/image.png" /></p>
<p>위와 같이 Process4가 들어갈 수 있는 메모리가 있음에도 불구하고 할당되지 못하여 대기하게 되는 문제가 발생한다.</p>
<h2 id="내부-단편화internal-fregmentation란">내부 단편화(Internal Fregmentation)란?</h2>
<blockquote>
<p><strong>프로세스를 페이지로 분할하는 과정에서 남는 공간이 발생하는 것</strong></p>
</blockquote>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/9563a02b-33e8-486f-aef5-dc1e7fbabf74/image.png" /></p>
<p>하지만 <strong>외부 단편화</strong>와 비교했을때, 남는 공간이 얼마 되지 않기 때문에 외<strong>부 단편화보다 효율적이다.</strong></p>
<h2 id="page-크기와-논리적-주소">Page 크기와 논리적 주소</h2>
<p>페이지 매핑에 맞춰 CPU로부터 생성되는 논리적 주소는 <code>페이지 주소</code>와 <code>페이지 오프셋</code>으로 구성된다.</p>
<ul>
<li><strong>페이지 주소</strong><ul>
<li>페이지 순서에 해당하는 숫자 (page number)</li>
</ul>
</li>
<li><strong>페이지 오프셋</strong><ul>
<li>페이지 하나의 크기는 대게 4KB, 12bits 차지</li>
<li>4KB 기준으로 페이지 내부에서도 0~2의 12승까지의 번지값을 가지며 이를 offset이라 부른다.</li>
</ul>
</li>
</ul>
<p>운영체제가 32비트 프로세서이고, 페이지 크기가 4KB라면 한번에 CPU는 2의 20승 개수의 페이지 번호를 구분 가능하다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/083be655-bc1d-4cdc-b372-648e286067e6/image.png" /></p>
<h2 id="page---frame-매핑">Page -&gt; Frame 매핑</h2>
<h3 id="1-오프셋-offset">1. 오프셋 (Offset)</h3>
<p>먼저 <strong>'오프셋'</strong> 이란 개념을 이해해야 한다. <strong>'위치를 찾기 위해 더해주는 값'</strong> 정도로 이해하면 된다.
주소 공간을 0~31로 설정하고 페이지 크기를 4로 설정하자. 그러면 아래와 같이 8개의 페이지가 생성된다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/cca66029-bca5-41e5-9029-82f2be3b3802/image.png" /></p>
<p>여기서 모든 주소를 페이지로 표현하려면 어떻게 해야할까? 아래와 같이 나타낼 수 있다.</p>
<blockquote>
<p><strong>현재 주소 = (페이지Number * 페이지 크기) + offset</strong>
3 = (0<em>4) + 3 // 3번지 주소는 Page0의 3번째 인덱스
6 = (1</em>4) + 2 // 6번지 주소는 Page1의 2번째 인덱스
17 = (4*4) + 1 // 17번지 주소는 Page4의 1번째 인덱스</p>
</blockquote>
<p>위와 같이 <strong>주소</strong>는 <strong>'페이지 번호'</strong>에 <strong>'페이지 크기'</strong>를 곱해주고 <strong>offset</strong>을 더해주면 된다.</p>
<h3 id="2-페이지-테이블page-table">2. 페이지 테이블(Page Table)</h3>
<p>페이지 테이블에 저장되어 있는 값은 각 페이지의 '물리적 주소에서의 시작 주소값(base address)'이다. 즉, 페이지 테이블에서 자신의 base address를 찾고 이 값에 offset을 더해주면, 더해준 값이 물리적 주소가 되는 것이다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/9dbd9ec8-1bca-412b-b6fa-91e2c79d1537/image.png" /></p>
<p>논리적 주소 공간의 크기 = 2^m (byte)
페이지 크기 = 2^n (byte)
페이지 테이블의 크기 = 2^(m-n) (byte)</p>
<p>page number = m-n (bit)
page offset = n (bit)</p>
<p>예를 들어 16 bit CPU의 경우, 논리적 주소는 16비트로 표현된다. 만약에 페이지 크기를 4비트로 정하면, 페이지 개수는 12비트로 자연스럽게 구해진다. 그런데 페이지의 개수는 페이지 테이블이 가지는 값의 개수와 일치해야 하므로 page number를 page table index로 봐도 무방하다. </p>
<p>따라서 페이지와 페이지 테이블의 매핑 관계를 그림으로 정리하면 아래와 같다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/fb232ef2-9367-4ac7-b76e-7d43e8654747/image.png" /></p>
<p>여기서 base address에 페이지의 offset 값을 더할 수 있는 이유는 페이지의 크기와 프레임의 크기가 동일하기 때문이다.</p>
<h3 id="3-논리적-주소로-page-offset-구해보기">3. 논리적 주소로 page, offset 구해보기</h3>
<p>논리적 주소와 페이지 오프셋이 주어졌을때, 이 주소가 몇 번째 페이지의 몇번째 오프셋인지 구해보자.</p>
<blockquote>
<p><strong>16 bit CPU
Logical Address: 46027 = 1011001111001011(2)</strong></p>
</blockquote>
<p>1) page offset = 4bit
2) page offset = 8bit
3) page offset = 12bit</p>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/54869dd8-cd6d-43df-874c-8355fcf5d153/image.png" /></p>
<h3 id="4-페이징-예제">4. 페이징 예제</h3>
<p>아래와 같이 데이터가 실제로 저장되어 있는 예제이다. 어떤 과정으로 물리적 주소로 매핑되는지 살펴보자.</p>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/5749799e-6d17-430d-ba7e-e0db261595dd/image.png" /></p>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/7c390e38-78f6-4b45-9589-a2ccd3433763/image.png" /></p>