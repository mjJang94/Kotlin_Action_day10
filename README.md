# 동반 객체: 팩토리 메소드와 정적 멤버가 들어갈 장소

코틀린 클래스 안에는 정적인 멤버가 없다. 대신 코틀린에서는 패키지 수준의 최상위 함수와 객체 선언을 활용한다.   
대부분의 경우 최상위 함수를 활용하는 편을 더 권장하지만 최상위 함수가 private이라면 접근할 수 없다. 그래서 클래스의 인스턴스와 관계없이 호출해야 하지만, 클래스 내부 정보에 접근해야 하는 함수가 필요할 때는 클래스에 중첩된 객체 선언의 멤버 함수로 정의해야 한다. 

그래서 팩토리 메소드를 써야한다.   

클래스 안에 정의된 객체 중 하나에 companion이라는 특별한 표시를 붙이면 그 클래스의 동반 객체로 만들 수 있다. 동반 객체의 프로퍼티나 메소드에 접근하려면 그 동반 객체가 정의된 클래스 이름을 사용한다. 이때 객체의 이름은 따로 지정할 필요는 없고, 자바의 정적 메소드 호출이나 정적 필드 사용 구문과 같다.

<code>
<pre>
class A {
  companion object {
    fun bar() {
      println("Companion object called")
      }
    }
 }
>>> A.bar()
Companion object called
</pre>
</code>

동반 객체가 private 생성자를 호출하기에도 좋은 위치이다. 동반 객체는 자신을 둘러싼 클래스의 모든 private 멤버에 접근가능하다.   
그러므로 동반 객체는 바깥쪽 클래스의 private 생성자도 호출할 수 있다. 따라서 동반 객체는 팩토리 패턴을 구현하기 가장 적합하다.

일단 부 생성자가 여럿 있는 클래스를 만들자.
<code>
<pre>
class User {
  val nickname: String
  
  contructor(email: String) {
    nickname = email.substringBefore('@')
   }
   
   constructor(facebookAccountId: Int) {
    nickname = getFacebookName(facebookAccountId)
   }
 }
</code>
</pre>

이렇게 생긴 클래스를 팩토리 메소드로 구현해보자.
<code>
<pre>
class User private constructor(val nickname: String) {
  companion object {
    
    fun newSubscribingUser(email: String) = User(email.substringBefore('@'))
    
    fun newFacebookUser(facebookAccountId: Int) = User(getFacebookName(accountId))
   }
 }
 
>>> val subscribingUser = User.newSubscribingUser("bob@naver.com")
>>> val facebookUser = User.newFacebookUser(4)
>>> println(subscribingUser.nickname)
bob
</code>
</pre>
