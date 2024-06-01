<h2 id="회원가입-로직">회원가입 로직</h2>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/7ea346e0-0810-43ae-8023-082be4c4f420/image.png" /></p>
<hr />
<h2 id="joindto">JoinDto</h2>
<ul>
<li>JoinDto</li>
</ul>
<pre><code class="language-java">package com.example.jwtprac.dto;

import lombok.Getter;
import lombok.Setter;

@Setter
@Getter
public class JoinDTO {

    private String username;
    private String password;

}</code></pre>
<h2 id="userrepository">UserRepository</h2>
<ul>
<li>UserRepository</li>
</ul>
<pre><code class="language-java">public interface UserRepository extends JpaRepository&lt;UserEntity, Integer&gt; {

    Boolean existsByUsername(String username);
}</code></pre>
<p>아이디 중복방지를 위해 JPA의 exists를 활용하여 existsByUsername 메소드를 만든다.</p>
<hr />
<h2 id="joinservice">JoinService</h2>
<ul>
<li>JoinService</li>
</ul>
<pre><code class="language-java">@Service
public class JoinService {

    private final UserRepository userRepository;
    private final BCryptPasswordEncoder bCryptPasswordEncoder;

    public JoinService(UserRepository userRepository, BCryptPasswordEncoder bCryptPasswordEncoder) {

        this.userRepository = userRepository;
        this.bCryptPasswordEncoder = bCryptPasswordEncoder;
    }

    public void joinProcess(JoinDTO joinDTO) {
        // DTO로부터 username, password를 전달받는다.
        String username = joinDTO.getUsername();
        String password = joinDTO.getPassword();

        Boolean isExist = userRepository.existsByUsername(username);

        if (isExist) { // 존재하는 경우 return(회원가입x)

            return;
        }

        UserEntity data = new UserEntity();

        data.setUsername(username);
        //비밀번호 암호화
        data.setPassword(bCryptPasswordEncoder.encode(password));
        data.setRole(&quot;ROLE_ADMIN&quot;);

        //유저저장
        userRepository.save(data);
    }
}</code></pre>
<p>서비스단에서 회원가입하려는 유저의 이름이 중복인지 확인 한 후 회원가입을 진행한다.</p>
<hr />
<h2 id="joincontroller">JoinController</h2>
<ul>
<li><p>JoinController</p>
<pre><code class="language-java">@Controller
@ResponseBody
public class JoinController {

  private final JoinService joinService;

  public JoinController(JoinService joinService) {

      this.joinService = joinService;
  }

  @PostMapping(&quot;/join&quot;)
  public String joinProcess(JoinDTO joinDTO) {

      System.out.println(joinDTO.getUsername());
      joinService.joinProcess(joinDTO);

      return &quot;ok&quot;;
  }
}</code></pre>
</li>
</ul>
<p>회원가입이 완료되면 &quot;ok&quot; 를 리턴한다.</p>
<h2 id="요청보내기">요청보내기</h2>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/111246f6-058f-49fc-b68f-be10df4c6cb8/image.png" /></p>
<p>POSTMAN을 사용해 body 에 form data 형식으로 username과 password를 post 요청보낸다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/35e03767-389c-4d86-96cc-575fc41506c5/image.png" /></p>
<p>DB를 확인해보면 실제로 저장이 이루어진 모습을 확인할 수 있다.</p>
<hr />
<p>출처: <a href="https://www.youtube.com/watch?v=yNACbJF4uoo&amp;list=PLJkjrxxiBSFCcOjy0AAVGNtIa08VLk1EJ&amp;index=6">개발자 유미</a></p>