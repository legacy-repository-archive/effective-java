비트 필드 대신 EnumSet을 사용하라
===================================

```java
public class Text {
    public static final int STYLE_BOLD          = 1 << 0; //1
    public static final int STYLE_ITALIC        = 1 << 1; //2
    public static final int STYLE_UNDERLINE     = 1 << 2; //4
    public static final int STYLE_STRIKETHROUGH = 1 << 3; //8

    public void applyStyles(int styles) {
    }
}
```  
  
* 정수 열거 상수의 단점을 그대로 가진다. 
* 비트 필드 값이 그대로 출력되면 해석하기가 어렵다. 
* 비트 필드 하나에 녹아 있는 모든 원소를 순회하기도 까다롭다. 
* 최대 몇 비트가 필요한지 API 작성 시 미리 예측해 적절한 타입을 선택해야 한다.(ex: int or long)

```java
public class Text {  
    public enum Style { BOLD, ITALIC, UNDERLINE, STRIKETHROUGH }
    //어떤 Set을 넣어도 되지만 EnumSet이 제일 좋다. 
    public void applyStyles(Set<Style> styleSet){ ... }
}
```
```java
text.applyStyles(EnumSet.of(Style.BOLD, Style.ITALIC));
```

# 정리   
열거할 수 있는 타입을 한데 모아 집합 형태로 사용한다고 해도 비트 필드를 사용할 이유는 없다.      
EnumSet 클래스가 비트 필드 수준의 명료함과 성능을 제공하고 열거 타입의 장점까지 선사하기 때문이다.      
EnumSet 의 유일한 단점이라면 불변 EnumSet을 만들 수 없다는 것이다.(Collections.unmodifiableSet으로 가능)    

