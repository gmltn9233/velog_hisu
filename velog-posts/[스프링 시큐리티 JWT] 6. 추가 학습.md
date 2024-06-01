<h2 id="세션-정보">세션 정보</h2>
<h3 id="jwtfilter-를-통과한-뒤-세션-확인">JWTFIlter 를 통과한 뒤 세션 확인</h3>
<pre><code class="language-java">@Controller
@ResponseBody
public class MainController {

    @GetMapping(&quot;/&quot;)
    public String mainP() {

        String name = SecurityContextHolder.getContext().getAuthentication().getName();

        Authentication authentication = SecurityContextHolder.getContext().getAuthentication();

        Collection&lt;? extends GrantedAuthority&gt; authorities = authentication.getAuthorities();
        Iterator&lt;? extends GrantedAuthority&gt; iter = authorities.iterator();
        GrantedAuthority auth = iter.next();
        String role = auth.getAuthority();

        return &quot;Main Controller : &quot;+name + role;
    }

}
</code></pre>
<h3 id="세션-현재-사용자-아이디">세션 현재 사용자 아이디</h3>
<pre><code class="language-java">SecurityContextHolder.getContext().getAuthentication().getName();</code></pre>
<h3 id="세션-현재-사용자-role">세션 현재 사용자 role</h3>
<pre><code class="language-java">Authentication authentication = SecurityContextHolder.getContext().getAuthentication();

Collection&lt;? extends GrantedAuthority&gt; authorities = authentication.getAuthorities();
Iterator&lt;? extends GrantedAuthority&gt; iter = authorities.iterator();
GrantedAuthority auth = iter.next();
String role = auth.getAuthority();</code></pre>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/178f0c5d-834c-48e5-aa89-d1925f3d2276/image.png" /></p>
<p>토큰을 넣어서 루트값으로 요청을 보내면 위와같이 사용자 정보를 꺼낼 수 있다.</p>
<hr />
<h2 id="cors-설정">CORS 설정</h2>
<h3 id="cors란">CORS란</h3>
<p>CORS는 <strong><code>Cross-Origin Resource Sharing</code></strong>의 줄임말로 <strong><code>교차-출처 리소스 공유</code></strong> 라도고 한다.
즉, 다른 출처이기 때문에 발생하는 에러라고도 할 수 있다.</p>
<h3 id="발생원리">발생원리</h3>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/228d505c-ca42-44ea-86ba-636034b1fab3/image.png" /></p>
<p>사용자가 웹 브라우저를 통해서 웹 사이트에 접속하게되면, 프론트 서버에서 react나 view와 같은 페이지를 응답해준다.</p>
<p>프론트엔드 서버는 보통 <strong>3000번</strong> 포트에 띄워서 테스트를 하게되고, 데이터를 받기위해 api 서버를 호출하게 되면 api 데이터는 <strong>8080</strong> 포트에서 응답하게 된다.</p>
<p>이렇게 2개의 서버 포트번호가 다르지만 웹 브라우저에서는 <strong>교차-출처 리소스 공유</strong>를 금지시키기 때문에 데이터가 보이지않게된다.</p>
<h3 id="cors-설정-1">CORS 설정</h3>
<blockquote>
<ul>
<li>Security에서 설정</li>
</ul>
</blockquote>
<ul>
<li>Servlet 방식인 MvcConfig에서 설정</li>
</ul>
<p>기본적으로 컨트롤러단에 들어오는 데이터는 MvcConfig에서 설정해주어야 하고, SecurityFilter를 타는 로그인 방식에는 Security 설정을 하지않으면 토큰이 리턴되지않는 문제가 발생한다.</p>
<p><strong>-&gt; 모두 처리해주어야 한다!</strong></p>
<ul>
<li>SecurityConfig</li>
</ul>
<pre><code class="language-java">@Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {

        http
            .cors((corsCustomizer -&gt; corsCustomizer.configurationSource(
                new CorsConfigurationSource() {

                    @Override
                    public CorsConfiguration getCorsConfiguration(HttpServletRequest request) {

                        CorsConfiguration configuration = new CorsConfiguration();
                        //프론트엔트 서버 허용
                        configuration.setAllowedOrigins(
                            Collections.singletonList(&quot;http://localhost:3000&quot;));
                        //허용하는 메소드
                        configuration.setAllowedMethods(Collections.singletonList(&quot;*&quot;));
                        configuration.setAllowCredentials(true);
                        //허용할 헤더
                        configuration.setAllowedHeaders(Collections.singletonList(&quot;*&quot;));
                        //허용할 시간
                        configuration.setMaxAge(3600L);
                        //Authorizaion 헤더 허용
                        configuration.setExposedHeaders(Collections.singletonList(&quot;Authorization&quot;));

                        return configuration;
                    }
                })));
        return http.build();
    }  </code></pre>
<ul>
<li>config &gt; CorsMvcConfig</li>
</ul>
<pre><code class="language-java">@Configuration
public class CorsMvcConfig implements WebMvcConfigurer {

    @Override
    public void addCorsMappings(CorsRegistry corsRegistry) {

        //모든경로에서 3000번 요청을 허용
        corsRegistry.addMapping(&quot;/**&quot;)
            .allowedOrigins(&quot;http://localhost:3000&quot;);
    }
}</code></pre>
<hr />
<p>출처: <a href="https://www.youtube.com/watch?v=Y1p6bVrRExs&amp;list=PLJkjrxxiBSFCcOjy0AAVGNtIa08VLk1EJ&amp;index=13">개발자 유미</a></p>