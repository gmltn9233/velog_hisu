<h2 id="jwt-발급과-검증">JWT 발급과 검증</h2>
<ul>
<li>로그인시 -&gt; 성공 -&gt; JWT 발급</li>
<li>접근시 -&gt; JWT 검증</li>
</ul>
<p>JWT에 관해 발급과 검증을 담당할 클래스가 필요하다. 따라서 <code>JWTUtil</code>이라는 클래스를 생성하여 <strong>JWT 발급, 검증 메소드</strong>를 작성해보자.</p>
<hr />
<h3 id="jwt-생성-원리">JWT 생성 원리</h3>
<p><code>JWT</code>는 JSON타입의 토큰으로 문자열 형태를 띄고있다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/962994f0-9a0d-4170-9c98-309d8a64014a/image.png" /></p>
<p><strong>Header</strong>.<strong>Payload</strong>.<strong>Signature</strong> 구조로 이루어져 있다.
각 요소는 다음 기능을 수행한다.</p>
<ul>
<li>Header<ul>
<li>JWT임을 명시</li>
<li>사용된 암호화 알고리즘</li>
</ul>
</li>
<li>Payload<ul>
<li>정보</li>
</ul>
</li>
<li>Signature<ul>
<li>암호화알고리즘((BASE64(Header))+(BASE64(Payload)) + 암호화키)</li>
</ul>
</li>
</ul>
<blockquote>
<ul>
<li>JWT의 특징은 내부 정보를 단순 BASE64 방식으로 인코딩하기 때문에 외부에서 쉽게 디코딩 할 수 있다. </li>
</ul>
</blockquote>
<ul>
<li>외부에서 열람해도 되는 정보를 담아야하며, 토큰 자체의 발급처를 확인하기 위해서 사용한다. (비밀번호같은 중요 정보 금지)
(지폐와 같이 외부에서 그 금액을 확인하고 금방 외형을 따라서 만들 수 있지만 발급처에 대한 보장 및 검증은 확실하게 해야하는 경우에 사용한다. 따라서 토큰 내부에 비밀번호와 같은 값 입력 금지)</li>
</ul>
<hr />
<h3 id="jwt-암호화-방식">JWT 암호화 방식</h3>
<ul>
<li>암호화 종류<ul>
<li>양방향<ul>
<li>대칭키 - 이 프로젝트는 양방향 대칭키 방식을 사용한다 :HS256</li>
<li>비대칭키</li>
</ul>
</li>
<li>단방향</li>
</ul>
</li>
</ul>
<hr />
<h3 id="암호화-키-저장">암호화 키 저장</h3>
<p>암호화 키는 하드코딩 방식으로 구현 내부에 탑재하는 것을 지양하기 때문에 변수 설정 파일에 저장한다.</p>
<ul>
<li>application.properties<pre><code>spring.jwt.secret=vmfhaltmskdlstkfkdgodyroqkfwkdbalroqkfwkdbalaaaaaaaaaaaaaaaabbbbb</code></pre></li>
</ul>
<hr />
<h3 id="jwtutil">JWTUtil</h3>
<ul>
<li><p>토큰 Payload에 저장될 정보</p>
<ul>
<li>username</li>
<li>role</li>
<li>생성일</li>
<li>만료일</li>
</ul>
</li>
<li><p>JWTUtil 구현 메소드</p>
<ul>
<li>JWTUtil 생성자</li>
<li>username 확인 메소드</li>
<li>role 확인 메소드</li>
<li>만료일 확인 메소드</li>
</ul>
</li>
<li><p>JWTUtil : 0.12.3</p>
</li>
</ul>
<pre><code class="language-java">@Component
public class JWTUtil {

    private SecretKey secretKey;

    public JWTUtil(@Value(&quot;${spring.jwt.secret}&quot;)String secret) {


        secretKey = new SecretKeySpec(secret.getBytes(StandardCharsets.UTF_8), Jwts.SIG.HS256.key().build().getAlgorithm());
    }

    public String getUsername(String token) {

        return Jwts.parser().verifyWith(secretKey).build().parseSignedClaims(token).getPayload().get(&quot;username&quot;, String.class);
    }

