Cloneable 인터페이스는 복제 가능한 객체임을 명시해주는 믹스인 인터페이스다.    
그러나 clone()이라는 메서드가 선언된 곳이 Cloneable이 아닌 Object이며 이 마저도 protected이다.     
그래서 Cloneable을 구현하는 것만으로는 clone 메서드를 호출할 수 없다.          
그럼에도 Cloneable을 구현하는 방식은 널리 쓰이고 있어서 잘 알아두는 것이 좋다. 

이번 아이템은 clone()을 잘 동작할 수 있게 하는 구현 방법과 언제 그렇게 해야하는지 알려주고   
가능한 다른 선택지에 관해 논의하고자 한다.   

메서드가 없는 Cloneable 인터페이스는 무슨일을 할까?(마커 인터페이스)      
이 메서드는 Object 클래스의 clone 메서드의 동작 방식을 결정한다.        
clone()을 사용하면 그 객체의 필드들을 하나씩 복사한 객체를 반환하며,           
그렇지 않은 클래스는 ClassNotSupportedExceptio을 던진다.        
(이례적인 방법)  

실무에서는 Cloneable을 구현한 클래스는 clone()을 public으로 제공하며,     
사용자는 당연히 복제가 제대로 이뤄지리라 기대한다.       
이 기대를 만족시키려면 클래스와 몯느 상위 클래스는 복잡하고 강제할수 없고 허술하게 기술된 프로토콜을 지켜야만 하는데  
그결과 깨지기 쉽고 위험하고 모순적인 매커니즘이 탄생한다.   
즉, 생성자를 호출하지 않고도 객체를 생성할 수 있게 되는 것이다.  

clone 메서드 규약은 아래와 같으며 허술하다.  
```
x.clone() != x
x.clone().getClass() == x.getClass()
x.clone().equals(x)
x.clone().getClass() == x. getClass()
```


