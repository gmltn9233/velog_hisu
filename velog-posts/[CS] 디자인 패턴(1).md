<h1 id="디자인-패턴이란">디자인 패턴이란?</h1>
<blockquote>
<p>프로그램을 설계할때 발생했던 문제점들을 객체간의 상호 관계 등을 이용하여 해결할 수 있도록 하나의 <strong>'규약'</strong> 형태로 만들어 놓은 것</p>
</blockquote>
<hr />
<h1 id="디자인-패턴의-종류">디자인 패턴의 종류</h1>
<h2 id="싱글톤-패턴singleton-pattern">싱글톤 패턴(Singleton Pattern)</h2>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/8bfea280-09bb-48f5-a1f8-f89193541182/image.png" /></p>
<blockquote>
<p>하나의 클래스에 오직 하나의 인스턴스를 가지는 패턴</p>
</blockquote>
<ul>
<li>하나의 클래스를 기반으로 여러 개의 개별적인 인스턴스를 만들 수 있지만, 그렇게 하지 않고 <strong>하나의 클래스를 기반으로 단 하나의 인스턴스</strong>를 만들어 이를 기반으로 로직을 만드는데 쓰인다.</li>
<li>주로 <strong>데이터베이스 연결 모듈</strong>에 많이 사용한다.</li>
<li>하나의 인스턴스를 다른 모듈들이 공유하며 사용하기 때문에 인스턴스를 생성할 때 드는 <strong>비용이 줄어든다.</strong></li>
<li><strong>의존성이 높아진다는 단점</strong>이 존재한다.</li>
</ul>
<h4 id="자바에서의-싱글톤-패턴">자바에서의 싱글톤 패턴</h4>
<pre><code class="language-java">Class Singleton {
    private static class singleInstanceHolder {
        private static final Singleton INSTANCE = new Singleton();
    }
    public static Singleton getInstance(){
        return SingletonInstanceHolder.INSTANCE;
    }
}
public class HelloWorld {
    public static void main(String[] args){
        Singleton a = Singleton.getInstance();
        Singleton b = Singleton.getInstance();
        System.out.println(a.hashCode());
        System.out.println(b.hashCode());
        if(a==b){
            System.out.println(true);
        }
    }
}
/*
 705927765
 705927765
 true
 */</code></pre>
<h3 id="싱글톤-패턴의-단점">싱글톤 패턴의 단점</h3>
<h4 id="1-tddtest-driven-development를-할-때-걸림돌이-된다">1. TDD(Test Driven Development)를 할 때 걸림돌이 된다.</h4>
<p>TDD를 할 때 단위 테스트를 주로 하는데, <strong>단위 테스트는 테스트가 서로 독립적이어야 하며 테스트를 어떠한 순서로든 실행할 수 있어야 한다.</strong>
하지만 싱글톤 패턴은 미리 생성된 하나의 인스턴스를 기반으로 구현하는 패턴이므로 <strong>각 테스트마다 '독립적인' 인스턴스를 만들기가 어렵다.</strong></p>
<h4 id="2-모듈간의-결합을-강하게-만든다">2. 모듈간의 결합을 강하게 만든다.</h4>
<p>이때, 의존성 주입을 통해 모듈간의 결합을 조금 더 느슨하게 만들어 해결할 수 있다.</p>
<h3 id="의존성-주입di-dependency-injection">의존성 주입(DI, Dependency Injection)</h3>
<blockquote>
<p>객체가 필요로 하는 의존 객체를 외부에서 제공(주입)하는 방식, 의존성이란 종속성이라고도 하며 A가 B에 의존성이 있다는 것은 B의 변경 사항에 대해 A 또한 변해야 된다는 것을 의미한다.</p>
</blockquote>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/915bbabb-44de-4214-8f7a-b9f0544e0277/image.png" /></p>
<p>위 그림처럼, 메인 모듈이 <strong>'직접'</strong> 하위 모듈에 대한 의존성을 주기보다는 중간에 <code>의존성 주입자</code>가 이 부분을 가로채 메인 모듈이 <strong>'간접'</strong> 적으로 의존성을 주입하는 방식
이를 통해 메인 모듈(상위 모듈은) 하위 모듈에 대한 의존성이 떨어지게 된다.</p>
<h4 id="의존성-주입의-장점">의존성 주입의 장점</h4>
<ul>
<li>모듈을 쉽게 교체할 수 있는 구조가 되어 <strong>테스팅하기 쉽고 마이그레이션 하기도 수월하다.</strong></li>
<li>애플리케이션 의존성 방향이 <strong>일관되고, 쉽게 추론할 수 있다.</strong></li>
<li>모듈간의 관계들이 <strong>명확해진다.</strong></li>
</ul>
<h4 id="의존성-주입의-단점">의존성 주입의 단점</h4>
<ul>
<li>모듈들이 분리되므로 클래스 수가 늘어나 <strong>복잡성이 증가될 수 있으며 런타임 페널티</strong>가 생긴다.</li>
<li><strong>의존성 주입 원칙</strong>을 지켜주면서 만들어야 한다.<blockquote>
<p><strong>의존성 주입원칙이란?</strong>
&quot;상위 모듈은 하위 모듈에서 어떠한 것도 가져오지 않아야 한다. 또한 둘 다 추상화에 의존해야 하며, 이때 추상화는 세부 사항에 의존하지 말아야 한다.&quot;</p>
</blockquote>
</li>
</ul>
<hr />
<h2 id="팩토리-패턴factory-pattern">팩토리 패턴(Factory Pattern)</h2>
<blockquote>
<p>객체를 사용하는 코드에서 객체 생성 부분을 떼어내 <strong>추상화</strong>한 패턴이자 상속관계에 잇는 두 클래스에서 <strong>상위 클래스가 중요한 뼈대</strong>를 결정하고, <strong>하위 클래스에서 객체 생성에 관한 구체적인 내용을 결정</strong>하는 패턴</p>
</blockquote>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/4deff4de-f6e8-448c-a629-09776965df87/image.png" /></p>
<p>예를들어 라떼 레시피와 아메리카노 레시피, 우유 레시피라는 구체적인 내용이 들어 있는 <strong>하위 클래스가 컨베이어 벨트를 통해 전달</strong>되고, <strong>상위 클래스인 바리스타 공장에서 이 레시피들을 토대로 우유 등을 생산</strong>하는 생산공정을 생각하면 된다.</p>
<h4 id="자바의-팩토리-패턴">자바의 팩토리 패턴</h4>
<pre><code class="language-java">enum CoffeeType {
    LATTE,
    ESPRESSO
}

