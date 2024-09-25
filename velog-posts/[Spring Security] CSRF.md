<h2 id="🎯현-상황">🎯현 상황</h2>
<p>기이수 파일을 업로드했던 유저가 로그아웃 후 다시 기이수 파일을 업로드 하려하면 <strong>403 Forbidden에러가 발생</strong>했다.
<img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/b6f8f8ca-6a66-4bad-a3c5-f1dde1d519d2/image.png" /></p>
<hr />
<h2 id="🔍원인">🔍원인</h2>
<h3 id="기이수-성적을-업로드-하지-않은-경우">기이수 성적을 업로드 하지 않은 경우</h3>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/f53a7fe6-aa71-4af3-a7e0-b67a3482f69b/image.png" /></p>
<p>정상적으로 <code>XSRF-TOKEN</code>이 존재하는것을 확인할 수 있다.
하지만 로그아웃하고 다시 접속하게 되면</p>
<h3 id="기이수-성적을-업로드-한-후">기이수 성적을 업로드 한 후</h3>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/2815a93c-db22-4e16-96e0-2462aef0266a/image.png" /></p>
<p><code>XSRF-TOKEN</code>이 소실된 것을 확인할 수 있다!</p>
<h3 id="xsrf-token">XSRF-TOKEN</h3>
<p><strong>XSRF-TOKEN</strong>은 <strong>CSRF공격</strong>으로부터 웹 애플리케이션을 보호하기 위해 사용되는 보안 토큰이다.</p>
<blockquote>
<p>❓ <strong>CSRF 공격</strong>
공격자가 사용자에게 보이지 않는 상태에서 악의적인 요청을 보내도록 유도하여 사용자의 세션을 이용해 원치 않는 작업을 수행하는 것</p>
</blockquote>
<h3 id="cookiecsrftokenrepository">CookieCsrfTokenRepository</h3>
<p><strong>스프링 시큐리티</strong> 에서는 <strong>CSRF 토큰</strong>을 통해 CSRF 공격을 방어하는데, <strong>CsrfTokenRepository</strong>을 사용하여 <code>XSRF-TOKEN</code>이라는 쿠키에 CSRF 토큰을 유지한다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/4cf28389-8285-4ede-b2f2-19aa6d322cd4/image.png" /></p>
<p><strong>CookieCsrfTokenRepository</strong> 클래스를 살펴보면 실제로 <code>XSRF-TOKEN</code> 에 발행되는 것을 확인할 수 있다.
<img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/de6238df-605e-4139-bb46-1dbeed1779b1/image.png" /></p>
<hr />
<h2 id="💡해결방법">💡해결방법</h2>
<p>그런데 왜 <code>XSRF-TOKEN</code> 이 <strong>소실되는 문제</strong>가 발생했을까?
먼저 스프링 시큐리티의 <code>XSRF-TOKEN</code> 생성 방식에 대해 살펴볼 필요가 있다.
보통 <strong>CSRF 옵션은 POST, DELETE, UPDATE에만 적용한다.</strong></p>
<p><strong>GET</strong>은 리소스를 제공할 뿐 리소스의 상태를 변화시키지 않으므로 <strong>CSRF 옵션</strong>이 필요하지 않다. </p>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/5fecc219-e967-4391-9887-3b80ff2853c4/image.png" /></p>
<p>그래서 단순히 정보를 조회하는 <strong>회원페이지</strong>에도 <code>XSRF-TOKEN</code>이 생성되지 않는 것을 확인할 수 있다.</p>
<p>하지만 <strong>기이수성적 업로드 페이지</strong>에서는 정보를 불러온 후(GET) 버튼을 통해 업데이트(POST) 하기 때문에 <code>XSRF-TOKEN</code>이 존재해야 한다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/b64a761b-7bff-47c6-a191-c72654e20c7b/image.png" /></p>
<p>같은 방식인 <strong>비밀번호 변경 페이지</strong>도 GET 요청을 통해 폼을 불러오고 POST 요청을 통해 업데이트 하기 때문에 <code>XSRF-TOKEN</code>이 존재하는 것을 볼 수 있다.</p>
<p>하지만 <strong>기이수성적 업로드 페이지</strong>는 업로드 이후에는 GET 요청으로만 인식해서인지 <code>XSRF-TOKEN</code>이 소실되어있다. 그래서 직접 <code>CSRF TOKEN</code>
을 포함시켜야 했다.</p>
<h3 id="csrf-token-직접-포함-방법">CSRF TOKEN 직접 포함 방법</h3>
<h4 id="1-form-태그">1. FORM 태그</h4>
<pre><code class="language-html">&lt;input type=&quot;hidden&quot;
       name=&quot;${_csrf.parameterName}&quot;
       value=&quot;${_csrf.token}&quot;/&gt;</code></pre>
<p>주로 <strong>POST</strong> 요청을 보내는 폼에 사용되며, 사용자가 폼을 제출할 때 CSRF 토큰이 함께 전송되게 해준다. 그러나 현재 CSRF 토큰이 <strong>POST 요청을 보내기 전에 소실된 상황</strong>이므로 적용되지 않았다.</p>
<h4 id="2-ajax-요청">2. AJAX 요청</h4>
<pre><code class="language-js">$(function () {
    var token = $(&quot;meta[name='_csrf']&quot;).attr(&quot;content&quot;);
    var header = $(&quot;meta[name='_csrf_header']&quot;).attr(&quot;content&quot;);
    $(document).ajaxSend(function(e, xhr, options) {
        xhr.setRequestHeader(header, token);
    });
});</code></pre>
<p> <strong>AJAX 요청</strong>과 같은 <strong>비동기 통신</strong>시 CSRF 토큰을 헤더로 추가하는 방식인데, 현재 통신 방식과 달라 의미가 없다.</p>
<h4 id="3-meta-태그">3. META 태그</h4>
<pre><code class="language-html">&lt;html&gt;
&lt;head&gt;
    &lt;meta name=&quot;_csrf&quot; content=&quot;${_csrf.token}&quot;/&gt;
    &lt;!-- default header name is X-CSRF-TOKEN --&gt;
    &lt;meta name=&quot;_csrf_header&quot; content=&quot;${_csrf.headerName}&quot;/&gt;
    &lt;!-- ... --&gt;
&lt;/head&gt;
&lt;!-- ... --&gt;
&lt;/html&gt;</code></pre>
<p>CSRF 토큰과 헤더 이름을 메타 태그로 페이지에 포함시키는 것으로 <strong>GET 요청에서도 CSRF 토큰이 존재</strong>할 수 있게 만든다. 그래서 이 방식을 채택하여 적용시켰다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/7e4c8744-7f76-404c-aba0-8543c41fc963/image.png" /></p>
<p><strong>해결!</strong></p>