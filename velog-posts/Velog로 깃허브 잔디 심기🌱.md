<h2 id="1-깃허브에-레포지토리-생성">1. 깃허브에 레포지토리 생성</h2>
<p>❗❗ <strong>Public repository로 생성하자!</strong></p>
<h2 id="2-폴더링-및-파일-생성">2. 폴더링 및 파일 생성</h2>
<p>생성한 리포지토리에 아래와 같은 구조로 파일을 만들어 준다.</p>
<pre><code>your-github-repo/
├── .github/
│   └── workflows/
│       └── update_blog.yml
├── scripts/
│   └── update_blog.py
└── ...</code></pre><h2 id="3-update_blogyml">3. update_blog.yml</h2>
<p><strong>update_blog.yml</strong> 파일에 github action 코드를 작성해준다.</p>
<p><strong>❗❗ 자신의 깃허브 주소에 맞게 편집해주기</strong></p>
<ul>
<li>cron: '0 0 * * *' 는 미국시간 00시 (자정)에 자동으로 업로드하겠다는 뜻이다.
미국시간을 맞춰서 업로드하고 싶은 시간으로 설정해놓자.<pre><code class="language-yml">name: Update Blog Posts

</code></pre>
</li>
</ul>
<p>on:
  push:
      branches:
        - main  # 또는 워크플로우를 트리거하고 싶은 브랜치 이름
  schedule:
    - cron: '0 6 * * *'  # 매일 오후 7시에 업로드</p>
<p>jobs:
  update_blog:
    runs-on: ubuntu-latest</p>
<pre><code>steps:
- name: Checkout
  uses: actions/checkout@v2

- name: Push changes
  run: |
    git config --global user.name '깃허브 아이디'
    git config --global user.email '깃허브 이메일'
    git push https://${{ secrets.GH_PAT_HISU }}@github.com/gmltn9233/velog_hisu.git 

    # 깃허브 아이디
    # 깃허브 이메일
    # https://${{SecretKey}}@github.com/깃허브 아이디/레포지토리이름.git

- name: Set up Python
  uses: actions/setup-python@v2
  with:
    python-version: '3.x'

- name: Install dependencies
  run: |
    pip install feedparser gitpython

- name: Run script
  run: python scripts/update_blog.py</code></pre><pre><code>## 4. update_blog.py

**update_blog.py** 파일에 파이썬 코드를 작성해준다.

**❗❗ 자신의 벨로그 아이디에 맞게 수정한다** 
```python
import feedparser
import git
import os

# 벨로그 RSS 피드 URL
# example : rss_url = 'https://api.velog.io/rss/@gmltn9233'
rss_url = 'https://api.velog.io/rss/@[벨로그 아이디]'

# 깃허브 레포지토리 경로

repo_path = '.'

# 'velog-posts' 폴더 경로
posts_dir = os.path.join(repo_path, 'velog-posts')

# 'velog-posts' 폴더가 없다면 생성
if not os.path.exists(posts_dir):
    os.makedirs(posts_dir)

# 레포지토리 로드
repo = git.Repo(repo_path)

# RSS 피드 파싱
feed = feedparser.parse(rss_url)

# 각 글을 파일로 저장하고 커밋
for entry in feed.entries:
    # 파일 이름에서 유효하지 않은 문자 제거 또는 대체
    file_name = entry.title
    file_name = file_name.replace('/', '-')  # 슬래시를 대시로 대체
    file_name = file_name.replace('\\', '-')  # 백슬래시를 대시로 대체
    # 필요에 따라 추가 문자 대체
    file_name += '.md'
    file_path = os.path.join(posts_dir, file_name)

    # 파일이 이미 존재하지 않으면 생성
    if not os.path.exists(file_path):
        with open(file_path, 'w', encoding='utf-8') as file:
            file.write(entry.description)  # 글 내용을 파일에 작성

        # 깃허브 커밋
        repo.git.add(file_path)
        repo.git.commit('-m', f'Add post: {entry.title}')

# 변경 사항을 깃허브에 푸시
repo.git.push()</code></pre><h2 id="5-pat-권한-받기">5. PAT 권한 받기</h2>
<ul>
<li>Github 계정 - Settings - Developer Settings</li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/33529d2e-f645-4a9b-9e0e-dcf044db9a8b/image.png" /></p>
<ul>
<li>Personal access tokens(classic) - Generate New Token</li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/e5b799fb-f3c2-4c19-82a6-faea29287d3b/image.png" /></p>
<ul>
<li>Note에는 토큰 이름 작성, rep, workflow 클릭 - Generate new token</li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/e9e5cded-d277-4c42-9684-b711b9324a7e/image.png" /></p>
<ul>
<li><p>받은 토큰 복사 (❗ 한번밖에 볼 수 없으니 꼭 복사하자)</p>
</li>
<li><p>위에서 생성한 레포지토리 - Settings - Secrets and variables - Actions - New Repository Secret</p>
</li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/08edf779-f08e-45fb-beea-dccaeb69c8fe/image.png" /></p>
<ul>
<li>Name : 원하는 키 이름(update_blog.yml 에 들어가는 키 이름) / 발급받은 토큰 붙여넣기</li>
</ul>
<h2 id="6-커밋-또는-지정한-시간까지-기다리기">6. 커밋 또는 지정한 시간까지 기다리기</h2>
<p>커밋 또는 지정한 시간이 될때 자동으로 블로그가 업로드 된다!
혹시 오류가 난다면 권한 문제일 확률이 높으니 나 이외의 모든 사용자에게 권한을 줘버리자.</p>
<ul>
<li>Settings - Actions - general - Actions permissions 권한 허용해주기 후 저장</li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/141b23d1-ab0f-47ea-abb9-c9b86c6b3b61/image.png" /></p>
<h2 id="📚-참고">📚 참고</h2>
<p><a href="https://velog.io/@sooozi/velog%EC%99%80-github-%EC%97%B0%EB%8F%99-%EB%B2%A8%EB%A1%9C%EA%B7%B8-%EA%B8%80%EC%93%B0%EA%B3%A0-%EC%9E%94%EB%94%94%EC%8B%AC%EA%B8%B0">https://velog.io/@sooozi/velog와-github-연동-벨로그-글쓰고-잔디심기</a>
<a href="https://velog.io/@rimgosu/velog-%EA%B8%80-%EC%9E%91%EC%84%B1-%EC%8B%9C-%EC%9E%90%EB%8F%99%EC%9C%BC%EB%A1%9C-github%EC%97%90-%EC%BB%A4%EB%B0%8B%ED%95%98%EA%B8%B0">https://velog.io/@rimgosu/velog-글-작성-시-자동으로-github에-커밋하기</a></p>