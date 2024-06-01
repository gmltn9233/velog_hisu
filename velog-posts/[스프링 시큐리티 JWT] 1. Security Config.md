<h2 id="의존성">의존성</h2>
<ul>
<li>필수 의존성<ul>
<li>Lombok</li>
<li>Spring Web</li>
<li>Spring Security</li>
<li>Spring Data JPA</li>
<li>MySQL Driver</li>
</ul>
</li>
</ul>
<hr />
<h2 id="데이터베이스-의존성-주석-처리">데이터베이스 의존성 주석 처리</h2>
<p>임시로 주석처리 진행 (스프링 부트에서 데이터베이스 의존성을 추가한 뒤 연결을 진행하지 않을 경우 런타임 에러 발생)</p>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/e8f2c6ac-2ec7-46da-a924-4e23745596bf/image.png" /></p>
<hr />
<h2 id="jwt-필수-의존성">JWT 필수 의존성</h2>
<p>최신버전인 JWT 0.12.3 버전을 기준으로 구현</p>
<ul>
<li><p>0.12.3</p>
<pre><code class="language-Groovy">dependencies {

  implementation 'io.jsonwebtoken:jjwt-api:0.12.3'
  implementation 'io.jsonwebtoken:jjwt-impl:0.12.3'
  implementation 'io.jsonwebtoken:jjwt-jackson:0.12.3'
}</code></pre>
</li>
</ul>
<hr />
<h2 id="기본-controller-생성">기본 Controller 생성</h2>
<ul>
<li>MainController<pre><code class="language-java">import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.ResponseBody;
</code></pre>
</li>
</ul>
<p>@Controller
@ResponseBody
public class MainController {</p>
<pre><code>@GetMapping(&quot;/&quot;)
public String mainP() {

    return &quot;main Controller&quot;;
}</code></pre><p>}</p>
<pre><code>
* AdminController
```java
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.ResponseBody;

@Controller
@ResponseBody
public class AdminController {

    @GetMapping(&quot;/admin&quot;)
    public String adminP() {

        return &quot;admin Controller&quot;;
    }
}</code></pre><hr />
<h2 id="securityconfig-클래스-기본-요소-작성">SecurityConfig 클래스 기본 요소 작성</h2>
<p>시큐리티 JWT 구현을 위한 Config 클래스의 일부분을 작성할 예정입니다. 먼저 기본적인 설정만 진행하고 시리즈를 진행하며 커스텀 필터 요소들을 추가 구현할 예정입니다.</p>
<ul>
<li>SecurityConfig</li>
</ul>
<pre><code class="language-java">package com.example.jwtprac.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.http.SessionCreationPolicy;
import org.springframework.security.web.SecurityFilterChain;

@Configuration
@EnableWebSecurity
public class SecurityConfig {

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

        //세션 설정
        http
            .sessionManagement((session) -&gt; session
                .sessionCreationPolicy(SessionCreationPolicy.STATELESS));

        return http.build();
    }
}</code></pre>
<p>JWT를 통한 인증/인가를 위해서 세션을 STATELESS 상태로 설정하는것이 중요하다.</p>
<hr />
<h2 id="bcryptpasswordencoder-등록">BCryptPasswordEncoder 등록</h2>
<ul>
<li><p>SecurityConfig</p>
<pre><code class="language-java">@Configuration
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

      http
              .sessionManagement((session) -&gt; session
                      .sessionCreationPolicy(SessionCreationPolicy.STATELESS));

      return http.build();
  }
}</code></pre>
</li>
</ul>
<hr />
<p>API 서버는 웹 서버와 달리 서버측으로 요청을 보낼 수 있는 페이지가 존재하지 않고 엔드 포인트만 존재하기 때문에 요청을 보낼 API 클라이언트가 필요하다.</p>
<p>POSTMAN 으로 실습을 진행해보자.
<a href="https://www.postman.com/downloads/">POSTMAN</a></p>
<h4 id="postman-호출">POSTMAN 호출</h4>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/b24f6746-db4f-4c5f-8adf-b228fbddd198/image.png" /></p>
<ul>
<li>출처: <a href="https://www.youtube.com/watch?v=A3YsWHGbeZQ">개발자 유미</a></li>
</ul>