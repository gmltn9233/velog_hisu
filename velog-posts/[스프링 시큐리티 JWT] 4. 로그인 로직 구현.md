<h2 id="로그인-모식도">로그인 모식도</h2>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/d04e7054-3850-445a-8e67-d003d0794888/image.png" /></p>
<p>JWT 방식을 위해 form 로그인 방식을 disable 시켰기 때문에 Authentication Manager 를 직접 구현해주어야한다. </p>
<hr />
<h2 id="스프링-시큐리티-필터-동작-원리">스프링 시큐리티 필터 동작 원리</h2>
<p>스프링 시큐리티는 클라이언트의 요청이 여러개의 필터를 거쳐 <code>DispatcherServlet(Controller)</code>으로 향하는 중간 필터에서 요청을 가로챈 후 검증(인증/인가)을 진행한다.</p>
<ul>
<li><p>클라리언트 요청 -&gt; 서블릿 필터 -&gt; 서블릿(컨트롤러)
<img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/4c742aca-b83e-4aa5-8b18-b850d4b3a289/image.png" /></p>
</li>
<li><p>Delegaging Filter Proxy</p>
</li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/99a44f38-5b6c-4f5f-a6e4-45b61846ec67/image.png" /></p>
<ul>
<li>서블릿 필터 체인의 <strong>DelegatingFilter</strong> -&gt; <strong>Security 필터 체인</strong> (내부 처리 후) -&gt; 서블릿 필터 체인의 <strong>DelegatingFilter</strong></li>
</ul>
<p>가로챈 요청은 <strong>SecurityFilterChain</strong>에서 처리 후 상황에 따른 거부, 리다이렉션, 서블릿으로 요청 전달을 진행한다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/43a92413-f2e2-419c-803f-6f1c8203ea69/image.png" /></p>
<ul>
<li><strong>SecurityFilterChain</strong>의 필터 목록과 순서</li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/30ef8d7a-6a76-463c-8d17-3970240ec222/image.png" /></p>
<hr />
<h2 id="form-로그인-방식에서-usernamepasswordauthenticationfilter">Form 로그인 방식에서 UsernamePasswordAuthenticationFilter</h2>
<p><code>Form</code> 로그인 방식에서는 클라이언트단이 <strong>username</strong>과 <strong>password</strong>를 전송한 뒤 Security 필터를 통과하는데 <code>UsernamePasswordAuthentication</code> 필터에서 회원 검증을 진행을 시작한다. </p>
<p>(회원 검증의 경우 <code>UsernamePasswordAuthenticationFilter</code>가 호출한 <strong>AuthenticationManager</strong>를 통해 진행하며 DB에서 조회한 데이터를 UserDetailsService를 통해 받음)</p>
<p>우리의 JWT 프로젝트는 SecurityConfig에서 <strong>formLogin 방식을 disable</strong> 했기 때문에 기본적으로 활성화 되어 있는 해당 필터는 동작하지 않는다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/e8819449-d871-49d4-a38c-784c6d7753ca/image.png" /></p>
<p>따라서 로그인을 진행하기 위해서 필터를 커스텀하여 등록해야 한다.</p>
<hr />
<h2 id="로그인-로직-구현-목표">로그인 로직 구현 목표</h2>
<ul>
<li>아이디, 비밀번호 검증을 위한 커스텀 필터 작성</li>
<li>DB에 저장되어 있는 회원 정보를 기반으로 검증할 로직 작성</li>
<li>로그인 성공시 JWT를 반환할 success 핸들러 생성</li>
<li>커스텀 필터 SecurityConfig에 등록</li>
</ul>
<hr />
<h2 id="로그인-요청-받기--커스텀-usernamepasswordauthentication-필터-작성">로그인 요청 받기 : 커스텀 UsernamePasswordAuthentication 필터 작성</h2>
<ul>
<li>LoginFilter</li>
</ul>
<pre><code class="language-java">
@Configuration
@EnableWebSecurity
public class SecurityConfig {

    @Bean
    public BCryptPasswordEncoder bCryptPasswordEncoder() {

        return new BCryptPasswordEncoder();
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

                //필터 추가 LoginFilter()는 인자를 받음 (AuthenticationManager() 메소드에 authenticationConfiguration 객체를 넣어야 함) 따라서 등록 필요
        http
                .addFilterAt(new LoginFilter(), UsernamePasswordAuthenticationFilter.class);

        http
                .sessionManagement((session) -&gt; session
                        .sessionCreationPolicy(SessionCreationPolicy.STATELESS));

        return http.build();
    }
}</code></pre>
<ul>
<li>SecurityConfig : AuthenticationManager Bean 등록과 LoginFilter 인수 전달</li>
</ul>
<pre><code class="language-java">@Configuration
@EnableWebSecurity
public class SecurityConfig {

        //AuthenticationManager가 인자로 받을 AuthenticationConfiguraion 객체 생성자 주입
        private final AuthenticationConfiguration authenticationConfiguration;

