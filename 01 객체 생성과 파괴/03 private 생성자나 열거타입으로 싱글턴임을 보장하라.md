private 생성자나 열거타입으로 싱글턴임을 보장하라    
=======================================   
        
싱글턴이란, **인스턴스를 오직 하나만 생성할 수 있는 클래스를 말한다.**           
싱글턴의 전형적인 예로는 **함수와 같은 `무상태 객체`나 설계상 유일해야 하는 `시스템 컴포넌트`를 들 수 있다.**            
    
그런데 **클래스를 싱글턴으로 만들면 이를 사용하는 클라이언트를 테스트하기 어려워질 수 있다.**       
타입을 인터페이스로 정의한 다음 그 인터페이스를 구현해서 만든 싱글턴이 아니라면          
싱글턴 인스턴스를 가짜(mock) 구현으로 대체할 수 없기 때문이다.           

# 싱글턴을 생성하는 방법  
싱글턴을 생성하는 방법은 2가지가 존재하다.     
     
1. public static 멤버가 final 필드인 방식  
2. 정적 팩터리 메서드를 public static 멤버로 제공   
3. 원소가 하나인 열거타입으로 선언      
     
두 방식 모두 생성자는 `private`으로 감춰두고,            
유일한 인스턴스에 접근할 수 있는 수단으로 `public static 멤버`를 하나 마련해둔다.          


## public static 멤버가 final 필드인 방식

```java
public class Elvis {
    public static final Elvis INSTANCE = new Elvis();
    private Elvis() { ... }
    
    public void leaveTheBuilding() { ... }
}
```
객체를 초기화할 때 `private 생성자`가 딱 한번 호출된다.       
그러나 리플랙션인, `AccessibleObject.setAccessible` 을 사용해 `private 생성자`를 호출할 수 있다.          
이러한 공격을 방어하려면 생성자를 두 번째 객체가 생성되려 할 때 예외를 던지게 하면 된다.     

**장점**   
* 해당 클래스가 싱글턴임을 API에 명백히 드러난다.   
* `public static final`이니 절대로 객체를 참조할 수 없다.
* 간결하다.    
  
## 정적 팩터리 메서드를 public static 멤버로 제공
```java
public class Elvis {
    private static final Elvis INSTANCE = new Elvis();
    private Elvis() { ... }
    public static Elvis getInstance() { return INSTANCE; } 
    
    public void leaveTheBuilding() { ... }
}
```
`Elvis.getInstance`는 항상 같은 객체의 참조를 반환하므로 다른 인스턴스는 만들어지지 않는다.    
 
**장점**    
* 마음이 바뀐다면 API를 바꾸지 않고도 싱글턴임이 아니게 변경할 수 있다.   
* 정적 팩터리를 싱글턴 팩터리로 만들 수 있다.  
* 정적 팩터리의 메서드 참조를 공급자로 사용할 수 있다.    
  예 : `Elvis::getInstance`를 `Supplier<Elvis>`로 사용  

## 원소가 하나인 열거타입으로 선언      
```java
public enum Elvis {
    INSTANCE;
    
    public void leaveTheBuilding() { ... }
}
```

더 간결하고, 추가 노력없이 직렬화할 수 있고        
복잡한 직렬화 상황이나 리플렉션 공격에서도 제2의 인스턴스가 생기는 일을 막아준다.       
   
조금 부자연스러워 보일 수는 있으나       
**대부분 상황에서는 원소가 하나뿐인 열거 타입이 싱글터늘 만드는 가장 좋은 방법이다.**        
단, 만들려는 싱글턴이 Enum 외의 클래스를 상속해야 한다면 이 방법은 사용할 수 없다.       
(열거 타입이 다른 인터페이스를 구현하도록은 할 수 있다.)     
   
# 직렬화 
`정적 팩터리 메서드를 public static 멤버로 제공`과   
`정적 팩터리 메서드를 public static 멤버로 제공` 방식으로 만든 싱글턴 클래스는    
`Serializable` 을 구현한다고 선언하는 것만으로는 부족하다.      

모든 인스턴스 필드를 `일시적(transien)`이라고 선언하고 `readResolve 메서드`를 제공해야한다.         
사실 직렬화 자체는 문제 없는데 역직렬화시 새로운 인스턴스가 만들어지기 때문이다.   

```java
// 싱글턴임을 보장해주는 readResolve 메서드   
private Object readResolve() {
    // 진짜 Elvis를 반환하고, 가짜는 가비지 컬렉터에 맡긴다.    
    return INSTACNE;
}
```
          
          
        


