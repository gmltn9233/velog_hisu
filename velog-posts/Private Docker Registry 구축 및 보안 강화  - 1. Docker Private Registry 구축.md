<h2 id="1-docker-private-registry-구축">1. Docker Private Registry 구축</h2>
<h3 id="11-registry-컨테이너-실행">1.1 Registry 컨테이너 실행</h3>
<pre><code class="language-sh">docker run -d -p 5000:5000 --restart=always --name private-registry registry:2</code></pre>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/6cfcfcfe-ced4-4432-a8e6-01daee3d6657/image.png" /></p>
<p>5000번 포트가 이미 사용중이라는 에러가 발생한다.</p>
<ul>
<li>현재 열려있는 포트 확인 및 닫기
<img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/1965846e-99a1-42e7-b5b4-000ff730832d/image.png" /></li>
</ul>
<p>현재 complex-main이 5000 port를 사용중이므로 포트를 닫아준다.(13704:PID)</p>
<pre><code class="language-sh">kill -9 13704</code></pre>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/4fa31ca0-ad81-4809-8e83-6b4cf510e8b8/image.png" /></p>
<p>하지만 다시 5000 port를 확인하면 다시 살아나있는것을 볼 수 있다.
complex-main 은 Airplay를 수신하는 port로 현재 필요없으니 비활성화 해준다.
(시스템 설정 - airplay 검색 - Airplay 수신모드 끄기)</p>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/d0072de2-5dc6-4ab4-a801-a2d7fcee3719/image.png" /></p>
<p>5000 포트에 아무 출력이 없는 것을 확인
<img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/f2f7a9ab-f81c-483e-a969-2690d8d8a79b/image.png" /></p>
<h3 id="12-이미지-업로드">1.2 이미지 업로드</h3>
<h4 id="1-기본-이미지-다운로드-nginx">1. 기본 이미지 다운로드 (nginx)</h4>
<pre><code class="language-sh">docker pull nginx:latest</code></pre>
<h4 id="2-로컬-private-registry로-태그-변경">2. 로컬 Private Registry로 태그 변경</h4>
<pre><code class="language-sh">docker tag nginx:latest localhost:5000/nginx</code></pre>
<h4 id="3-이미지-private-registry에-업로드">3. 이미지 Private Registry에 업로드</h4>
<pre><code class="language-sh">docker push localhost:5000/nginx</code></pre>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/a59273b4-29a4-4804-aa66-e61c5fcc0211/image.png" /></p>
<h4 id="4-저장된-이미지-목록-확인">4. 저장된 이미지 목록 확인</h4>
<pre><code class="language-sh">curl http://localhost:5000/v2/_catalog</code></pre>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/a9544b1d-5fe9-470d-a73a-9106ecd1015d/image.png" /></p>
<h4 id="5-private-registry-이미지-다운로드-확인">5. Private Registry 이미지 다운로드 확인</h4>
<ul>
<li><p>기존 이미지 삭제</p>
<pre><code class="language-sh">docker rmi localhost:5000/nginx</code></pre>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/3110d365-7c11-4a00-9fc1-6c0f842b99fb/image.png" /></p>
</li>
<li><p>Private Registry에서 다운로드</p>
<pre><code class="language-sh">docker pull localhost:5000/nginx</code></pre>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/ab7771d4-622f-45b6-9d93-2753715885ef/image.png" /></p>
</li>
<li><p>이미지가 정상적으로 다운로드 되었는지 확인</p>
<pre><code class="language-sh">docker images</code></pre>
</li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/14def631-6d4e-431a-9ad0-0ee6923b39db/image.png" /></p>
<h2 id="참고-자료">참고 자료</h2>
<p><a href="https://jongsky.tistory.com/41">https://jongsky.tistory.com/41</a></p>