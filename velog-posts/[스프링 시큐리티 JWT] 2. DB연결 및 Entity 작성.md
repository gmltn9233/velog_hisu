<h2 id="데이터베이스-종류와-orm">데이터베이스 종류와 ORM</h2>
<p>회원 정보를 저장하기 위한 데이터베이스는 MySQL 엔진의 데이터베이스를 사용한다. 그리고 접근은 Spring Data JPA를 사용한다.</p>
<hr />
<h2 id="데이터베이스-의존성-주석-해제">데이터베이스 의존성 주석 해제</h2>
<p>이전 강의에서 진행했던 build.gradle의 Spring Data JPA 및 MySQL Driver 의존성 주석을 해제한다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/0ef305dc-be8b-47fb-ae22-d313166fac65/image.png" /></p>
<hr />
<h2 id="변수-설정">변수 설정</h2>
<ul>
<li>DB 연결 설정 : application.properteis</li>
</ul>
<pre><code>spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.url=jdbc:mysql://아이피:3306/데이터베이스?useSSL=false&amp;useUnicode=true&amp;serverTimezone=Asia/Seoul&amp;allowPublicKeyRetrieval=true
spring.datasource.username=아이디
spring.datasource.password=비밀번호</code></pre><ul>
<li>Hibernate ddl 설정 : application.properites</li>
</ul>
<pre><code>spring.jpa.hibernate.ddl-auto=none
spring.jpa.hibernate.naming.physical-strategy=org.hibernate.boot.model.naming.PhysicalNamingStrategyStandardImpl</code></pre><hr />
<h2 id="회원-테이블-entity-작성--userentity">회원 테이블 Entity 작성 : UserEntity</h2>
<ul>
<li><p>UserEntity</p>
<pre><code class="language-java">@Entity
@Setter
@Getter
public class UserEntity {

  @Id
  @GeneratedValue(strategy = GenerationType.IDENTITY)
  private int id;

  private String username;
  private String password;

  private String role;
}</code></pre>
</li>
</ul>
<hr />
<h2 id="회원-테이블-repositorty-작성--userrepository">회원 테이블 Repositorty 작성 : UserRepository</h2>
<ul>
<li>UserRepository<pre><code class="language-java">public interface UserRepository extends JpaRepository&lt;UserEntity, Integer&gt; {
</code></pre>
</li>
</ul>
<p>}</p>
<p>```</p>
<hr />
<h2 id="ddl-auto--create-설정-후-실행">ddl-auto = create 설정 후 실행</h2>
<p>데이터베이스에서 회원 정보를 저장할 테이블을 생성해야 하지만 ddl-auto 설정을 통해 스프링 부트 Entity 클래스 기반으로 테이블을 생성할 수 있다.</p>
<blockquote>
<p>주의 : create 로 테이블을 생성한 후 다시 none 으로 변경해주어야지 데이터 삭제를 막을 수 있다.</p>
</blockquote>
<p>출처: <a href="https://www.youtube.com/watch?v=JFTpzy7gsg0">개발자 유미</a></p>