    public String getRole(String token) {

        return Jwts.parser().verifyWith(secretKey).build().parseSignedClaims(token).getPayload().get(&quot;role&quot;, String.class);
    }

    public Boolean isExpired(String token) {

        return Jwts.parser().verifyWith(secretKey).build().parseSignedClaims(token).getPayload().getExpiration().before(new Date());
    }

    public String createJwt(String username, String role, Long expiredMs) {

        return Jwts.builder()
                .claim(&quot;username&quot;, username)
                .claim(&quot;role&quot;, role)
                .issuedAt(new Date(System.currentTimeMillis()))
                .expiration(new Date(System.currentTimeMillis() + expiredMs))
                .signWith(secretKey)
                .compact();
    }
}</code></pre>
<h2 id="로그인-성공시-jwt-발급">로그인 성공시 JWT 발급</h2>
<h3 id="jwtutil-주입">JWTUtil 주입</h3>
<ul>
<li>LoginFilter : JWTUtil 주입</li>
</ul>
<pre><code class="language-java">public class LoginFilter extends UsernamePasswordAuthenticationFilter {

    private final AuthenticationManager authenticationManager;
        //JWTUtil 주입
        private final JWTUtil jwtUtil;

    public LoginFilter(AuthenticationManager authenticationManager, JWTUtil jwtUtil) {

        this.authenticationManager = authenticationManager;
                this.jwtUtil = jwtUtil;
    }

    @Override
    public Authentication attemptAuthentication(HttpServletRequest request, HttpServletResponse response) throws AuthenticationException {

                //클라이언트 요청에서 username, password 추출
        String username = obtainUsername(request);
        String password = obtainPassword(request);

                //스프링 시큐리티에서 username과 password를 검증하기 위해서는 token에 담아야 함
        UsernamePasswordAuthenticationToken authToken = new UsernamePasswordAuthenticationToken(username, password, null);

                //token에 담은 검증을 위한 AuthenticationManager로 전달
        return authenticationManager.authenticate(authToken);
    }

        //로그인 성공시 실행하는 메소드 (여기서 JWT를 발급하면 됨)
    @Override
    protected void successfulAuthentication(HttpServletRequest request, HttpServletResponse response, FilterChain chain, Authentication authentication) {

    }

        //로그인 실패시 실행하는 메소드
    @Override
    protected void unsuccessfulAuthentication(HttpServletRequest request, HttpServletResponse response, AuthenticationException failed) {

    }
}</code></pre>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/f5917165-f682-454c-8ab6-d965428db399/image.png" /></p>
<p>생성자 방식으로 <strong>JWTUtil</strong>을 주입시키면 이렇게 오류가 발생하는데, 이는 <code>SecurityFilter</code>에서 생성자방식으로 인자를 초기화해주는데, JWTUtil 인자를 안넣어주었기에 오류가 발생한다.
<code>SecurityConfig</code> 클래스에서도 <strong>JWTUtil</strong>객체를 넣어주자</p>
<ul>
<li>SecurityConfig에서 Filter에 JWTUtil 주입</li>
</ul>
<pre><code class="language-java">@Configuration
@EnableWebSecurity
public class SecurityConfig {

    //AuthenticationManager가 인자로 받을 AuthenticationConfiguraion 객체 생성자 주입
    private final AuthenticationConfiguration authenticationConfiguration;

    //JWTUtil 주입
    private final JWTUtil jwtUtil;
    public SecurityConfig(AuthenticationConfiguration authenticationConfiguration,JWTUtil jwtUtil) {

        this.authenticationConfiguration = authenticationConfiguration;
        this.jwtUtil = jwtUtil;
    }

    @Bean
    public BCryptPasswordEncoder bCryptPasswordEncoder(){

        return new BCryptPasswordEncoder();
    }

