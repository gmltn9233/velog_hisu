<h2 id="이분탐색이진탐색">이분탐색(이진탐색)</h2>
<p>이분탐색은 어떤 경우의 수를 하기에는 애매하거나, n이 무지막지하게 큰 경우 O(logN) 만에 처리해야겠다는 생각이 들 경우 시도해보는 알고리즘이다.</p>
<blockquote>
<p><strong>이진탐색</strong></p>
</blockquote>
<ul>
<li><strong>정렬된 배열</strong>에 특정 원소가 있는지를 확인하는 것을 logN 만에 해결하는 알고리즘</li>
<li>정렬된 배열의 가운데를 살펴보고 찾고자 하는 원소가 그 안에 있는지를 확인한다. 만일 그안에 있다면 멈춘다.</li>
<li>그 외에는 왼쪽에 있는지 오른쪽에 있는지를 판단하여 탐색을 진행한다. 할때마다 절반으로 줄어들기 때문에, 이 알고리즘의 복잡도는 longN 이며, 내장된 라이브러리 lower_bound, upper_bound를 통해 구현할 수도 있다.</li>
</ul>
<h3 id="백준-2776-암기왕">백준 2776 암기왕</h3>
<p><a href="https://www.acmicpc.net/problem/2776">2776 암기왕</a></p>
<pre><code class="language-c">#include&lt;bits/stdc++.h&gt;
using namespace std; 
int t,n,m;
int check(int num, vector&lt;int&gt; &amp;v){
    int l=0, r=v.size()-1;
    while(l&lt;=r){
        int mid = (l+r)/2;
        if(v[mid]==num) return 1;
        else if(v[mid]&lt;num){
            l = mid+1;
        }
        else{
            r= mid-1;
        }
    }
    return 0;
}
int main(){
    cin.tie(NULL); //입출력 묶음 해제
    ios_base::sync_with_stdio(false);
    cin&gt;&gt;t;
    for(int i=0; i&lt;t; i++){
        vector&lt;int&gt; v;
        int num = 0;
        cin&gt;&gt;n;
        for(int j=0; j&lt;n; j++){
            cin&gt;&gt;num;
            v.push_back(num);
        }
        sort(v.begin(),v.end());
        cin&gt;&gt;m;
        for(int j=0; j&lt;m; j++){
            cin&gt;&gt;num;
            cout&lt;&lt;check(num,v)&lt;&lt;&quot;\n&quot;;
        }
    }
    return 0;
}</code></pre>
<p><strong>check 함수 내에서 중간점을 기준으로 l과 r을 갱신해나가면서 검색함.</strong></p>
<h3 id="백준2792-보석상자">백준2792 보석상자</h3>
<p><a href="https://www.acmicpc.net/problem/2792">2792 보석 상자</a></p>
<pre><code class="language-c">#include&lt;bits/stdc++.h&gt;
using namespace std; 
typedef long long ll;
ll n,m,a[300001],ret = 1e18;
bool check(ll mid){
    ll num = 0;
    for(int i=0; i&lt;m; i++){
        num += a[i]/mid;
        if(a[i] % mid) num ++;
    }
    return n&gt;=num;
}
int main(){
    cin&gt;&gt;n&gt;&gt;m;
    ll l = 1, h = 0, mid;
    for(int i=0; i&lt;m; i++){
        cin&gt;&gt;a[i];
        h = max(h,a[i]);
    }
    while(l &lt;= h){
        mid = (l+h)/2;
        if(check(mid)){
            ret = min(ret, mid);
            h = mid - 1;
        }else l = mid +1;
    }
    cout&lt;&lt;ret;
    return 0;
}</code></pre>
<p>원하는 범위 내에서 이게 될까? 하고 테스트해보는것!</p>
<h2 id="lis-최대증가부분수열">LIS, 최대증가부분수열</h2>
<h3 id="백준-11053-가장-긴-증가하는-부분-수열">백준 11053 가장 긴 증가하는 부분 수열</h3>
<p><a href="https://www.acmicpc.net/problem/11053">11053 가장 긴 증가하는 부분 수열</a></p>
<pre><code class="language-c">#include&lt;bits/stdc++.h&gt;
using namespace std; 
int n;
int a[1001],cnt[1001],ret;
int main(){
    cin&gt;&gt;n;
    for(int i=0; i&lt;n; i++){
        cin&gt;&gt;a[i];
    }
    for(int i=0; i&lt;n; i++){
        int maxValue = 0;
        for(int j=0; j&lt; i; j++){
            if(a[j]&lt;a[i] &amp;&amp; maxValue &lt; cnt[j]) maxValue = cnt[j];
        }
        cnt[i] = maxValue + 1;
        ret = max(ret,cnt[i]);
    }
    cout&lt;&lt;ret;
    return 0;
}</code></pre>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/d950989f-91c5-4b9a-a6b5-6d405e7137ab/image.png" /></p>
<blockquote>
<p>이전 원소들을 탐색하며, 현재보다 작으면서 가장 cnt 값이 큰 원소 +1 저장한다.</p>
</blockquote>
<h3 id="백준-14002-가장-긴-증가하는-부분-수열4">백준 14002 가장 긴 증가하는 부분 수열4</h3>
<p><a href="https://www.acmicpc.net/problem/14002">14002 가장 긴 증가하는 부분 수열4</a></p>
<pre><code class="language-c">#include&lt;bits/stdc++.h&gt;
using namespace std; 
int n;
int a[1001],cnt[1001],ret=1,idx;
int prev_list[1001];
vector&lt;int&gt; V;
void go(int idx){
    if(idx == -1) return;
    V.push_back(a[idx]);
    go(prev_list[idx]);
    return;
}
int main(){
    cin&gt;&gt;n;
    for(int i=0; i&lt;n; i++){
        cin&gt;&gt;a[i];
    }
    fill(prev_list,prev_list+1001, -1);
    fill(cnt,cnt+1001,1);
    for(int i=0; i&lt;n; i++){
        for(int j=0; j&lt; i; j++){
            if(a[j]&lt;a[i] &amp;&amp; cnt[i]&lt;cnt[j]+1){
                cnt[i] = cnt[j] + 1;
                prev_list[i] = j;
                if(ret&lt;cnt[i]){
                    ret = cnt[i];
                    idx = i;
                }    
            }
        }
    }
    cout&lt;&lt;ret&lt;&lt;&quot;\n&quot;;
    go(idx);
    for(int i = V.size()-1; i&gt;=0; i--){
        cout&lt;&lt;V[i]&lt;&lt;&quot; &quot;;
    }
    return 0;
}</code></pre>
<p>이전 값을 prev_list에 저장하면서 추적가능하다.</p>
<p>하지만 위 코드의 시간복잡도는 O(n^2)이다. 시간복잡도를 줄여보자.
바로 lower_bound를 통해 탐색을 하면 logN의 시간이 걸리게 된다.</p>
<h3 id="lower_bound">lower_bound</h3>
<p>찾으려는 key 값보다 같거나 큰 숫자가 배열 몇번째에서 처음 등장하는지 찾음</p>
<h4 id="사용법">사용법</h4>
<pre><code class="language-c">#include &lt;iostream&gt;
#include &lt;algorithm&gt;
using namespace std;

