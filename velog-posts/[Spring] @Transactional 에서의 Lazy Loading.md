<h2 id="🎯현-상황">🎯현 상황</h2>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/3b213c8b-fe3b-44ff-9eaf-0bd39e91d85d/image.png" /></p>
<p>기이수 파일 업로드시 파일을 선택하지 않고 업로드시 아래와 같은 에러가 발생하였다.</p>
<h3 id="에러메시지">에러메시지</h3>
<p><strong><code>LazyInitializationException</code></strong>이 발생하였다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/cd5d2db2-0c25-4027-b422-9b19cdb46642/image.png" /></p>
<h3 id="lazyinitializationexception이란">LazyInitializationException이란?</h3>
<p>JPA에서 관리하는 세션이 종료 된 후 관계가 설정된 엔티티를 참조하려고 할 때 발생한다.</p>
<ul>
<li>JPA에서 세션이란 <strong>영속성 컨텍스트</strong>를 의미한다.</li>
</ul>
<hr />
<h2 id="🔍원인">🔍원인</h2>
<h3 id="completedcourseservice">CompletedCourseService</h3>
<pre><code class="language-java">@Transactional
    public void extractExcelFile(MultipartFile file, UserDetails userDetails) throws IOException, FileException { //엑셀 데이터 추출
        List&lt;CompletedCoursesDomain&gt; dataList = new ArrayList&lt;&gt;();
        String extension = FilenameUtils.getExtension(file.getOriginalFilename());

        ...

        UserDomain userDomain = userDao.findByStudentId(studentId).get();

        ...

        for (int i = FIRST_ROW; i &lt; worksheet.getPhysicalNumberOfRows(); i++) { //데이터 추출
            ...

            CoursesDomain coursesDomain = coursesDao.findByCourseId(
                courseId);// 학수번호를 기반으로 Courses 테이블 검색

            CompletedCoursesDomain data = CompletedCoursesDomain.builder().userDomain(userDomain)
                .coursesDomain(coursesDomain).year(year).semester(semester).build();

            completedCoursesDao.save(data);
        }
    }</code></pre>
<p>해당 코드에서 <strong><code>@Transactional</code></strong> 로 인해 트랜잭션 중 <strong>Exception</strong>이 발생 시 <strong>Rollback</strong>이 일어난다.</p>
<p><strong>Rollback</strong>이 발생하면 해당 트랜잭션에서 관리하던 <strong>영속성 컨텍스트가 비워지게 된다.</strong></p>
<p>해당 코드에서 <strong>지연 로딩(Lazy Loading)</strong>이 필요한 엔티티(UserDomain)에 접근하려고 할 때 <strong>영속성 컨테스트</strong>가 비워졌기 때문에 <code>LazyInitializationException</code>가 발생하게 되는것이다.</p>
<h3 id="lazyloading">LazyLoading</h3>
<blockquote>
<p>연관된 엔티티의 데이터는 <strong>실제로 사용될 때 까지 로딩을 지연</strong>시키는 방법이다.</p>
</blockquote>
<p><strong>프록시 객체(HibervateProxy)</strong>를 실제 엔티티를 대신해서 반환하다 실제 데이터가 필요한 시점에서 DB에서 데이터를 가져온다.</p>
<ul>
<li>연관 데이터를 필요없는 경우에도 항상 조회하지 않아 성능 향상에 도움이 된다.</li>
</ul>
<hr />
<h2 id="💡해결방법">💡해결방법</h2>
<p><strong><code>@Transactional</code></strong> 을 데이터에 접근하는 부분에만 선언하여 영속성을 따로 관리해주도록 메소드를 분리하였다.</p>
<pre><code class="language-java">public void extractExcelFile(MultipartFile file, UserDetails userDetails)
        throws IOException, FileException { //엑셀 데이터 추출
        //업로드 파일 검증

        //DB에 해당 사용자의 기이수 과목 정보 확인

        //엑셀 내용 검증

        //데이터 추출
        ExtractData(worksheet, dataFormatter, user);
    }</code></pre>
<pre><code class="language-java">@Transactional
    public void ExtractData(Sheet worksheet, DataFormatter dataFormatter, UserDomain userDomain) {

        for (int i = FIRST_ROW; i &lt; worksheet.getPhysicalNumberOfRows(); i++) { //데이터 추출
            ...

            CoursesDomain coursesDomain = coursesDao.findByCourseId(
                courseId);// 학수번호를 기반으로 Courses 테이블 검색
            if (coursesDomain == null) {
                continue;
            }

            CompletedCoursesDomain data = CompletedCoursesDomain.builder().userDomain(userDomain)
                .coursesDomain(coursesDomain).year(year).semester(semester).build();

            completedCoursesDao.save(data);
        }
    }</code></pre>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/091b5ee3-8aa2-419c-b94d-49d694cfcdbb/image.png" /></p>
<p><strong>해결 완료!</strong></p>