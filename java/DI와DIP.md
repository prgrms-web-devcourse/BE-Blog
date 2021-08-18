고민, 고민 끝에 DI와 DIP에 대해 작성해보려고 합니다!!ㅜㅜㅜ 계속 주제를 바꾸는 것 같네요 흐엉.... 이번에는 확실하게 픽스하고 진행하겠습니다.

해당 주제는 제가 넥슨 웹개발자 면접때 받았던 질문입니다! 그럼 시작하겠습니다~

![](https://images.velog.io/images/dong_geon_kim/post/3e9280d4-e250-43b9-bcee-7df429a4ac11/image.png)

### DI와 DIP에 대해 설명하시오.

- DI는 의존성 주입을 의미합니다. 객체가 다른 객체를 사용하기 위해서 본인의 내부 로직에서 외부 객체에 대한 인스턴스를 생성하는 것이 아닌, 외부로부터 인스턴스를 주입받아 사용하는 것을 의미합니다.
- DI는 생성자 주입과 필드주입이 존재하며, 대체로 외부에서 변경이 가능한 생성자 주입을 사용하는 것을 권고합니다.
- DIP는 의존성 역전 원칙을 의미합니다. 객체가 구현체에 의존하지 않고, 인터페이스를 통해 인스턴스를 할당받게 함으로서, 다형성을 증가시킬 수 있는 원칙을 의미합니다.
- DIP를 다형성을 만족할 수 있고, 이를 통해 확장에 열려있을 수 있으며, 인터페이스를 통해 변경과 변화 그리고 코드의 일관성을 지킬 수 있습니다.

### DI와 DIP가 뭐지?

우리가 Spring Project를 구현하다보면, 필드 주입 또는 생성자 주입을 통해 객체를 받아서 사용할 것입니다.

보통은 아래의 코드처럼 생성자 주입을 통해 필요한 객체를 주입받아서 사용합니다

```
private final Book book;

public Study(Book book){
	this.book = book;
}
```

or

```
@Autowired
private Book book;
```

여기 매개변수로 받는 Book이 IOC 컨테이너를 통해 생성되고 주입되는 객체를 의미합니다! IOC 컨테이너에서 객체는 생성되고 사망하고...

이 부분은 너무나 방대하여 다음에 다시 정리하도록 하겠습니다!

위의 코드에서 외부에서 객체의 주입이 발생하는데.,..이런 주입을 DI `Dependency Injection`이라고 합니다

위의 코드처럼 외부의 주입을 통해 Study라는 Class에서 Book을 사용할 수 있습니다. 물론, Study에서 Book에 대한 인스턴스를 생성해서 사용할 수도 있습니다.

```
class Study {
	Book book = new Book();
}
```

or

```
class Study {
	private Book book;

	public Study(){
		book = new Book();
  }
}
```

위의 코드처럼 Study가 Book을 생성해서 사용할 경우 Study와 Book의 의존관계에서 결합도는 매우매우 커지게 됩니다!

의존관계에서 결합도가 높아지게 될 경우, Book이 변경되었을 때 Study는 Book과 연관된 코드를 전부 변경해야 하는 수고스러움이 발생합니다!

```
class Study {
	private Book book;

	public Study(){
		book = new Book(10, "3만원");
  }
}
```

위의 코드처럼 Book의 생성자가 바뀐다고 가정하면, new로 생성할 경우에는 Study의 생성자에서도 코드 변경의 귀찮음이 발생하게 됩니다..ㅜ

이렇게 Study라는 클래스 내부에서 객체를 생성하고 관리하게 되면, 추후 발생하는 변화에 유동적으로 대응할 수 없는 문제가 발생합니다!

그렇기 때문에 DI `Dependency Injection`이라는 기능을 통해 외부로 부터 주입받아서 결합도를 낮추고 변화에 능동적으로 대처하도록 구현해야 합니다. 

### 필드주입과 생성자 주입 그리고 Setter 주입

필드주입보다 생성자 주입이 좋다고 다들 말하지만, 실제로 왜 좋은지에 대해 고민해본적이 없던것 같습니다. 더불어 지금은 사용하지 않는 Setter 주입에 대해서 살펴보겠습니다!

```
public class Study {
	
	@Autowired
	private Book book;
}
```

아마 많은 교재에서 필드 주입을 통해 인스턴스를 할당할 것입니다,,, 인텔리제이를 사용하신다면, 필드주입 부분에서 에러를 확인하실 수 있는데 필드주입은 현재 스프링 또는 자바 어플리케이션에서 권고하지 않는 방식입니다.

필드를 선언하고 필드 위에 `@Autowired`를 선언하면, 외부 의존성 주입을 받지 않고 인스턴스를 생성하여 Bean에 등록할 수 있습니다. 이방식은 코드가 간결한 장점이 있지만! Test를 작성할때 외부주입이 불가능함으로 어려움을 겪을 수 있습니다. 더불어 추상체가 아닌 구현체를 직접 생성하기 때문에 변화에 민감해집니다.

생성자 주입은 아래와 같이 외부의 주입을 통해 의존관계를 형성하는 방법입니다!

```
public class Study {
	
	private final Book book;

  public Study(Book book){
		this.book = book;
  }
}
```

외부로 부터 주입 받기 때문에 변화에 능동적으로 대처할 수 있고, 구현체에 의존하지 않고 사용할 수 있는 장점이 있습니다!

더불어 final로 생성된 불변객체를 사용함으로서, 인스턴스의 복제, 비교가 쉬워지며, 한번 생성한 인스턴스에 대한 변경을 금지시킴으로서 안전한 객체사용이 가능해집니다!

Setter 주입은 set() 메서드를 통해 생성자를 주입하는 방법입니다

```
public class Study{
	private Book book;

	@Autowired
	public void setBook(Book book){
		this.book = book;
  }
}
```

선택적으로 book에 대한 인스턴스를 주입받아 생성할 수 있도록 구현하는 방식입니다!

물론, 해당 방식을 사용하면, 원하는 인스턴스를 개발자가 원하는 시기에 생성할 수 있고 관리할 수 있다는 장점이 존재합니다!

하지만! 이 방식의 경우 Study객체의 인스턴스가 생성되었을 때 set()을 돌려주지 않으면 book이 null값을 가져 exception이 터질 수 있습니다!

런타임시에 문제를 해결할 수 없기 때문에 해당 주입 방법은 지양하는 것을 추천드립니다!

다음으로 interface를 통해 확장성을 가지는 예를 살펴보겠습니다!

```
class Study {
	private final Book book;

	public Study(Book book){
		this.book = smallBook;
  }
}
```

SmallBook이라는 Book을 implements한 클래스가 있다고 가정합시다!

Study에서는 Book이라는 존재만 알고 있어도 smallBook이라는 구현체를 받아서 사용할 수 있게 됩니다!

```
Book smallBook = new SmallBook();
new Study(smallBook);
```

반대로 직접 인스턴스를 생성할 경우!

```
class Study {
	private final Book book;

	public Study(){
		this.book = new SmallBook();
  }
}
```

Study는 book을 implements한 모든 구현체를 직접 Study안에서 수정해야 하는 불편함을 겪게 됩니다..

이처럼 Study가 구현체에 의존하게 된다면, Book이라는 인터페이스를 implements한 모든 class에 대한 생성자 혹은 변환 메서드를 구현해야 하는 불편함이 발생합니다!

그렇기 때문에 우리가 자주 외웠던 SOLID의 마지막 원칙 DIP `Dependency Inversion principle` 의존성 역전 원칙를 통해 구현체 직접 의존하지 않고, 추상화된 클래스를 의존함으로서 변경에 유용하고 다형성을 통한 확장을 유용하게 할 수 있는 코드를 작성해야 합니다!

DIP의 개념이 쓰이는 곳은 정말 방대합니다. 예를 들어 Spring에서 OAuth2.0을 통해 로그인을 진행한다고 하면

![](https://images.velog.io/images/dong_geon_kim/post/84ed139d-092e-4cb2-8a6c-42110e24358e/image.png)

이렇게 다양한 소셜네트워크 서비스가 존재할 것입니다. 만약 해당 객체들을 서로 다른 클래스로 구현한다면

```
public class KaKao{

}

public class Facebook{

}

public class Naver{

}
```

이렇게 무지막지한 class 생성 폭발이 발생할 겁니다! 그리고 이를 service에서 사용하게 된다면

```
public void loginkakao(){
	kakao.login();
}

public void loginNaver(){
	naver.login();
}

public void loginFacebook(){
	facebook.login();
}
```

다형성이라는 개념은 어디에도 없게 되고, 코드의 길이는 점점 길어지며, login을 진행하기 위해서 필요한 서비스 로직은 방대해지게 됩니다..

그렇기 때문에 카카오, 페이스북, 네이버 모두 로그인이라는 인터페이스를 상속받아서 사용하게 된다면 추후 로그인 로직에서 다형성을 지킬 수 있으며, 구현체에 의존하지 않는 코드를 작성할 수 있게 됩니다.

결국, 인터페이스를 통해 선언된 로직을 통해 카카오, 페이스북, 네이버에 의존하지 않고도 로그인 서비스를 이용할 수 있게 되는 것입니다.

```
public interface Login(){
	void login();
}

public class KaKao implements Login{

}

public class Facebook implements Login{

}

public class Naver implements Login{

}

public void login(){
	login.login();
}
```

지금까지 간단한 예제를 살펴보며, DI와 DIP에 대해 간략하게 살펴봤습니다!

### DI에 대해 다시 정리하자면..

![](https://images.velog.io/images/dong_geon_kim/post/b6f2ca3c-0330-4a17-a75c-70bcf8d9f3e8/image.png)

아마도 위의 예제 코드를 보더라도 많이 어려움을 느낄 것 같습니다.. 그래서 탕수육으로 비교하며 DI에 대한 정리를 마칠려고 합니다!

부먹 탕수육은 소스가 이미 부어져 있어서 새로운 소스가 나타났을때, 기존의 소스를 제거하기 위해 많은 어려움이 존재할 것입니다..ㅜㅜ

하지만 찍먹 탕수육의 경우에는 새로운 소스가 나타난다면, 언제든지 새로운 소스를 찍어먹으며 다채로운 맛을 느낄 수 있을 겁니다!!

마찬가지로 DI도 외부로부터 주입받음으로써, 내부의 로직 변경없이, 변경된 클래스에 대한 설정만으로도 문제를 해결할 수 있는 장점을 제공합니다!

### 그렇다면 DIP는?

![](https://images.velog.io/images/dong_geon_kim/post/7ec6b17c-8d40-4eba-84a8-38e00f26d3bb/image.png)

DIP는 치킨집에서 제일 잘나가는 치킨을 무엇이냐고 물어보는 것과 같다고 생각합니다.

우리가 치킨집에 가서 제일 잘팔리는 치킨을 달라고 하면, 종업원은 다양한 치킨메뉴 중 어떤 것을 시킬거냐고 물어볼 것입니다.

만약 우리가, 후라이드 치킨을 시켰다면, 그 가게에서 제일 잘팔리는 양념치킨을 못먹었을 겁니다...

마찬가지로 DIP는 치킨처럼 큰 범주를 가진 인터페이스를 통해서 양념치킨과 후라이드치킨과 같은 구상체를 받을 수 있는 원칙입니다.

이를 통해 우리는 치킨이 양념치킨 또는 후라이드치킨처럼 종류가 다양해지는 다형성이라는 기능을 살릴 수 있게 됩니다.