abstract class Coffee {
    protected String name;

    public String getName() {
        return name;
    }
}

class Latte extends Coffee {
    public Latte() {
        name = &quot;latte&quot;;
    }
}

class Espresso extends Coffee {
    public Espresso() {
        name = &quot;Espresso&quot;;
    }
}

class CoffeeFactory {
    public static Coffee createCoffee(CoffeeType type) {
        switch (type) {
            case LATTE:
                return new Latte();
            case ESPRESSO:
                return new Espresso();
            default:
                throw new IllegalArgumentException(&quot;Invalid coffee type: &quot; + type);
        }
    }
}

public class Main {
    public static void main(String[] args) { 
        Coffee coffee = CoffeeFactory.createCoffee(CoffeeType.LATTE); 
        System.out.println(coffee.getName()); // latte
    }
}</code></pre>
<h3 id="팩토리-패턴의-장점">팩토리 패턴의 장점</h3>
<ul>
<li>상위 클래스와 하위 클래스가 분리되기 때문에 <strong>느슨한 결합을 가진다.</strong></li>
<li>상위 클래스에서는 인스턴스 생성방식에 대해 전혀 알 필요가 없기 때문에 더 많은 <strong>유연성을 가진다.</strong></li>
<li>객체 생성 로직이 따로 떼어져 있기 때문에 코드를 리팩토링하더라도 한 곳만 고칠 수 있게되어 <strong>유지보수성이 증가한다.</strong></li>
</ul>
<h3 id="팩토리-패턴의-단점">팩토리 패턴의 단점</h3>
<ul>
<li>각 제품 구현체 마다 팩토리 객체들을 모두 구현해주어야 하기 때문에, <strong>구현체가 늘어날때 마다 팩토리 클래스가 증가</strong>하여 복잡성이 증가한다.</li>
<li><strong>코드의 복잡성이 증가한다.</strong></li>
</ul>
<h2 id="전략-패턴sttrategy-pattern">전략 패턴(Sttrategy Pattern)</h2>
<blockquote>
<p>객체의 행위를 바꾸고 싶은 경우 '<strong>직접' 수정하지 않고</strong> 전략이라고 부르는 <strong>'캡슐화한 알고리즘'을 컨텍스트 안에서 바궈주면서 상호 교체가 가능하게 만드는 패턴</strong></p>
</blockquote>
<h4 id="컨텍스트란">컨텍스트란?</h4>
<ul>
<li>상황,맥락,문맥을 의미하며 개발자가 어떠한 작업을 완료하는데 필요한 모든 관련 정보를 말한다.</li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/gmltn9233/post/caf27fa5-c3b4-4e2c-86d0-49084baeb84f/image.png" /></p>
<p>우리가 어떤 것을 살 때 네이버페이, 카카오페이 등 다양한 방법으로 결제하듯이 어떤 아이템을 살 때 LUNAcard로 사는 것과 KAKAOcard로 사는 것을 자바 코드로 구현한 예제를 살펴보자.</p>
<h4 id="paymentstrategy">PaymentStrategy</h4>
<pre><code class="language-java">interface PaymentStrategy{
    public void pay(int amount);
}

