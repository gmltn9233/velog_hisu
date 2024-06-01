<h2 id="dp다이나믹-프로그래밍-이란">DP,다이나믹 프로그래밍 이란?</h2>
<p>DP, 즉 다이나믹 프로그래밍(또는 동적 계획법)은 복잡한 문제를 더 작은 하위 문제로 나누어 해결하는 알고리즘 설계 기법이다.</p>
<h2 id="dp의-특징">DP의 특징</h2>
<h3 id="dp에서-중요한-점">DP에서 중요한 점?</h3>
<p>점화식을 잘 짜야 한다. 만들어진 점화식을 기반으로 문제를 풀어야 함.</p>
<h3 id="그렇다면-어떻게-점화식을-짜야-할까">그렇다면 어떻게 점화식을 짜야 할까?</h3>
<blockquote>
<p>모든 <strong>경우의 수</strong>를 생각 + <strong>메모이제이션</strong> 
-&gt; 어떤 idx에서 어떤 경우의 수가 있나를 늘 생각해야 한다.</p>
</blockquote>
<h3 id="메모이제이션">메모이제이션?</h3>
<p><strong>메모이제이션</strong> : 이미 계산한 값을 저장해 두번이상 벌어지는 로직에 대해 그 값을 쓰는 것</p>
<blockquote>
<p>맵이든 셋이든 배열이든 계산된 값을 저장해 놓아서 다시 한번 계산하는 것을 방지
-&gt; 이 <strong>상태값</strong>을 정의하는 것이 DP의 핵심임</p>
</blockquote>
<p>예를 들어 <code>DP[idx][turn]</code> 배열이 있을때, idx와 turn이라는 상태값이 문제를 푸는데 중요한 키이기 때문에 저렇게 선언해서 사용하는 것.</p>
<p>하지만 어떤 배열을 어떤 차원으로 설정해야 할 지는 문제에 달려있음.</p>
<h2 id="dp의-조건">DP의 조건</h2>
<p>DP는 사실 어느정도의 조건을 만족시켜야 쓸 수 있는 알고리즘이다.</p>
<ol>
<li><strong>참조투명성</strong>을 가져야 하며 <strong>입력을 제외한 외적 요소에 결과값이 영향을 미치지 않아야 한다.</strong></li>
<li><strong>Overlapping Subproblem</strong>: <strong>겹치는 부분 문제</strong>가 존재해야 한다.</li>
<li><strong>Optimal Substructure</strong>: <strong>최적 부분구조</strong>를 가지고 있어야 한다.</li>
</ol>
<p>1번은 보통의 코딩테스트 문제라면 다 지켜서 준다. 참고로 <strong>참조투명성</strong>이란 외부 전역변수에 영향을 끼치지 않아야 하고 입력에 의해서만 출력이 결정되는것을 말한다.</p>
<p>2번은 아래와 같은 구조를 말한다.
<img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/33fc0674-8405-4fa6-9a73-b5cc62575af6/image.png" /></p>
<p>위의 예시는 피보나치수열인데, (피보나치: f(n) = f(n-1)+f(n-2) ) 계산된 값이 중복해서 겹치는 것을 볼 수 있다. 이러한 구조를 가지는 구조를 말한다.</p>
<p>3번은 지금의 최적해가 결론적으로 전체의 최적해가 되는것을 의미한다.</p>
<h3 id="실제로-생각하는-방식">실제로 생각하는 방식</h3>
<p>하지만 코딩테스트를 풀 때 위의 3가지를 생각하기는 어렵다.</p>
<p>아래와 같이 생각해보자.</p>
<ol>
<li>이거 완전탐색인데?</li>
<li>경우의 수가 너무 큰데?</li>
<li>메모제이션 가능한가? (배열로 담을 수 있나?)</li>
</ol>
<p>위의 방식으로 실제 DP 문제를 풀어보자.</p>
<h2 id="dp-문제">DP 문제</h2>
<h3 id="백준-2240-자두나무">백준 2240 자두나무</h3>
<p><a href="https://www.acmicpc.net/problem/2240">2240 자두나무</a></p>
<pre><code class="language-c">#include&lt;bits/stdc++.h&gt; 
using namespace std;
int n,m;
int b[1004];
int dp[1004][2][34];
int go(int idx, int tree, int cnt){
    // 기저사례
    if(cnt &lt; 0) return -1e9;
    if(idx == n) return cnt == 0 ? 0 : -1e9;
    // 메모이제이션
    // 탐색했던 값이 있다면 더 이상 하위를 탐색하지 않고 return함 
    int &amp;ret = dp[idx][tree][cnt];
    if(~ret) return ret;
    // 로직
    // ret(현재 dp[idx][tree][cnt])
    // 하위 2가지 경우의 수 중 큰 값 + 현재 떨어지는 나무 위치라면 +1 
    return ret = max(go(idx+1,tree^1,cnt-1),go(idx+1,tree,cnt)) + (tree == b[idx]-1); 
}
int main() {
    //초기화 
    memset(dp,-1,sizeof(dp));
    cin&gt;&gt;n&gt;&gt;m;
    for(int i=0; i&lt;n; i++) cin&gt;&gt;b[i];
    cout&lt;&lt;max(go(0,1,m-1),go(0,0,m));
    return 0;
}</code></pre>
<h4 id="dp의-구조">DP의 구조</h4>
<blockquote>
<p><strong>1. 기저사례
2. 메모이제이션
3. 로직
4. 초기화</strong></p>
</blockquote>
<p><strong>1. 기저사례</strong></p>
<p>완전탐색의 종료조건과 같다. 위의 문제에서는</p>
<ol>
<li>전부 탐색했을때 (idx ==n)</li>
<li>이동 가능 횟수를 벗어났을때 (cnt&lt;0)
이 기저사례가 된다.</li>
</ol>
<p><strong>2. 메모이제이션</strong>
보통 초기화에서 -1로 초기화를 하기 때문에 ret!= -1 을써서 코드와 같이 ~를 통해 구현을 할 수 있다. 값이 있다면 그 값을 return 함으로써 하위 탐색을 진행하지 않아 시간복잡도를 줄인다.</p>
<pre><code class="language-c">// 메모이제이션
    int &amp;ret = dp[idx][tree][cnt];
    if(~ret) return ret;
