<h2 id="🎯현-상황">🎯현 상황</h2>
<h4 id="usersecurityservice">UserSecurityService</h4>
<pre><code class="language-java">@Transactional
@Service
public class UserSecurityService implements UserDetailsService {

    private final UserDao userDao;

    @Value(&quot;${admin1}&quot;)
    private Long admin1;

    @Value(&quot;${admin2}&quot;)
    private Long admin2;

    @Value(&quot;${admin3}&quot;)
    private Long admin3;

    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        try {
            // 학번을 확인하여 운영자이면 ADMIN, 아니면 USER 권한 부여
            UserDomain siteUser = validateUsername(username);
            List&lt;GrantedAuthority&gt; authorities = new ArrayList&lt;&gt;();
            if (checkAdmin(siteUser.getStudentId())) {
                authorities.add(new SimpleGrantedAuthority(UserRole.ADMIN.getValue()));
            } else {
                authorities.add(new SimpleGrantedAuthority(UserRole.USER.getValue()));
            }
            // 유저 반환
            return new User(Long.toString(siteUser.getStudentId()), siteUser.getPassword(),
                authorities);
        } catch (IllegalArgumentException | UsernameNotFoundException e) {
            throw new UsernameNotFoundException(e.getMessage(), e); // 발생한 예외를 적절히 처리
        }
    }
 }</code></pre>
<p><strong><code>UserSecurityService</code></strong>에서 로그인시 유저정보를 확인하여 인증정보를 부여해주는 코드이다.
<strong>ADMIN</strong> 권한을 학번에 따라 부여하기 때문에, 보안상 관리자 학번을 <code>application.properties</code>에 저장하여 사용중이다.</p>
<h2 id="🌧문제상황">🌧문제상황</h2>
<p>하지만 이를 테스트하기 위해 테스트코드를 작성하는 도중, <code>@Value</code> 어노테이션으로 값을 받으면, 정상적으로 받아지지 않고 <strong>NULL</strong> 값이 들어오는 문제가 발생했다.</p>
<pre><code class="language-java">@Transactional
@DisplayName(&quot;DB 테스트(로그인)&quot;)
public class UserSecurityServiceTest {

    @Value(&quot;${admin1}&quot;)
    private Long admin1;

   ...

