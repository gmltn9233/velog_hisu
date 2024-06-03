<h2 id="transactional">@Transactional</h2>
<p><strong>Spring 2.0</strong> 이상의 버전에서 지원하는 <code>@Transactional</code>은 <strong>선언적 데이터베이스 트랜잭션 관리 방법</strong>을 제공한다. 메서드 레벨 또는 클래스 레벨에서 사용할 수 있으며 메서드 또는 모든 public 메서드에 트랜잭션을 적용한다.</p>
<p>해당하는 메서드를 실행할때 스프링은 트랜잭션을 시작하고, <strong>메서드가 정상적으로 종료되면 트랜잭션을 <code>commit</code>하고, 예외가 발생하면 트랜잭션을 <code>rollback</code></strong>한다. 즉, 비정상적 종료로 인한 rollback이 발생할 경우에는 트랜잭션의 일부 작업만 데이터베이스에 반영되는 것을 방지해 <strong>데이터 일관성을 유지</strong>할 수 있다.</p>
<p>아래 간단한 예시 코드처럼 기존의 긴 JDBC 트랜잭션을 짧은 코드를 Service 단 메서드 위에 어노테이션 처리한 두 번째 코드처럼 유지보수가 쉽게 관리할 수 있다.</p>
<ul>
<li>JDBC 트랜잭션<pre><code class="language-java">import java.sql.Connection;
</code></pre>
</li>
</ul>
<p>Connection conn = dataSource.getConnection();
Savepoint savepoint;</p>
<p>try (connection) {
    conn.setAutoCommit(false);
    Statement stmt = conn.createStatement();
    savepoint = conn.setSavepoint(&quot;savepoint&quot;);
    String sql = &quot;Insert into User values (16, 'Rosie', 'rosie@gmail.com')&quot;
    stmt.executeUpdate(sql);
    conn.commit(); 
} catch (SQLException e) {
    conn.rollback(savepoint); 
}</p>
<pre><code>* @Transactional 사용 시
```java
public class UserService{
    private final UserRepository userRepository;   

    @Transactional(readOnly = true)
    public List&lt;User&gt; findAll(User user) {
        return userRepository.save(user);
    }
}</code></pre><h3 id="transactional의-동작-방식">@Transactional의 동작 방식</h3>
<p>클래스, 메서드에 <code>@Transactional</code>이 추가되면, 이 클래스에 트랜잭션 기능이 적용된 <strong>프록시 객체</strong>가 생성된다. 이 프록시 객체는 @Transactional이 포함된 메소드가 호출 될 경우, <code>PlatformTransactinalManager</code>를 사용하여 트랜잭션을 시작하고, <strong>정상 여부에 따라 commit 또는 rollback 한다.</strong></p>
<h2 id="service단에서-transactional을-사용해야하는-이유">Service단에서 @Transactional을 사용해야하는 이유</h2>
<h3 id="1-비지니스-로직의-원자성보장">1. 비지니스 로직의 원자성보장</h3>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/d4b81be5-5c3b-488c-9df6-6dd0e6f519a5/image.png" /></p>
<p>위 <code>@Transactional</code>의 동작 방식을 보면 알 수 있듯이, 트랜잭션을 시작하고, 종료하는 부분은 모두 <strong>프록시</strong>에서 실행되기 때문에, 서비스 계층은 <strong>순수한 비즈니스 로직 코드만을 유지할 수 있다.</strong> </p>
<h3 id="2-트랜잭션의-경계-설정">2. 트랜잭션의 경계 설정</h3>
<p>트랜잭션 경계를 <strong>Service Layer</strong>에서 설정하면 <strong>하나의 비즈니스 작업 단위(즉, 하나의 서비스 메서드 호출)가 하나의 트랜잭션으로 처리된다.</strong> 이는 비즈니스 로직이 실패할 경우 전체 작업이 롤백되어 데이터의 일관성을 유지할 수 있게 합니다. 반면, <strong>Controller Layer</strong>는 주로 요청을 처리하고 응답을 반환하는 역할을 하므로 트랜잭션을 설정하는 적절한 위치가 아닙니다.</p>
<h3 id="3-repository-layer">3. Repository Layer?</h3>
<p><strong>Repository Layer</strong>는 단순히 데이터베이스와의 <code>CRUD</code> 작업을 수행합니다. 트랜잭션을 <strong>Repository Layer</strong>에 설정하면 단일 데이터 조작 작업마다 트랜잭션이 적용될 수 있지만, 이는 비즈니스 로직에서 필요한 <strong>트랜잭션의 일관된 처리</strong>와 맞지 않다. 또한, 여러 Repository 호출이 하나의 트랜잭션으로 묶여야 하는 경우에 대응할 수 없다.</p>
<h2 id="transactional-어노테이션-주의할-점">@Transactional 어노테이션 주의할 점</h2>
<h3 id="1-트랜잭션을-적용하려는-메서드는-반드시-public으로-선언되어야-한다">1. 트랜잭션을 적용하려는 메서드는 반드시 public으로 선언되어야 한다.</h3>
<p><strong>프록시 객체</strong>로 외부에서 접근 가능한 인터페이스를 제공해야 하기 때문입니다. 만약 해당 메서드가 <strong>private</strong>이나 <strong>protected</strong>로 선언되어 있다면, 프록시 객체가 생성될 때 해당 메서드에 접근할 수 없으므로 <code>@Transactional</code> 어노테이션을 사용한 트랜잭션 관리가 불가능하다.</p>
<p>하지만 <code>@Transactional</code> 애너테이션이 적용된 <strong>public</strong> 메서드에서 <strong>private</strong> 메서드를 호출하면 해당 private 메서드에도 Transaction이 적용되니 잘 알아두자!</p>
<pre><code class="language-java">@Service
public class MyService {

    @Transactional
    public void publicMethod() {
        // 트랜잭션이 시작됨
        privateMethod();
    }

    private void privateMethod() {
        // 트랜잭션이 적용된 컨텍스트 내에서 실행됨
    }
}</code></pre>
<h3 id="2-service-계층에서-사용하자">2. Service 계층에서 사용하자.</h3>
<p><strong>Service 계층</strong>에서 <code>@Transactional</code>을 사용하므로써 여러 데이터베이스 작업들을 원자적으로 처리할 수 있다. 그리고 Spring에서 <strong>단일 책임원칙</strong>에 따라 Database 계층에서 비즈니스 로직과 관련 없는 역할을 담당해 코드의 유지보수와 확장성을 높이기 위함이다.</p>
<h3 id="3-exception을-고려하자">3. Exception을 고려하자.</h3>
<p><strong>트랜잭션</strong>은 <code>RuntimeException</code>과 <code>Error</code>에서는 롤백되지만, <code>Checked exceptions</code>에서는 롤백되지 않는다. <strong>Checked exceptions</strong>는 예측가능한 에러를 말하는데, 아래와 같이 <code>@Transactional</code>에 <strong>rollbackFor</strong> 속성을 두어 롤백처리가 되도록 할 수도 있다.</p>
<pre><code class="language-java">@Transactional(rollbackFor={Exception.class})</code></pre>
<h2 id="참고자료">참고자료</h2>
<p><a href="https://medium.com/gdgsongdo/transactional-%EB%B0%94%EB%A5%B4%EA%B2%8C-%EC%95%8C%EA%B3%A0-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0-7b0105eb5ed6">Medium</a>
<a href="https://velog.io/@swjeong98/%EC%99%9C-Service-%EC%97%90-Transactional-%EC%9D%84-%EB%8B%AC%EC%95%84%EC%95%BC%ED%95%A0%EA%B9%8C">velog</a></p>