    //AuthenticationManager Bean 등록
    @Bean
    public AuthenticationManager authenticationManager(AuthenticationConfiguration configuration) throws Exception {

        return configuration.getAuthenticationManager();
    }

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {

        //csrf disable
        http
            .csrf((auth) -&gt; auth.disable());

        //From 로그인 방식 disable
        http
            .formLogin((auth) -&gt; auth.disable());

        //http basic 인증 방식 disable
        http
            .httpBasic((auth) -&gt; auth.disable());

        //경로별 인가 작업
        http
            .authorizeHttpRequests((auth) -&gt; auth
                .requestMatchers(&quot;/login&quot;, &quot;/&quot;, &quot;/join&quot;).permitAll()
                .requestMatchers(&quot;/admin&quot;).hasRole(&quot;ADMIN&quot;)
                .anyRequest().authenticated());
        http
            .addFilterAt(new LoginFilter(authenticationManager(authenticationConfiguration),jwtUtil), UsernamePasswordAuthenticationFilter.class);

        //세션 설정
        http
            .sessionManagement((session) -&gt; session
                .sessionCreationPolicy(SessionCreationPolicy.STATELESS));

        return http.build();
    }
}</code></pre>
<hr />
<h3 id="loginfilter-로그인-성공-successfulauthentication-메소드-구현">LoginFilter 로그인 성공 successfulAuthentication 메소드 구현</h3>
<ul>
<li>LoginFilter</li>
</ul>
<pre><code class="language-java">public class LoginFilter extends UsernamePasswordAuthenticationFilter {


    @Override
    protected void successfulAuthentication(HttpServletRequest request, HttpServletResponse response, FilterChain chain, Authentication authentication) {

        CustomUserDetails customUserDetails = (CustomUserDetails) authentication.getPrincipal();

        //username 가져오기
        String username = customUserDetails.getUsername();

        //role 가져오기
        Collection&lt;? extends GrantedAuthority&gt; authorities = authentication.getAuthorities();
        Iterator&lt;? extends GrantedAuthority&gt; iterator = authorities.iterator();
        GrantedAuthority auth = iterator.next();

        String role = auth.getAuthority();

        String token = jwtUtil.createJwt(username,role, 60*60*10L);

        // &quot;Bearer &quot; 띄워쓰기 무조건 해주어야 함!
        response.addHeader(&quot;Authorization&quot;, &quot;Bearer &quot; + token);

    }
}</code></pre>
<p>HTTP 인증 방식은 RFC 7235 정의에 따라 아래 인증 헤더 형태를 가져야 한다.</p>
<pre><code>Authorization: 타입 인증토큰