    @Test
    @DisplayName(&quot;로그인 성공(관리자)&quot;)
    public void testLoadUserByUsernameSuccess1() {
        Long studentId = admin1;
        MajorsDomain major = MajorsDomain.builder().major(&quot;컴퓨터공학과&quot;).build();

        UserDomain mockUser = UserDomain.builder()
            .studentId(studentId).password(&quot;1234&quot;).email(&quot;1234@naver.com&quot;)
            .majorsDomain(major).name(&quot;test&quot;)
            .build();
        when(userDao.findByStudentId(studentId)).thenReturn(Optional.of(mockUser));
        //admin1 학번을 가진 Mock User 생성

        // When
        UserDetails userDetails = userSecurityService.loadUserByUsername(studentId.toString());

        // Then
        assertEquals(admin1.toString(), userDetails.getUsername());
        assertEquals(&quot;1234&quot;, userDetails.getPassword());
        assertEquals(1, userDetails.getAuthorities().size());  // 하나의 권한(롤)을 예상
        assertEquals(UserRole.ADMIN.getValue(),
            userDetails.getAuthorities().iterator().next().getAuthority());
    }
    ...
}</code></pre>
<p>NULL 에러 발생</p>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/020de3e5-8fbd-4fbe-b9dc-aeff9e3aedf9/image.png" /></p>
<h3 id="why">why?</h3>
<p><code>@Value</code>는 컴포넌트 스캔시 <code>@Componet</code> 아래에서 의존성을 주입받는데, 현재 테스트 단계에서는 <strong>통합 테스트</strong>가 필요없어서 <code>@SpringBootTest</code>어노테이션 없이 <strong>유닛 테스트</strong>로 테스트를 진행하고 있었기 때문에 <code>@Value</code>에 값을 넣어주지 못한 것이다.</p>
<h2 id="💡해결방법">💡해결방법</h2>
<h3 id="1-springboottest-사용">1. @SpringBootTest 사용</h3>
<p>가장 쉽고 빠른 방법이지만, 유닛 테스트를 사용하려했던 처음 목적에 맞지 않기 때문에, PASS</p>
<h3 id="2-reflection-사용">2. Reflection 사용</h3>
<p><code>Reflection</code>을 사용하면 서비스 클래스의 인스턴스가 생성된 후 private 멤버 필드의 값을 동적으로 할당할 수 있다. 주로 API 나 레거시 코드를 단위 테스트하는 특별한 경우에 사용된다.</p>
<pre><code class="language-java">@Before
public void setUp() {
    ReflectionTestUtils.setField(controllerUnderTest, 
    &quot;myProperty&quot;, 
    &quot;String you want to inject&quot;);
}</code></pre>
<p>하지만 <code>Reflection</code>은 단점도 강력하기에 사용시 굉장한 주의를 요구한다.</p>
<h4 id="단점">단점</h4>
<blockquote>
<ul>
<li><strong>1. 일반 메서드 호출보다 성능이 떨어진다.</strong>
리플렉션은 동적으로 클래스를 생성하기 때문에 <strong>JVM 컴파일러가 최적화 할 수 없다.</strong> 컴파일        시에는 타입이 정해지지 않았기 때문이다. 해당 클래스의 타입이 맞는지, 생성자가 존재하는    지 등    의 벨리데이션 과정을 런타임 시 처리해야하기 때문에 <strong>성능이 떨어진다.</strong></li>
</ul>
</blockquote>
<ul>
<li><strong>2. 컴파일 시 타입 체크가 불가능하다.</strong>
리플렉션은 런타임 시점에 클래스의 정보를 알게 되므로 컴파일 시점에 타입 체크가 불가능하다.</li>
<li><strong>3. 추상화를 파괴한다.</strong>
리플렉션을 사용하는 모든 클래스의 정보를 알 수 있다. 외부로 노출시키지 않기 위해 private 접근 제어자를 사용해도 접근할 수 있다. 즉 추상화가 파괴된다.</li>
</ul>
<h3 id="3-생성자를-통한-주입">3. 생성자를 통한 주입</h3>
<h4 id="변경-전">변경 전</h4>
<pre><code class="language-java">@RequireAgrsConstructor
public class AClass {

    @Value(&quot;${app.blabla.value.a}&quot;)
    private final Integer valueA;

    @Value(&quot;${app.blabla.value.b}&quot;)
    private final Integer valueB;

    @Value(&quot;${app.blabla.value.c}&quot;)
    private final Integer valueC;
}</code></pre>
<h4 id="변경-후">변경 후</h4>
<pre><code class="language-java">public class AClass {

    @Value(&quot;${app.blabla.value.a}&quot;)
    private final Integer valueA;

    @Value(&quot;${app.blabla.value.b}&quot;)
    private final Integer valueB;

    @Value(&quot;${app.blabla.value.c}&quot;)
    private final Integer valueC;

    public AClass(
                @Value(&quot;${app.blabla.value.a}&quot;) Integer valueA,
                @Value(&quot;${app.blabla.value.b}&quot;) Integer valueB,
                @Value(&quot;${app.blabla.value.c}&quot;) Integer valueC
    ){
        this.valueA = valueA;
        this.valueB = valueB;
        this.valueC = valueC;
    }
}</code></pre>
<h4 id="testcode">TestCode</h4>
<pre><code class="language-java">@Test
public void test(){
    AClass aclass = new AClass(1, 2, 3);
    //...
}</code></pre>
<p>이렇게 하면 깔끔하게 단위 테스트 코드를 작성할 수 있다.
하지만 한가지 의문이 들었다. 처음 <code>@Value</code> 를 쓴 목적이 값을 숨기기 위함이였는데, 위 방식들은 모두 값을 숨길수 없다... 다른 방법은 없을까?</p>
<p>참고 : <a href="https://tlatmsrud.tistory.com/112">https://tlatmsrud.tistory.com/112</a>
참고 : <a href="https://kkambi.tistory.com/210">https://kkambi.tistory.com/210</a></p>