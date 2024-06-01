<h2 id="rest--restful-api">REST / RESTful API</h2>
<h3 id="api란">API란?</h3>
<blockquote>
<p> <strong>API(Application Programming Interface)</strong>는 한 프로그램에서 다른 프로그램으로 <strong>데이터를 주고받기 위한 방법</strong>을 말한다. </p>
</blockquote>
<h3 id="rest란">REST란?</h3>
<blockquote>
<ul>
<li>웹 애플리케이션을 개발하기 위한 아키텍쳐 스타일 중 하나로 클라이언트와 서버 간의 <strong>'통신 방식'</strong>을 규정한 것</li>
</ul>
</blockquote>
<ul>
<li>해당 통신 방식은 'HTTP 프로토콜'을 기반으로 하며 <strong>자원, 행위, 표현</strong> 세 가지 요소로 구성 됨</li>
</ul>
<h3 id="rest-api-representational-state-transfer란">REST API (Representational State Transfer)란?</h3>
<blockquote>
<p>REST 아키텍쳐 스타일에 따라 구성한 API를 의미</p>
</blockquote>
<h3 id="restful-api란">RESTful API란?</h3>
<blockquote>
<ul>
<li>HTTP를 위한 아키텍쳐 스타일 중 하나로 <strong>REST의 원칙을 따르는 웹 서비스</strong>를 '구현하는 방식'이며 웹 서비스를 개발하는 방식</li>
</ul>
</blockquote>
<ul>
<li>클라이언트와 서버 간 자원을 주고받을 때 '일괄적인 방식' 을 제공하며 이를 통해 서버와 클라이언트의 역할을 '명확하게 분리'하고 서로 '독립적'으로 개발할 수 있게 된다.</li>
</ul>
<h3 id="rest-api-와-restful-api의-차이점">REST API 와 RESTful API의 차이점</h3>
<blockquote>
<ul>
<li>REST API는 REST 아키텍쳐 스타일을 따르는 API이며 RESTful API는 REST API를 제공하는 <strong>웹 서비스</strong>를 의미</li>
</ul>
</blockquote>
<ul>
<li>결론적으로는 RESTful API는 REST API의 원칙을 따르기 때문에 REST API보다 조금 더 RESTful 하다고 볼 수 있다.</li>
</ul>
<h3 id="restful-하다-의미는">RESTful 하다 의미는?</h3>
<blockquote>
<p> REST API의 원칙을 따라 구성한 웹 서비스를 의미한다. <strong>자원을 URL로 표현</strong>하고 <strong>HTTP Method를 이용해 해당 자원에 대해 CRUD 작업을 수행</strong>하며 <strong>리소스와 행위를 명시적으로 분리</strong>하여 사용함으로써 URL만 보고도 어떤 동작을 수행하는지 알 수 있도록 구성한 것을 의미</p>
</blockquote>
<h2 id="rest의-요소">REST의 요소</h2>
<h3 id="1-자원resource이란">1. 자원(Resource)이란?</h3>
<blockquote>
<ul>
<li>웹 상에 존재하는 데이터나 정보와 같은 모든 것을 의미 ex) 사용자,댓글,이미지 등</li>
</ul>
</blockquote>
<ul>
<li>자원의 이름 구성은 동사가 아닌 '<strong>명사'</strong> 를 사용하는것이 권장됨</li>
</ul>
<h3 id="2-행위verb란">2. 행위(Verb)란?</h3>
<p><strong>- HTTP Method를 이용한 자원에 대한 행위(조회,생성,수정,삭제)를 의미</strong></p>
<table>
<thead>
<tr>
<th><strong>HTTP Method</strong></th>
<th><strong>행위</strong></th>
<th><strong>예시</strong></th>
</tr>
</thead>
<tbody><tr>
<td>GET</td>
<td>자원을 조회</td>
<td>사용자 정보 조회: GET/users/{user_id}</td>
</tr>
<tr>
<td>POST</td>
<td>자원을 생성</td>
<td>사용자 정보 생성: POST/users</td>
</tr>
<tr>
<td>PUT</td>
<td>자원을 수정</td>
<td>사용자 정보 수정: PUT/users/{user_id}</td>
</tr>
<tr>
<td>PATCH</td>
<td>자원 일부를 수정</td>
<td>사용자 정보 일부 수정 : PATCH/users/{user_id}</td>
</tr>
<tr>
<td>DELETE</td>
<td>자원을 삭제</td>
<td>사용자 정보 삭제: DELETE/users/{user_id}</td>
</tr>
</tbody></table>
<h3 id="3-표현representation이란">3. 표현(Representation)이란?</h3>
<blockquote>
<ul>
<li>RESTful에서 클라이언트와 서버 간의 데이터 통신을 주고받는 '<strong>데이터 형식</strong>(JSON,XML,HTML)' 을 의미</li>
</ul>
</blockquote>
<table>
<thead>
<tr>
<th>분류</th>
<th>JSON</th>
<th>XML</th>
<th>HTML</th>
</tr>
</thead>
<tbody><tr>
<td>문법</td>
<td>가볍고 간결하며 쉽게 읽고 쓸 수 있음</td>
<td>JSON과 비교해 더 많은 데이터를 필요로하며 상세함</td>
<td>데이터 교환을 위한 것이 아니라 웹 페이지 렌더링을 위해 설계됨</td>
</tr>
<tr>
<td>가독성</td>
<td>읽고 구문 분석하기 쉽고 복잡한 데이터 구조와 배열을 지원함</td>
<td>읽고 구문 분석하기 쉽고 복잡한 데이터 구조와 스키마 유효성 검사를 지원함</td>
<td>읽고 구문 분석하기 쉽고 기본 데이터 구조와 링크를 지원함</td>
</tr>
<tr>
<td>유연성</td>
<td>쉽게 확장하고 사용자 정의할 수 있으며 대부분의 프로그래밍 언어를 지원함</td>
<td>이름 공간과 사용자 정의 태그를 지원하지만 확장하는 데 더 많은 노력이 필요함</td>
<td>제한된 유연성으로 복잡한 데이터 교환에는 적합하지 않음</td>
</tr>
<tr>
<td>인기도</td>
<td>웹 개발, 모바일 앱 및 API에서 널리 사용됨</td>
<td>기업 응용 프로그램 및 레거시 시스템에서 널리 사용됨</td>
<td>웹 개발에서 널리 사용됨, 그러나 API에는 권장하지 않음</td>
</tr>
</tbody></table>
<h3 id="4-restful-api-상태코드status-code">4. RESTful API 상태코드(Status Code)</h3>
<blockquote>
<ul>
<li>RESTful API 에서는 <strong>HTTP 상태 코드</strong>를 사용하여 클라이언트에게 요청의 성공 또는 실패를 알려줌</li>
</ul>
</blockquote>
<ul>
<li>상태코드는 3자리 숫자로 표현되며 각각의 숫자는 특정한 의미를 가지고 있음</li>
</ul>
<h4 id="전체적인-상태코드-형태">전체적인 상태코드 형태</h4>
<table>
<thead>
<tr>
<th><strong>상태코드</strong></th>
<th><strong>상태 정보</strong></th>
<th><strong>의미</strong></th>
</tr>
</thead>
<tbody><tr>
<td><strong>1xx</strong></td>
<td>Informational</td>
<td>요청이 수신되었으며 처리가 계속됨을 의미</td>
</tr>
<tr>
<td><strong>2xx</strong></td>
<td>Success</td>
<td>요청이 성공적으로 처리되었음을 의미</td>
</tr>
<tr>
<td><strong>3xx</strong></td>
<td>Redirection</td>
<td>요청이 완료되기 위해 추가 작업이 필요함을 의미</td>
</tr>
<tr>
<td><strong>4xx</strong></td>
<td>Client Error</td>
<td>클라이언트 오류로 인해 요청이 실패함을 의미</td>
</tr>
<tr>
<td><strong>5xx</strong></td>
<td>Server Error</td>
<td>서버 오류로 인해 요청이 실패함을 의미</td>
</tr>
</tbody></table>
<h4 id="일반적으로-사용되는-상태코드-종류">일반적으로 사용되는 상태코드 종류</h4>
<table>
<thead>
<tr>
<th><strong>상태코드</strong></th>
<th><strong>상태 정보</strong></th>
<th><strong>의미</strong></th>
</tr>
</thead>
<tbody><tr>
<td><strong>200</strong></td>
<td>OK</td>
<td>요청이 성공적으로 처리되었음을 의미, 클라이언트에게 요청한 데이터를 반환함</td>
</tr>
<tr>
<td><strong>201</strong></td>
<td>Created</td>
<td>새로운 리소스가 성공적으로 생성되었음을 의미</td>
</tr>
<tr>
<td><strong>204</strong></td>
<td>No Content</td>
<td>요청이 성공적으로 처리되었지만, 반환할 데이터가 없음을 의미</td>
</tr>
<tr>
<td><strong>400</strong></td>
<td>Bad Request</td>
<td>클라이언트 요청이 잘못되었음을 의미</td>
</tr>
<tr>
<td><strong>401</strong></td>
<td>Unauthorized</td>
<td>클라이언트가 인증되지 않았음을 의미</td>
</tr>
<tr>
<td><strong>403</strong></td>
<td>Forbidden</td>
<td>클라이언트가 요청한 리소스에 접근할 권한이 없음을 의미</td>
</tr>
<tr>
<td><strong>404</strong></td>
<td>Not Found</td>
<td>요청한 리소스를 찾을 수 없음을 의미</td>
</tr>
<tr>
<td><strong>500</strong></td>
<td>Internal Server Error</td>
<td>서버에서 오류가 발생하여 요청을 처리할 수 없음을 의미</td>
</tr>
</tbody></table>
<h2 id="restful-api-구현">RESTful API 구현</h2>
<h3 id="controller-와-restcontroller의-차이">@Controller 와 @RestController의 차이</h3>
<ul>
<li>@Controller는 일반적으로 HTTP 요청을 처리하고 <strong>'View'</strong>를 반환하는데 사용됨</li>
<li>@RestController는 HTTP 요청에 대한 RESTful 웹 서비스의 <strong>JSON,XML 등의 '데이터'</strong>를 반환하는데 사용됨 </li>
</ul>
<table>
<thead>
<tr>
<th><strong>Annotation</strong></th>
<th><strong>설명</strong></th>
<th><strong>비고</strong></th>
</tr>
</thead>
<tbody><tr>
<td><strong>@Controller</strong></td>
<td>HTTP 요청을 처리하는 컨트롤러 클래스를 정의하는 어노테이션</td>
<td>Spring MVC View를 반환하는데 사용됨</td>
</tr>
<tr>
<td><strong>@RestController</strong></td>
<td>RESTful 웹 서비스를 위한 컨트롤러 클래스를 정의하는 어노테이션</td>
<td>RESTful 웹 서비스의 JSON,XML 등의 응답을 반환하는데 사용됨</td>
</tr>
</tbody></table>
<h3 id="restful-api의-행위verb-annotation">RESTful API의 행위(Verb) Annotation</h3>
<table>
<thead>
<tr>
<th><strong>Annotation</strong></th>
<th><strong>HTTP</strong></th>
<th><strong>Method 역할</strong></th>
<th><strong>비고(예시)</strong></th>
</tr>
</thead>
<tbody><tr>
<td><strong>@GetMapping</strong></td>
<td>GET</td>
<td>클라이언트가 리소스를 조회할 때 사용</td>
<td>@GetMapping(value=&quot;/users&quot;)</td>
</tr>
<tr>
<td><strong>@PostMapping</strong></td>
<td>POST</td>
<td>클라이언트가 리소스를 생성할 때 사용</td>
<td>@PostMapping(value=&quot;/users&quot;)</td>
</tr>
<tr>
<td><strong>@PutMapping</strong></td>
<td>PUT</td>
<td>클라이언트가 리소스를 갱신할 때 사용</td>
<td>@PutMapping(value=&quot;/users&quot;)</td>
</tr>
<tr>
<td><strong>@PatchMapping</strong></td>
<td>PATCH</td>
<td>클라이언트가 리소스 일부를 갱신할 때 사용</td>
<td>@PatchMapping(value=&quot;/users&quot;)</td>
</tr>
<tr>
<td><strong>@DeleteMapping</strong></td>
<td>DELETE</td>
<td>클라이언트가 리소스를 삭제할 때 사용</td>
<td>@DeleteMapping(value=&quot;/users&quot;)</td>
</tr>
<tr>
<td><strong>@RequestMapping</strong></td>
<td>ALL</td>
<td>요청 메서드(GET, POST, PUT, DELETE 등)와 URL 매핑을 함께 지정할 수 있다.    @RequestMapping(value=&quot;/users&quot;, method=RequestMethod.GET)</td>
<td></td>
</tr>
</tbody></table>
<p>💡 <strong>@RequestMapping 과 @XXMapping 중 무엇을 사용해야 할까?</strong></p>
<blockquote>
<ul>
<li><strong>간단한 HTTP 요청에 대해서는 @XXMapping</strong>을 사용하는 것이 코드 가독성과 유지보수성을 높일 수 있다. 하지만 <strong>여러 개의 HTTP 요청 메소드에 대해 하나의 메소드로 처리해야 하는 경우는 @RequestMapping</strong>을 사용해야 한다.</li>
</ul>
</blockquote>
<h3 id="restful-api의-표현representation-annotation">RESTful API의 표현(Representation) Annotation</h3>
<h4 id="1-json-데이터-형식의-전송-방식">1. JSON 데이터 형식의 전송 방식</h4>
<table>
<thead>
<tr>
<th><strong>구분</strong></th>
<th><strong>GET 방식</strong></th>
<th><strong>POST 방식</strong></th>
</tr>
</thead>
<tbody><tr>
<td>전송 데이터</td>
<td>URL 끝에 파라미터를 붙여서 서버에 요청을 보내는 방식</td>
<td>HTTP Body에 담아서 요청을 보내는 방식</td>
</tr>
<tr>
<td>전송 데이터 크기 제한</td>
<td>데이터의 양에 제한 있음</td>
<td>데이터의 양에 제한 없음</td>
</tr>
<tr>
<td>캐싱 가능 여부</td>
<td>가능</td>
<td>불가능</td>
</tr>
<tr>
<td>보안</td>
<td>데이터 노출 가능성 있음</td>
<td>데이터 노출 가능성 낮음</td>
</tr>
<tr>
<td>사용 예시</td>
<td>검색어 전송, 페이지 요청</td>
<td>로그인,회원가입,게시글 작성</td>
</tr>
</tbody></table>
<p>💡 <strong>캐싱이란?</strong></p>
<blockquote>
<ul>
<li>이전에 요청한 데이터를 저장해 두었다가 다음 요청 시에 바로 제공하는 것, 네트워크 대역폭을 절약하고, 서버의 부하를 줄일 수 있다.</li>
</ul>
</blockquote>
<h4 id="2-데이터-형식-주요-어노테이션">2. 데이터 형식 주요 어노테이션</h4>
<p><strong>@PathVariable</strong> : 'URL 경로의 일부'를 매개변수로 전달받는 어노테이션</p>
<pre><code class="language-java">@GetMapping(&quot;/user/{name}&quot;)
    public String findByName(@PathVariable(&quot;name&quot;) String name){
        return &quot;Name: &quot;+name;
    }</code></pre>