</code></pre>
<blockquote>
<p>💡 이와같이 DP에서는 어떠한 값을 리턴하기 때문에 <strong>DP의 함수 리턴형은 무조건 void가 아니다!</strong></p>
</blockquote>
<p><strong>3. 로직</strong>
기본적으로 뼈대가 되는 로직을 작성하면 된다. 이 문제에서는 경우의 수가 2가지 이므로 2가지 함수, 즉 완전 탐색하듯이 하면 된다.</p>
<pre><code class="language-c">//로직
    return ret = max(go(idx + 1, tree^1, cnt - 1), go(idx + 1, tree, cnt)) + (tree == b[idx] - 1); </code></pre>
<p><strong>4. 초기화</strong>
초기화는 보통 -1로 하지만 0으로 할 때도 있다. 이외에는</p>
<ul>
<li>최소를 구할때는 최대로 초기화</li>
<li>최대를 구할때는 최소로 초기화</li>
</ul>
<h3 id="백준-15989-123-더하기-4">백준 15989 1,2,3 더하기 4</h3>
<p><a href="https://www.acmicpc.net/problem/15989">15989 1,2,3 더하기 4</a></p>
<pre><code class="language-c">#include&lt;bits/stdc++.h&gt; 
using namespace std;
int t,n;
int dp[10001];
int main() {
    cin&gt;&gt;t;
    dp[0]=1;
    for(int i=1; i&lt;=3; i++){
        for(int j=1; j&lt;=10000; j++){
            if(j-i&gt;=0) dp[j] += dp[j-i];
        }
    }
    while(t--){
        cin&gt;&gt;n;
        cout&lt;&lt;dp[n]&lt;&lt;&quot;\n&quot;;
    }
    return 0;
}</code></pre>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/38414b19-3ee1-4d66-bd6c-6c97d006a4d5/image.png" /></p>
<p>dp[0] = 1 로 만들어 놓는다. 아무것도 추가하지 않는 방법의 수 하나니까.
우선 1으로 구성하는 경우의 수를 생각해보면 
<strong>dp[i] += dp[i-1]</strong> (이전의 수에 1을 더하면 되므로) 이니 1로 구성하는 경우의 수는 모두 1일것이다.
2를 추가해서 구성하는 방법을 생각해보면 <strong>dp[i] += dp[i-2]</strong> (2모자란 수에 2를 더하면 되므로) 이다.
이를 확장해보면 </p>
<pre><code class="language-c">for(int i=1; i&lt;=3; i++){
        for(int j=1; j&lt;=10000; j++){
            if(j-i&gt;=0) dp[j] += dp[j-i];
        }
    }</code></pre>