class KAKAOCardStrategy implemets PaymentStrategy{
    private String name;
    private String cardNumber;
    private String cvv;
    private String dateOfExpire;

    public KAKAOCardStrategy(String name, String cardNumber, String cvv, String dateOfExpire){
        this.name = name;
        this.cardNumber = cardNumber;
        this.cvv = cvv;
        this.dateOfExpire = dateOfExpire;
    }

    @Override
    public void pay(int amount){
        System.out.println(amount + &quot; paid using KAKAOCard.&quot;);
    }
}

class LUNACardStrategy implemets PaymentStrategy{
    private String emailId;
    private String password;

    public LUNACardStrategy(String emailId, String password){
        this.emailId = emailId;
        this.password = password;
    }

    @Override
    public void pay(int amount){
        System.out.println(amount + &quot; paid using LUNACard.&quot;);
    }
}</code></pre>
<p><code>PaymentStrategy</code> 인터페이스를 만들고, 인터페이스를 구현하는 <code>KAKAOCardStrategy</code> 클래스와 <code>LUNACardStrategy</code> 클래스를 구현해준다.</p>
<h4 id="item-shoppingcart">Item, ShoppingCart</h4>
<pre><code class="language-java">Class Item{
    private String name;
    private int price;

    public Item(String name, int price){
        this.name = name;
        this.price = price;
    }

    public Stirng getName(){
        return this.name;
    }

    public int getPrice(){
        return this.price;
    }
}

Class ShoppingCart{
    List&lt;Item&gt; items;

    public ShoppingCart(){
        this.items = new ArrayList&lt;Item&gt;();
    }

    public void addItem(Item item){
        this.items.add(item);
    }

    public void removeItem(Item item){
        this.items.remove(item);
    }

    public int calculateTotal(){
        int sum = 0;
        for(Item item : items){
            sum += item.getPrice();
        }
        return sum;
    }

    public void pay(PaymentStrategy paymentMethod){
        int amount = calculateTotal();
        paymentMethod.pay(amount);
    }
}</code></pre>
<p><code>Item</code> 객체를 만들고, 그 Item들을 계산할 <code>ShoppingCart</code> 객체를 만든다.
<code>ShoppingCart</code> 객체에서는 구현 전략에 따라 <strong>pay</strong> 메소드를 실행한다.</p>
<h4 id="실행결과">실행결과</h4>
<pre><code class="language-java">public class HelloWorld{
    public static void main(String[] args){
        ShoppingCart cart = new ShoppingCart();

        Item A = new Item(&quot;A&quot;,100);
        Item B = new Item(&quot;B&quot;,300);

        cart.addItem(A);
        cart.addItem(B);

        //pay by LUNACard
        cart.pay(new LUNACardStrategy(&quot;example@example.com&quot;,&quot;1234&quot;));

        //pay by KAKAOCard
        cart.pay(new KAKAOCardStrategy(&quot;example&quot;,&quot;1234&quot;,&quot;123&quot;,&quot;12/01&quot;));
    }
}

/*
 400 paid using LUNACard
 400 paid using KAKAOCard
 */</code></pre>
<h3 id="전략-패턴의-장점">전략 패턴의 장점</h3>
<ul>
<li>런타임에 한 <strong>객체 내부에서 사용되는 알고리즘들을 교환</strong>할 수 있다.</li>
<li>알고리즘을 사용하는 코드에서 <strong>알고리즘의 구현 세부정보들을 고립</strong>할 수 있다.</li>
<li>개방/폐쇄 원칙,** 콘텍스트를 변경하지 않고도 새로운 전략들을 도입**할 수 있다.</li>
</ul>
<h3 id="전략-패턴의-단점">전략 패턴의 단점</h3>
<ul>
<li>알고리즘이 몇 개밖에 되지 않고 거의 변하지 않는다면, 지나치게 복잡하게 만들 이유가 없다.</li>
<li>클라이언트들이 적절한 전략을 선택할 수 있도록 <strong>전략간의 차이점들을 알고 있어야 한다.</strong></li>
</ul>
<hr />
<p>참고 및 출처: <a href="https://www.yes24.com/Product/Goods/109317449">면접을 위한 CS 전공지식 노트</a></p>