<p><a href="http://localhost:8080/user/user1">http://localhost:8080/user/user1</a> 로 접속시</p>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/8165fabc-e7de-4e55-ab16-14ac92179c62/image.png" /></p>
<p><strong>@RequestParam</strong> : 'HTTP 요청 파라미터'를 매개변수로 전달받는 어노테이션</p>
<pre><code class="language-java">@GetMapping(&quot;/user&quot;)
    public User saveUser(@RequestParam(&quot;name&quot;) String name, @RequestParam(&quot;studentId&quot;) Long studentId){
        User user = new User();
        user.setName(name);
        user.setStudentId(studentId);
        return user;
    }</code></pre>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/1b6f9853-fc42-4bd8-a97b-3359afdeeb73/image.png" /></p>
<p><strong>@RequestBody</strong> : HTTP 요청의 '본문(body)'을 매개변수로 전달받는 어노테이션</p>
<pre><code class="language-java">@PostMapping(&quot;/user&quot;)
    public User saveUser(@RequestBody User user){
        // user 객체를 받아서 이름을 변경
        user.setName(&quot;user2&quot;);
        return user;
    }</code></pre>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/b5f4ea41-9506-402a-870e-37ee9b3d1c88/image.png" /></p>
<p><strong>ResponseBody</strong> : HTTP 응답의 '본문(body)'을 생성하는 메소드에 적용하는 어노테이션</p>
<pre><code class="language-java">@ResponseBody
    @GetMapping(&quot;/data&quot;)
    public User getUser(){
        User user = new User();
        user.setName(&quot;user1&quot;);
        user.setStudentId(1234L);
        return user;
    }</code></pre>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/a5c15c14-936b-4cc2-97b0-9f61abee4f3b/image.png" /></p>
