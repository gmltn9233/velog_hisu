<p><strong><p style="color: red;">본 내용은 김영한님의 스프링입문 - 코드로 배우는 스프링부트, 웹 MVC, DB 접근 기술 강의를 정리한 내용입니다.</p></strong></p>
<h2 id="jpa">JPA</h2>
<ul>
<li>JPA는 기존의 반복 코드는 물론이고, 기본적인 SQL도 JAP가 직접 만들어서 실행해준다.</li>
<li>JPA를 사용하면, SQL과 데이터 중심의 설게에서 객체 중심의 설계로 패러다임을 전환을 할 수 있다.</li>
<li>JPA를 사용하면 개발 생산성을 크게 높일 수 있다.</li>
</ul>
<h4 id="buildgradle-파일에-jpah2-데이터베이스-관련-라이브러리-추가">build.gradle 파일에 JPA,h2 데이터베이스 관련 라이브러리 추가</h4>
<pre><code>dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'
    implementation 'org.springframework.boot:spring-boot-starter-web'
// implementation 'org.springframework.boot:spring-boot-starter-jdbc'
    implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
    runtimeOnly 'com.h2database:h2'
    testImplementation('org.springframework.boot:spring-boot-starter-test') {
        exclude group: 'org.junit.vintage', module: 'junit-vintage-engine'
    }
}</code></pre><p>```spring-boot-starter-data-jpa``는 내부에 jdbc 관련 라이브러리를 포함한다. 따라서 jdbc는 제거해도 된다.</p>
<h4 id="스프링-부트에-jpa-설정-추가">스프링 부트에 JPA 설정 추가</h4>
<p><code>resources/application.propeties</code></p>
<pre><code>spring.datasource.url=jdbc:h2:tcp://localhost/~/test
spring.datasource.driver-class-name=org.h2.Driver
spring.datasource.username=sa
spring.jpa.show-sql=true
spring.jpa.hibernate.ddl-auto=none</code></pre><blockquote>
<p><strong>주의!</strong>: 스프링부트 2.4부터는 <code>spring.datasource.username=sa</code> 를 꼭 추가해주어야 한다. 그렇지않으면 오류가 발생한다.</p>
</blockquote>
<ul>
<li><code>show-sql</code>: JPA가 생성하는 SQL을 출력한다.</li>
<li><code>ddl-auto</code>: JPA는 테이블을 자동으로 생성하는 기능을 제공하는데 <code>none</code>을 사용하면 해당 기능을 끈다.<ul>
<li><code>create</code>를 사용하면 엔티티 정보를 바탕으로 테이블도 직접 생성해준다.</li>
</ul>
</li>
</ul>
<h4 id="jpa-엔티티-매핑">JPA 엔티티 매핑</h4>
<pre><code class="language-java">package hello.hellospring.domain;

import jakarta.persistence.Entity;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.GenerationType;
import jakarta.persistence.Id;

@Entity
public class Member {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}</code></pre>
<h4 id="jpa-회원-리포지토리">JPA 회원 리포지토리</h4>
<pre><code class="language-java">package hello.hellospring.repository;

import hello.hellospring.domain.Member;
import jakarta.persistence.EntityManager;
import java.util.List;
import java.util.Optional;

public class JpaMemberRepository implements MemberRepository{

    private final EntityManager em;
    public JpaMemberRepository(EntityManager em){
        this.em = em;
    }

    @Override
    public Member save(Member member) {
        em.persist(member);
        return member;
    }

    @Override
    public Optional&lt;Member&gt; findById(Long id) {
        Member member = em.find(Member.class, id);
        return Optional.ofNullable(member);
    }

    @Override
    public Optional&lt;Member&gt; findByName(String name) {
        List&lt;Member&gt; result = em.createQuery(&quot;select m from Member m where m.name = :name&quot;, Member.class)
            .setParameter(&quot;name&quot;,name)
            .getResultList();

        return result.stream().findAny();
    }

    @Override
    public List&lt;Member&gt; findAll() {
        return em.createQuery(&quot;select m from Member m&quot;, Member.class)
            .getResultList();
    }
}</code></pre>
<h4 id="서비스-계층에-트랜잭션-추가">서비스 계층에 트랜잭션 추가</h4>
<pre><code>import org.springframework.transaction.annotation.Transactional
@Transactional
public class MemberService {}</code></pre><ul>
<li><code>org.springframework.transaction.annotation.Transactional</code>을 사용하자.</li>
<li>스프링은 해당 클래스의 메서드를 실행할 때 트랜잭션을 시작하고, 메서드가 정상 종료되면, 트랜잭션을 커밋한다. 만약 런타임 예외가 발생하면 롤백한다.</li>
<li><strong>JPA를 통한 모든 데이터 변경은 트랜잭션 안에서 실행해야 한다.</strong></li>
</ul>
<h4 id="jpa를-사용하도록-스프링-설정-변경">JPA를 사용하도록 스프링 설정 변경</h4>
<pre><code class="language-java">package hello.hellospring;

import hello.hellospring.repository.JpaMemberRepository;
import hello.hellospring.repository.MemberRepository;
import hello.hellospring.service.MemberService;
import jakarta.persistence.EntityManager;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class SpringConfig {

    private EntityManager em;

    @Autowired
    public SpringConfig(EntityManager em){
        this.em = em;
    }

    @Bean
    public MemberService memberService() {
        return new MemberService(memberRepository());
    }

    @Bean
    public MemberRepository memberRepository() {
//        return new MemoryMemberRepository();
//        return new JdbcMemberRepository(dataSource);
//        return new JdbcTemplateMemberRepository(dataSource);
        return new JpaMemberRepository(em);
    }
}</code></pre>
<h2 id="스프링-데이터-jpa">스프링 데이터 JPA</h2>
<p>스프링 부트와 JPA만 사용해도 개발 생산성이 정말 많이 증가하고, 개발해야할 코드도 확연히 줄어듭니다. 여기에 스프링 데이터 JPA를 사용하면, 기존의 한계를 넘어 마치 마법처럼, 리포지토리에 구현 클래스 없이 인터페이스 만으로 개발을 완료할 수 있습니다. 그리고 반복 개발해온 기본 CRUD 기능도 스프링 데이터 JPA가 모두 제공합니다.
스프링 부트와 JPA라는 기반 위에, 스프링 데이터 JPA라는 환상적인 프레임워크를 더하면 개발이 정말 즐거워집니다.
지금까지 조금이라도 단순하고 반복이라 생각했던 개발 코드들이 확연하게 줄어듭니다. 따라서 개발자는 핵심 비즈니스 로직을 개발하는데, 집중할 수 있습니다.
실무에서 관계형 데이터베이스를 사용한다면 스프링 데이터 JPA는 이제 선택이 아니라 필수 입니다.</p>
<blockquote>
<p><strong>주의: 스프링 데이터 JPA는 JPA를 편리하게 사용하도록 도와주는 기술입니다. 따라서 JPA를 먼저 학습한후에 스프링 데이터 JPA를 학습해야 합니다.</strong></p>
</blockquote>
<h4 id="스프링-데이터-jpa-회원-리포지토리">**스프링 데이터 JPA 회원 리포지토리</h4>
<pre><code class="language-java">package hello.hellospring.repository;

import hello.hellospring.domain.Member;
import org.springframework.data.jpa.repository.JpaRepository;
import java.util.Optional;

public interface SpringDataJpaMemberRepository extends JpaRepository&lt;Member, Long&gt;,
    MemberRepository {

    Optional&lt;Member&gt; findByName(String name);
}</code></pre>
<h4 id="스프링-데이터-jpa-회원-리포지토리를-사용하도록-스프링-설정-변경">스프링 데이터 JPA 회원 리포지토리를 사용하도록 스프링 설정 변경</h4>
<pre><code class="language-java">package hello.hellospring;

import hello.hellospring.repository.MemberRepository;
import hello.hellospring.service.MemberService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class SpringConfig {

    private final MemberRepository memberRepository;

    @Autowired
    public SpringConfig(MemberRepository memberRepository) {
        this.memberRepository = memberRepository;
    }


    @Bean
    public MemberService memberService() {
        return new MemberService(memberRepository);
    }


//    @Bean
//    public MemberRepository memberRepository() {
////        return new MemoryMemberRepository();
////        return new JdbcMemberRepository(dataSource);
////        return new JdbcTemplateMemberRepository(dataSource);
////        return new JpaMemberRepository(em);
//
//    }
}</code></pre>
<h4 id="스프링-데이터-jpa-제공-클래스">스프링 데이터 JPA 제공 클래스</h4>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/2c6b6ebf-1f55-4c1a-a594-023ce7161ec5/image.png" /></p>
<p><strong>스프링 데이터 JPA 제공 기능</strong></p>
<ul>
<li>인터페이스를 통한 기본적인 CRUD</li>
<li><code>findByName()</code>,<code>findByEmail()</code>처럼 메서드 이름만으로 조회 기능 제공</li>
<li>페이징 기능 자동 제공</li>
</ul>
<blockquote>
<p>참고: 실무에서는 JPA와 스프링 데이터 JPA를 기본으로 사용하고, 복잡한 동적 쿼리는 Querydsl이라는 라이브러리를 사용하면 된다. Querydsl을 사용하면 쿼리도 자바 코드로 안전하게 작성할 수 있고, 동적 쿼리도 편리하게 작성할 수 있다. 이 조합으로 해결하기 어려운 쿼리는 JPA가 제공하는 네이티브 쿼리를 사용하거나, 앞서 학습한 스프링 JdbcTemplate를 사용하면 된다.</p>
</blockquote>