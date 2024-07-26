<h2 id="rdbms">RDBMS</h2>
<p><strong>R(Relational)+DBMS: 관계형 데이터 베이스 관리 시스템을 뜻한다.</strong>
관계를 나타내기 위해 <strong>외래 키(foreign key)</strong>를 사용하며 외래 키를 사용하여 <strong>테이블간 join</strong>이 가능하다.</p>
<ul>
<li><strong>ACID</strong> 특성을 제공한다.</li>
</ul>
<h4 id="acid">ACID</h4>
<blockquote>
<ul>
<li><strong>원자성(Atomicity)</strong><ul>
<li>트랜잭션 단위의 작업이 한번에 이루어짐(Do or Nothing)</li>
</ul>
</li>
</ul>
</blockquote>
<ul>
<li><strong>일관성(Consistency)</strong><ul>
<li>데이터가 미리 정의된 규칙에 의해서만 수정이 가능함</li>
</ul>
</li>
<li><strong>독립성(Isolation)</strong><ul>
<li>트랜잭션 수행 시, 다른 트랜잭션이 끼어들지 못함</li>
</ul>
</li>
<li><strong>지속성(Durability)</strong><ul>
<li>성공적으로 수행된 트랜잭션이 영원히 반영됨</li>
</ul>
</li>
</ul>
<h3 id="✔-장점">✔ 장점</h3>
<ul>
<li>정해진 스키마에 따라 데이터를 저장해야 해서 <strong>명확한 데이터 구조 보장 (데이터 일관성)</strong></li>
<li>각 데이터를 <strong>중복 없이</strong> 한 번만 저장 가능<strong>(데이터 무결성)</strong></li>
</ul>
<h3 id="✔-단점">✔ 단점</h3>
<ul>
<li>테이블 간 관계를 맺고 있기 때문에 테이블이 많아지면 복잡한 쿼리가 발생할 수 있음</li>
<li>스키마 변경이 유연하지 못함</li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/13b9be11-abfe-479f-bf2f-214c41d47586/image.png" /></p>
<h2 id="nosql-not-only-sql">NoSQL (Not Only SQL)</h2>
<ul>
<li>RDBMS의 성능과 한계를 극복하기 위해 등장</li>
<li>ACID 특성을 제공하지 않지만 <strong>뛰어난 확장성</strong>이나 성능 등의 특성을 가짐</li>
</ul>
<h3 id="nosql-종류">NoSQL 종류</h3>
<blockquote>
<h4 id="1-key-value-db">1. Key-value DB</h4>
</blockquote>
<ul>
<li><strong>Key-value</strong> 방식으로 데이터를 저장</li>
<li>key값은 모든 데이터 타입을 수용할 수 있고, <strong>중복되지 않는 유니크한 값</strong></li>
<li><strong>메모리 기반</strong>으로 빠르게 데이터를 읽어올 수 있음</li>
<li><em>EX)*</em> Redis, AWS DynamoDB, Riak 등</li>
</ul>
<hr />
<h4 id="2-document-db">2. Document DB</h4>
<ul>
<li><strong>비정형 대량 데이터</strong>를 저장하기 위한 방식</li>
<li>Key-value에서 확장된 방식으로, <strong>Key-Document 형태</strong>로 저장</li>
<li>Document는 <strong>계층적인 데이터 타입(JSON, XML)</strong>으로 저장됨</li>
<li><strong>JSON 타입을 사용하므로 HTTP 기반의 웹서버의 경우 데이터를 편리하게 주고받을 수 있음</strong></li>
<li><em>EX)*</em> MongoDB, Couch DB 등</li>
</ul>
<hr />
<h4 id="3-wide-column-db">3. Wide Column DB</h4>
<ul>
<li><strong>Row가 아닌 Column 위주</strong>로 데이터를 저장하는 방식</li>
<li>Key-value 와 유사한 형태의 <strong>Column-family Model</strong></li>
<li>데이터가 내부에서 <strong>Key를 기준으로 오름차순</strong> 정렬됨</li>
<li>이전의 모델들이 Key-value값을 이용해 필드를 결정했다면, 이 모델은 <strong>키에서 필드를 결정</strong></li>
<li><em>EX)*</em> HBase, Hypertable</li>
</ul>
<hr />
<h4 id="4-graph-db">4. Graph DB</h4>
<ul>
<li><strong>객체와 관계를 그래프 형식으로 데이터로 저장</strong></li>
<li>데이터를 <strong>Node와 Edge, Property</strong>와 함께 그래프 구조를 사용하여 데이터를 저장</li>
<li><strong>SNS, Network, Diagram 등과 SNS에서 함께 아는 친구 찾기, 추천 등 연관된 데이터를 추천해주는 엔진이나 패턴 기능에 사용</strong></li>
<li><em>EX)*</em> Neo4j</li>
</ul>
<h3 id="✔-장점-1">✔ 장점</h3>
<ul>
<li>스키마가 없어 <strong>유연하고 자유로운 데이터 구조</strong><ul>
<li>언제든 저장된 데이터 조정과 새로운 필드 추가 가능</li>
</ul>
</li>
<li><strong>데이터 분산</strong>이 용이하고, RDBMS에 비해 <strong>대용량의 데이터 저장 가능</strong></li>
</ul>
<h3 id="✔-단점-1">✔ 단점</h3>
<ul>
<li>스키마가 존재하지 않아 데이터의 <strong>일관성이 존재하지 않고 데이터 구조를 결정하기 어려움</strong></li>
<li><strong>데이터의 중복 발생 가능</strong></li>
</ul>