<p>💡 <strong>RequestBody와 ResponseBody 차이?</strong></p>
<p>RequestBody: HTTP요청의 body 내용을 java 객체로 변환
ResponseBody: java 객체의 내용을 HTTP 응답의 body로 변환</p>
<p><strong>위 예시에는 RequestBody만 써도 응답 body에 객체가 들어오던데요??</strong></p>
<blockquote>
<p><strong>@RestController</strong>를 사용하는 경우에는 자동으로 return 값에 ResponseBody를 붙여 body에 전달되기 때문에 @ResponseBody 생략이 가능!</p>
</blockquote>
<p><strong>ResponseStatus</strong> : HTTP 응답의 상태 코드를 지정하는 어노테이션</p>
<pre><code class="language-java">@ResponseStatus(HttpStatus.NOT_FOUND)
public class UserNotFoundException extends RuntimeException{
    public UserNotFoundException(String msg){
        super(msg);
    }
}</code></pre>
<p>위와 같이 응답을 설정해주고 싶을때 사용한다.</p>
<h3 id="restfulapi-구성하기">RESTfulAPI 구성하기</h3>
<p>💡 <strong>RESTfulAPI 설계 과정</strong></p>
<blockquote>
<ol>
<li>자원(Resource)을 정의한다.</li>
<li>행위(HTTP Method)를 정의한다.</li>
<li>표현(Representation)을 정의한다.</li>
<li>상태 코드(Status Code)를 정의한다.</li>
<li>API 문서화를 정의한다.</li>
</ol>
</blockquote>
<table>
<thead>
<tr>
<th>자원 : Endpoint</th>
<th>행위 : HTTP Method</th>
<th>표현 : 데이터 전송 방식</th>
<th>상태 코드</th>
<th>설명</th>
<th>구성 예시</th>
</tr>
</thead>
<tbody><tr>
<td>user</td>
<td>GET</td>
<td>GET 전송방식, parameter or URL 경로</td>
<td>성공 시 HttpStatus.OK 반환</td>
<td>사용자를 조회하는 API입니다.</td>
<td>GET /user/{userId}</td>
</tr>
<tr>
<td>user</td>
<td>POST</td>
<td>POST 전송방식, JSON 데이터 전송</td>
<td>성공 시 HttpStatus.OK 반환</td>
<td>사용자를 등록하는 API입니다.</td>
<td>POST /user</td>
</tr>
<tr>
<td>user</td>
<td>PUT</td>
<td>POST 전송방식, JSON 데이터 전송</td>
<td>성공 시 HttpStatus.OK 반환</td>
<td>사용자를 수정하는 API입니다</td>
<td>PUT /user</td>
</tr>
<tr>
<td>user</td>
<td>DELETE</td>
<td>POST 전송방식, JSON 데이터 전송</td>
<td>성공 시 HttpStatus.OK 반환</td>
<td>사용자를 삭제하는 API입니다</td>
<td>DELETE /user</td>
</tr>
</tbody></table>
<h3 id="responseentity">ResponseEntity</h3>
<p>Spring Framework에서 제공하는 클래스 중 <strong>HttpEntity</strong>라는 클래스가 존재한다. 이것은 HTTP 요청(Request) 또는 응답(Response)에 해당하는 <strong>HttpHeader</strong>와 <strong>HttpBody</strong>를 포함하는 클래스이다.</p>
<p>이러한 HttpEntity 클래스를 상속받아 구현한 클래스가 RequestEntity, ResponseEntity 클래스이다. <strong>ResponseEntity</strong>는 사용자의 HttpRequest에 대한 응답 데이터를 포함하는 클래스이다. 따라서 <strong>HttpStatus,HttpHeaders,HttpBody</strong>를 포함한다.</p>
<pre><code class="language-java"> @PostMapping(&quot;/user&quot;)
    public ResponseEntity saveUser(@RequestBody User user){
        // user 객체를 받아서 이름을 변경
        user.setName(&quot;user2&quot;);
        return new ResponseEntity(HttpStatus.OK);
    }</code></pre>