int main() {

    vector&lt;int&gt; arr = { 1,2,3,4,5,6,6,6 };
    cout &lt;&lt; &quot;lower_bound(6) : &quot; &lt;&lt; lower_bound(arr.begin(), arr.end(), 6) - arr.begin();

    // 결과 -&gt; lower_bound(6) : 5

    return 0;
}</code></pre>
<h4 id="lower_bound-사용한-코드">lower_bound 사용한 코드</h4>
<pre><code class="language-c">#include &lt;cstdio&gt;
#include &lt;algorithm&gt;
using namespace std;

int n, lis[1001], len, num;
int main() {
    scanf(&quot;%d&quot;, &amp;n);
    for (int i = 0; i &lt; n; i++){
        scanf(&quot;%d&quot;, &amp;num);
        auto lowerPos = lower_bound(lis, lis + len, num);
        // lowerpos에 해당하는 값이 없으면 len ++ 
        if(*lowerPos == 0) len++;
        // 해당하는 값 num 으로 갱신 
        *lowerPos = num;
        for(int j = 0; j &lt; n; j++){
            printf(&quot;%d &quot;, lis[j]);
        }
        printf(&quot;\n&quot;);
    }
    printf(&quot;%d&quot;, len);

    return 0;
}
</code></pre>
<p>이처럼 간단하게 구현이 가능하다!</p>
<h3 id="백준-2565-전깃줄">백준 2565 전깃줄</h3>
<p><a href="https://www.acmicpc.net/problem/2565">2565 전깃줄</a></p>
<pre><code class="language-c">#include&lt;bits/stdc++.h&gt; 
using namespace std;
int n,len;
pair&lt;int,int&gt; a[101];
int lis[100001];
int main() {
    cin &gt;&gt; n;
    for(int i=0; i&lt;n; i++){
        cin&gt;&gt;a[i].first&gt;&gt;a[i].second;
    }
    sort(a,a+n);
    for(int i=0; i&lt;n; i++){
        auto it = lower_bound(lis,lis+len,a[i].second);
        if(*it==0) len++;
        *it = a[i].second;
    }
    cout&lt;&lt;n-len;
    return 0;
}</code></pre>
<p>전깃줄의 목적지를 x축 기준으로 정렬하면</p>
<blockquote>
<p>8 2 9 1 4 6 7 10</p>
</blockquote>
<p>복잡해보이지만, 위의 증가하는 부분수열과 로직이 같다.</p>