    public SecurityConfig(AuthenticationConfiguration authenticationConfiguration) {

        this.authenticationConfiguration = authenticationConfiguration;
    }

        //AuthenticationManager Bean 등록
        @Bean
    public AuthenticationManager authenticationManager(AuthenticationConfiguration configuration) throws Exception {

        return configuration.getAuthenticationManager();
    }

    @Bean
    public BCryptPasswordEncoder bCryptPasswordEncoder() {

        return new BCryptPasswordEncoder();
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

                //필터 추가 LoginFilter()는 인자를 받음 (AuthenticationManager() 메소드에 authenticationConfiguration 객체를 넣어야 함) 따라서 등록 필요
        http
                .addFilterAt(new LoginFilter(authenticationManager(authenticationConfiguration)), UsernamePasswordAuthenticationFilter.class);

        http
                .sessionManagement((session) -&gt; session
                        .sessionCreationPolicy(SessionCreationPolicy.STATELESS));

        return http.build();
    }
}</code></pre>
<hr />
<h2 id="로그인-성공시-jwt-반환">로그인 성공시 JWT 반환</h2>
<p>로그인 성공시 successfulAuthentication() 메소드를 통해 JWT를 응답해야 한다. 따라서 JWT 응답 구문을 작성해야 한다.</p>
<hr />
<h2 id="userrepository">UserRepository</h2>
<ul>
<li><p>UserRepository</p>
<pre><code class="language-java">public interface UserRepository extends JpaRepository&lt;UserEntity, Integer&gt; {

  Boolean existsByUsername(String username);

      //username을 받아 DB 테이블에서 회원을 조회하는 메소드 작성
  UserEntity findByUsername(String username);
}</code></pre>
</li>
</ul>
<hr />
<h2 id="userdetails-커스텀-구현">UserDetails 커스텀 구현</h2>
<ul>
<li>CustomUserDetailsService</li>
</ul>
<pre><code class="language-java">@Service
public class CustomUserDetailsService implements UserDetailsService {

    private final UserRepository userRepository;

    public CustomUserDetailsService(UserRepository userRepository) {

        this.userRepository = userRepository;
    }

    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {

                //DB에서 조회
        UserEntity userData = userRepository.findByUsername(username);

        if (userData != null) {

                        //UserDetails에 담아서 return하면 AutneticationManager가 검증 함
            return new CustomUserDetails(userData);
        }

        return null;
    }
}</code></pre>
<p>아직 <strong>CustomUserDetails</strong> 를 구현해주지 않았기때문에 구현해주어야 한다.</p>
<hr />
<h2 id="userdetails-커스텀-구현-1">UserDetails 커스텀 구현</h2>
<p>UserDetails는 필수로 구현해야하는 메서드들이 존재하기 때문에 구현해주자.</p>
<ul>
<li><p>UserDetails</p>
<pre><code class="language-java">public class CustomUserDetails implements UserDetails {

  private final UserEntity userEntity;

  public CustomUserDetails(UserEntity userEntity) {

      this.userEntity = userEntity;
  }

</code></pre>
</li>
</ul>
<pre><code>@Override
public Collection&lt;? extends GrantedAuthority&gt; getAuthorities() {

    Collection&lt;GrantedAuthority&gt; collection = new ArrayList&lt;&gt;();

    collection.add(new GrantedAuthority() {

        @Override
        public String getAuthority() {

            return userEntity.getRole();
        }
    });

    return collection;
}

@Override
public String getPassword() {

    return userEntity.getPassword();
}

@Override
public String getUsername() {

    return userEntity.getUsername();
}

@Override
public boolean isAccountNonExpired() {

    return true;
}

@Override
public boolean isAccountNonLocked() {

    return true;
}

@Override
public boolean isCredentialsNonExpired() {

    return true;
}

@Override
public boolean isEnabled() {

    return true;
}</code></pre><p>}</p>
<pre><code>
* isAccountNonExpired()
* isAccountNonLocked()
* isCredentialsNonExpired()
* isEnabled()

위 4개의 메서드들은 현재 필요하지않으므로 true를 리턴하도록 설정해놓는다.

---

## 테스트

실제로 로그인이 성공적으로 구현이 되었는지 확인해보자.
![](https://velog.velcdn.com/images/gmltn9233/post/ca54bf63-b69b-4124-b424-cc4a0ecb9c50/image.png)

POSTMAN으로 로그인 정보 POST 요청
![](https://velog.velcdn.com/images/gmltn9233/post/013ef6e0-b2f2-4cec-a576-742ed9f9a856/image.png)

![](https://velog.velcdn.com/images/gmltn9233/post/04ac742d-028e-4eaa-b490-6afb50e7e3eb/image.png)

성공!

---

출처: [개발자 유미](https://www.youtube.com/watch?v=q4eTHvQAvRo&amp;list=PLJkjrxxiBSFCcOjy0AAVGNtIa08VLk1EJ&amp;index=8)</code></pre>