<p>위와 같이 코드를 짜고 POSTMAN으로 요청을 보내면 상태코드가 200으로 오는것을 확인할 수 있다.
<img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/2824670f-a929-4b01-92f9-7514535118ef/image.png" /></p>
<p>또한 상태코드(Status),헤더(Headers),응답데이터(ResponseData)를 담는 생성자도 존재한다.</p>
<pre><code class="language-java">public class ResponseEntity&lt;T&gt; extends HttpEntity&lt;T&gt; {

    public ResponseEntity(@Nullable T body, @Nullable MultiValueMap&lt;String, String&gt; headers, HttpStatus status) {
        super(body, headers);
        Assert.notNull(status, &quot;HttpStatus must not be null&quot;);
        this.status = status;
    }
}</code></pre>
<p>위의 ResponseEntity를 이용해서 클라이언트에게 응답을 보내는 예제를 정리해보자.</p>
<pre><code class="language-java">import lombok.Data;

@Data
public class Message {

    private StatusEnum status;
    private String message;
    private Object data;

    public Message() {
        this.status = StatusEnum.BAD_REQUEST;
        this.data = null;
        this.message = null;
    }
}</code></pre>
<p><strong>Message</strong>라는 클래스를 만들어 <strong>상태코드, 메시지, 데이터</strong>를 담을 필드를 추가한다.</p>
<pre><code class="language-java">public enum StatusEnum {

    OK(200, &quot;OK&quot;),
    BAD_REQUEST(400, &quot;BAD_REQUEST&quot;),
    NOT_FOUND(404, &quot;NOT_FOUND&quot;),
    INTERNAL_SERER_ERROR(500, &quot;INTERNAL_SERVER_ERROR&quot;);

    int statusCode;
    String code;

    StatusEnum(int statusCode, String code) {
        this.statusCode = statusCode;
        this.code = code;
    }
}</code></pre>
<p>상태코드로 보낼 몇가지의 예시 enum</p>
<pre><code class="language-java">@PostMapping(&quot;/user&quot;)
    public ResponseEntity&lt;Message&gt; saveUser(@RequestBody User user){
        // user 객체를 받아서 이름을 변경
        user.setName(&quot;user2&quot;);

        Message message = new Message();
        HttpHeaders headers= new HttpHeaders();
        headers.setContentType(new MediaType(&quot;application&quot;, &quot;json&quot;, StandardCharsets.UTF_8));
        message.setStatus(StatusEnum.OK);
        message.setMessage(&quot;성공 코드&quot;);
        message.setData(user);

        return new ResponseEntity&lt;&gt;(message, headers, HttpStatus.OK);
    }</code></pre>
<p>이전의 코드에서 Message 클래스를 통해 <strong>StatusCode, ResponseMessage, ResponseData</strong>를 담아서 클라이언트에게 응답을 보내는 코드이다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/4e7d1360-5499-4f4b-84e2-121a57a2864e/image.png" /></p>
<p><a href="https://adjh54.tistory.com/150?category=1074554">참고 자료</a>
<a href="https://devlog-wjdrbs96.tistory.com/182">참고 자료</a></p>