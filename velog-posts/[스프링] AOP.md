<p><strong><p style="color: red;">본 내용은 김영한님의 스프링입문 - 코드로 배우는 스프링부트, 웹 MVC, DB 접근 기술 강의를 정리한 내용입니다.</p></strong></p>
<h2 id="aop가-필요한-상황">AOP가 필요한 상황</h2>
<ul>
<li>모든 메소드의 호출 시간을 측정하고 싶다면? </li>
<li>공통 관심 사항(cross-cutting concern) vs 핵심 관심 사항(core concern)</li>
<li>회원 가입 시간, 회원 조회 시간을 측정하고 싶다면?
<img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/61fe902d-5897-41a9-a0af-fdfded9f5b56/image.png" /></li>
</ul>
<h4 id="memberservice-회원-조회-시간-측정-추가">MemberService 회원 조회 시간 측정 추가</h4>
<pre><code class="language-java">// 회원 가입
    public Long join(Member member) {
        // 같은 이름이 있는 중복회원 X
        // extract method -&gt; Ctrl + Alt + m

        long start = System.currentTimeMillis();

        try{
            validateDuplicateMember(member);
            memberRepository.save(member);
            return member.getId();
        }finally {
            long finish = System.currentTimeMillis();
            long timeMs = finish - start;
            System.out.println(&quot;join = &quot; + timeMs + &quot;ms&quot;);
        }

    }
 // 전체회원 조회
    public List&lt;Member&gt; findMembers(){
        long start = System.currentTimeMillis();

        try{
            return memberRepository.findAll();
        }finally {
            long finish = System.currentTimeMillis();
            long timeMs = finish - start;
            System.out.println(&quot;findMembers &quot; + timeMs + &quot;ms&quot;);
        }

    }</code></pre>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/3f6ea8ce-0aca-4b61-8e04-b5ac992ac260/image.png" /></p>
<p>문제</p>
<ul>
<li>회원가입, 회원 조회에 시간을 측정하는 기능은 <strong>핵심 관심 사항</strong>이 아니다.</li>
<li>시간을 측정하는 로직은 <strong>공통 관심 사항</strong>이다.</li>
<li>시간을 측정하는 로직과 핵심 비지니스의 로직이 섞여서 <strong>유지보수가 어렵다.</strong></li>
<li>시간을 측정하는 로직을 별도의 공통 로직으로 만들기 매우 어렵다.</li>
<li>시간을 측정하는 로직을 변경할 때 모든 로직을 찾아가면서 변경해야 한다.</li>
</ul>
<h2 id="aop-적용">AOP 적용</h2>
<ul>
<li><strong><code>AOP</code></strong> : Aspect Oriented Programming</li>
<li>공통 관심 사항(cross-cutting concern) vs 핵심 관심 사항(core concern) 분리
<img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/1c342f36-514c-488c-9c52-f94496cb6c05/image.png" /></li>
</ul>
<h4 id="시간-측정-aop-등록">시간 측정 AOP 등록</h4>
<pre><code class="language-java">package hello.hellospring.aop;

import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;

@Aspect
// @Component 로 Component scan 되도록 해도 가능하지만
// 특별히 AOP 를 쓰고 있다는 것을 인식시키기 위해 config 에서 bean 등록
public class TimeTraceAop {

    //타겟팅
    @Around(&quot;execution(* hello.hellospring..*(..))&quot;)
    public Object execute(ProceedingJoinPoint joinPoint) throws Throwable {
        long start = System.currentTimeMillis();
        //어떤 메소드를 call 하는지
        System.out.println(&quot;START: &quot; + joinPoint.toString());
        try{
            return joinPoint.proceed();
        }finally {
            long finish = System.currentTimeMillis();
            long timeMs = finish - start;
            System.out.println(&quot;END: &quot; + joinPoint.toString() + &quot; &quot; + timeMs + &quot;ms&quot;);
        }
    }
}</code></pre>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/33c75651-c056-4888-ab37-8a49757bd492/image.png" /></p>
<p>해결</p>
<ul>
<li>회원가입, 회원 조회등 핵심 관심사항과 시간을 측정하는 공통 관심 사항을 분리한다.</li>
<li>시간을 측정하는 로직을 별도의 공통 로직으로 만들었다.</li>
<li>핵심 관심 사항을 깔끔하게 유지할 수 있다.</li>
<li>변경이 필요하면 이 로직만 변경하면 된다.</li>
<li>원하는 적용 대상을 선택할 수 있다.</li>
</ul>
<h2 id="스프링-aop-동작-방식-설명">스프링 AOP 동작 방식 설명</h2>
<h4 id="aop-적용-전-의존관계">AOP 적용 전 의존관계</h4>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/3ea2a131-4d4e-4682-80f0-5d86e1d2449d/image.png" /></p>
<h4 id="aop-적용-후-의존관계">AOP 적용 후 의존관계</h4>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/801006fd-9d95-43c3-bf03-d403c4649f00/image.png" /></p>
<p>가짜 memberService (프록시)를 만들어서 joinPoint.proceed() 로 실제 memberService 호출</p>
<pre><code class="language-java">System.out.println(&quot;memberService =&quot; + memberService.getClass());</code></pre>
<p>로 변경하여 확인해보면
<img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/2ad029ee-dd35-449a-aba8-a758be1cba2e/image.png" /></p>
<p>class명이 memberService가 아닌 추가적으로 문자가 붙어있는 것을 확인할 수 있다.</p>
<h4 id="aop-적용-전-전체-그림">AOP 적용 전 전체 그림</h4>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/4dd8b544-c99c-40fc-88b0-de5dcb72d31d/image.png" /></p>
<h4 id="aop-적용-후-전체-그림">AOP 적용 후 전체 그림</h4>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/d4096995-00da-41c4-88f9-c77502eec707/image.png" /></p>