//예시
Authorization: Bearer 인증토큰string</code></pre><hr />
<h3 id="loginfilter-로그인-실패-unsuccessfulauthentication-메소드-구현">LoginFilter 로그인 실패 unsuccessfulAuthentication 메소드 구현</h3>
<ul>
<li>LoginFilter</li>
</ul>
<pre><code class="language-java">public class LoginFilter extends UsernamePasswordAuthenticationFilter {

    private final AuthenticationManager authenticationManager;
    private final JWTUtil jwtUtil;

    public LoginFilter(AuthenticationManager authenticationManager, JWTUtil jwtUtil) {

        this.authenticationManager = authenticationManager;
        this.jwtUtil = jwtUtil;
    }

    @Override
    protected void unsuccessfulAuthentication(HttpServletRequest request, HttpServletResponse response, AuthenticationException failed) {

                //로그인 실패시 401 응답 코드 반환
        response.setStatus(401);
    }
}</code></pre>
<h3 id="테스트">테스트</h3>
<p>POSTMAN으로 로그인 요청을 보내보자.</p>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/6665eddc-e4c2-44ce-a68f-a5f08da11b00/image.png" /></p>
<p>Headers에 Authorization 이라는 key에 대한 JWT 값을 확인할 수 있다!</p>
<h2 id="jwt-검증-필터">JWT 검증 필터</h2>
<p>스프링 시큐리티 filter chain에 요청에 담긴 JWT를 검증하기 위한 커스텀 필터를 등록해야 한다.</p>
<p>해당 필터를 통해 요청 헤더 <strong>Authorization</strong> 키에 JWT가 존재하는 경우 JWT를 검증하고 강제로 <strong>SecurityContextHolder에 세션을 생성한다.</strong> (이 세션은 STATLESS 상태로 관리되기 때문에 해당 요청이 끝나면 소멸 된다.)</p>
<hr />
<h3 id="jwtfilter-구현">JWTFilter 구현</h3>
<ul>
<li><p>JWTFilter </p>
<pre><code class="language-java">public class JWTFilter extends OncePerRequestFilter {

  private final JWTUtil jwtUtil;

  public JWTFilter(JWTUtil jwtUtil) {

      this.jwtUtil = jwtUtil;
  }

</code></pre>
</li>
</ul>
<pre><code>@Override
protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain filterChain) throws ServletException, IOException {

            //request에서 Authorization 헤더를 찾음
    String authorization= request.getHeader(&quot;Authorization&quot;);

            //Authorization 헤더 검증
    if (authorization == null || !authorization.startsWith(&quot;Bearer &quot;)) {

        System.out.println(&quot;token null&quot;);
        filterChain.doFilter(request, response);

                    //조건이 해당되면 메소드 종료 (필수)
        return;
    }

    System.out.println(&quot;authorization now&quot;);
            //Bearer 부분 제거 후 순수 토큰만 획득
    String token = authorization.split(&quot; &quot;)[1];

            //토큰 소멸 시간 검증
    if (jwtUtil.isExpired(token)) {

        System.out.println(&quot;token expired&quot;);
        filterChain.doFilter(request, response);

                    //조건이 해당되면 메소드 종료 (필수)
        return;
    }

            //토큰에서 username과 role 획득
    String username = jwtUtil.getUsername(token);
    String role = jwtUtil.getRole(token);

            //userEntity를 생성하여 값 set
    UserEntity userEntity = new UserEntity();
    userEntity.setUsername(username);
    userEntity.setPassword(&quot;temppassword&quot;);
    userEntity.setRole(role);

            //UserDetails에 회원 정보 객체 담기
    CustomUserDetails customUserDetails = new CustomUserDetails(userEntity);

            //스프링 시큐리티 인증 토큰 생성
    Authentication authToken = new UsernamePasswordAuthenticationToken(customUserDetails, null, customUserDetails.getAuthorities());
            //세션에 사용자 등록
    SecurityContextHolder.getContext().setAuthentication(authToken);

    filterChain.doFilter(request, response);
}</code></pre><p>}</p>
<pre><code>
---

### SecurityConfig JWTFilter 등록

* SecurityConfig

```java
@Configuration
@EnableWebSecurity
public class SecurityConfig {

    private final AuthenticationConfiguration authenticationConfiguration;
    private final JWTUtil jwtUtil;

    public SecurityConfig(AuthenticationConfiguration authenticationConfiguration, JWTUtil jwtUtil) {

        this.authenticationConfiguration = authenticationConfiguration;
        this.jwtUtil = jwtUtil;
    }

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {


        http
                .csrf((auth) -&gt; auth.disable());

        http
                .formLogin((auth) -&gt; auth.disable());

        http
                .httpBasic((auth) -&gt; auth.disable());

        http
                .authorizeHttpRequests((auth) -&gt; auth
                        .requestMatchers(&quot;/login&quot;, &quot;/&quot;, &quot;/join&quot;).permitAll()
                        .anyRequest().authenticated());

                //JWTFilter 등록
        http
                .addFilterBefore(new JWTFilter(jwtUtil), LoginFilter.class);

        http
                .addFilterAt(new LoginFilter(authenticationManager(authenticationConfiguration), jwtUtil), UsernamePasswordAuthenticationFilter.class);

        http
                .sessionManagement((session) -&gt; session
                        .sessionCreationPolicy(SessionCreationPolicy.STATELESS));

        return http.build();
    }
}</code></pre><hr />
<h3 id="jwt-요청-인가-테스트">JWT 요청 인가 테스트</h3>
<p>요청 헤더에 JWT를 첨부하고 로그인이 권한이 필요한 페이지에 접근을 진행해보자.</p>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/0d9fc05d-ee58-4457-a4e4-f40f1cbeb1a0/image.png" /></p>
<p>인증이 필요한 admin 페이지에 잘 접근이 되는 모습을 확인할 수 있다!</p>
<hr />
<ul>
<li>출처: <a href="https://www.youtube.com/watch?v=obNHwsl0fXM&amp;list=PLJkjrxxiBSFCcOjy0AAVGNtIa08VLk1EJ&amp;index=9">개발자 유미</a></li>
</ul>