<p>와 같이 경우의 수를 만들어 줄 수있다.</p>
<h4 id="dp의-종류">DP의 종류</h4>
<p>DP의 종류는 크게 <strong>바텀업</strong>과 <strong>탑다운</strong>이 있다. (상향식,하향식으로도 불린다.)</p>
<p>이전의 자두나무 코드를 살펴보자.</p>
<pre><code class="language-c">int go(int idx, int tree, int cnt){
//기저사례
    if(cnt &lt; 0) return -1e9;
    if(idx == n) return cnt == 0 ? 0 : -1e9;
// 메모이제이션
    int &amp;ret = dp[idx][tree][cnt];
    if(~ret) return ret;  
//로직
    return ret = max(go(idx + 1, tree^1, cnt - 1), go(idx + 1, tree, cnt)) + (tree == b[idx] - 1); 
}</code></pre>
<p>이러한 재귀적인 구조를 가지고 있는 DP를 <strong>탑다운DP</strong> 라고 한다.
이는 <strong>필요한 부분의 배열만을 만든다는 특징</strong>을 가지고 있다.</p>
<h3 id="백준-2748-피보나치-2">백준 2748 피보나치 2</h3>
<p><a href="https://www.acmicpc.net/problem/2748">2748 피보나치 2</a></p>
<pre><code class="language-c">#include&lt;bits/stdc++.h&gt; 
using namespace std;
long long n;
long long dp[91];
long long fibo(long long idx){
    if(idx == 0 ||idx ==1) return idx;
    long long &amp;ret =dp[idx];
    if(ret) return ret;
    return ret = fibo(idx-1)+fibo(idx-2);
}
int main() {
    cin &gt;&gt; n;
    cout&lt;&lt;fibo(n)&lt;&lt;'\n';
    return 0;
}</code></pre>
<p>재귀적인 구조를 가졌고 n부터 시작하니 엄연한 <strong>탑다운 재귀적 풀이</strong>이다. 이 풀이는 재귀적인 방식을 선호하는 개발자에게 적합하며 직관적인 특징을 지닌다. 또한 부분 문제들을 필요할 때 채우는 것 또한 특징이다.</p>
<p>1,2,3 더하기 4 문제를 살펴보자.</p>
<pre><code class="language-c">   dp[0] = 1;
    for(int i = 1; i &lt;= 3; i++){
        for(int j = 1; j &lt;= 10000; j++){
            if(j - i &gt;= 0)dp[j] += dp[j - i];
        }
    }</code></pre>
<p>이 문제는 반복적DP이자, <strong>바텀업 DP</strong>이다. 이 반복적인 DP는 <strong>모든 배열을 다 만든다는 특징</strong>이 있다. 재귀 호출에 대한 오버헤드가 없고 DP라는 자료구조를 채우는 올바른 순서에 따라 채워주어야 한다.</p>