AOP
========

- 스프링에 적용된 가장 인기 있는 AOP의 적용대상은 바로 선언적 트랜잭션 기능


## 트랜잭션 코드의 분리

- 비즈니스 로직이 주인이어야 할 메소드 안에 트랜잭션 코드가 더 많은 자리를 차지
- 하지만 트랜잭션의 경계는 분명 비즈니스 로직의 전후에 설정돼야 하는 것이 분명

### 메소드 분리

- 비즈니스 로직 코드를 사이에 두고 트랜잭션 시작과 종료를 담당하는 코드가 앞뒤에 위치
- 비즈니스 로직을 담당하는 코드가 트랜잭션의 시작과 종료 작업 사이에서 수행되어야 한다는 사항만 지켜지면 된다


## DI를 이용한 클래스의 분리

- DI의 기본 아이디어는 실제 사용할 오브젝트의 클래스 정체는 감춘 채 인터페이스를 통해 간접으로 접근하는 것
- 인터페이스를 이용해 구현 클래스를 클라이언트에 노출하지 않고 런타임 시에 DI를 통해 적용하는 방법을 쓰는 이유는, 일반적으로 구현 클래스를 바꿔가면서 사용하기 위함이다
- 한 번에 두 개의 인터페이스 구현 클래스를 동시에 사용할 수도 있다
  - 트랜잭션 경계설정을 담당하는 코드를 외부로 빼내려는 것


## 분리된 트랜잭션 기능

- 비즈니스 트랜잭션 처리를 담은 클래스에서 같은 인터페이스를 구현한 다른 오브젝트에게 고스란히 작업을 위임시킨다
- 트랜잭션 안에서 동작하도록 만들어야 하는 메소드 호출 전후로 트랜잭션 경계설정 API를 사용하면 된다
- 클라이언트는 트랜잭션 처리가 가능한 인터페이스 구현 클래스를 주입받도록 한다


## 트랜잭션 경계설정 코드 분리의 장점

- 비즈니스 로직을 담당하고 있는 서비스 클래스의 코드를 작성할 때 트랜잭션과 같은 기술적인 내용에는 전혀 신경쓰지 않아도 된다
- 트랜잭션은 DI를 이용해 트랜잭션 기능을 가진 오브젝트가 먼저 실행되도록 만들기만 하면 된다
- 비즈니스 로직에 대한 테스트를 손쉽게 만들어낼 수 있다


## 고립된 단위 테스트

- 테스트 대상이 다른 오브젝트와 환경에 의존하고 있다면 작은 단위의 테스트가 주는 장점을 얻기 힘듬
- 테스트를 준비하기 힘들고, 환경이 조금이라도 달라지면 동일한 테스트 결과를 내지 못할 수도 있으며, 수행 속도는 느리고 그에 따라 테스트를 작성하고 실행하는 빈도가 점차 떨어진다
- 테스트의 대상이 환경이나, 외부 서버, 다른 클래스의 코드에 종속되고 영향을 받지 않도록 고립시킬 필요가 있음
- 테스트를 의존 대상으로부터 분리해서 고립시키는 방법은 테스트를 위한 대역을 사용하는 것
- 고립된 테스트를 하면 테스트가 다른 의존 대상에 영향을 받을 경우를 대비해 복잡하게 준비할 필요가 없을 뿐만 아니라, 테스트 수행 성능도 크게 향상된다


## 목 오브젝트 구현

- 스텁과 같은 방식으로 테스트 대상을 통해 사용될 때 필요한 기능을 지원해줘야 함
- 사용하지 않을 메소드도 구현해야 한다면 UnsupportedOperationException을 던지도록 만드는 편이 좋음
- 완전히 고립되어 테스트만을 위해 독립적으로 동작하는 테스트 대상을 사용하는 경우, 스프링 컨테이너의 빈을 가져올 필요가 없음


## 단위 테스트와 통합 테스트

- 단위 테스트: 테스트 대상 클래스를 목 오브젝트 등의 테스트 대역을 이용해 의존 오브젝트나 외부의 리소스를 사용하지 않도록 고립시켜서 테스트하는 것
- 통합 테스트: 두 개 이상의, 성격이나 계층이 다른 오브젝트가 연동하도록 만들어 테스트하거나, 또는 외부의 DB나 파일, 서비스 등의 리소스가 참여하는 테스트


### 가이드라인

- 항상 단위 테스트를 먼저 고려한다
- 하나의 클래스나 성격과 목적이 같은 긴밀한 클래스 몇 개를 모아서 외부와의 의존관계를 모두 차단하고 필요에 따라 스텁이나 목 오브젝트 등의 테스트 대역을 이용하도록 테스트를 만든다
  - 단위 테스트는 테스트 작성도 간단하고 실행 속도도 빠르며 테스트 대상 외의 코드나 환경으로부터 테스트 결과에 영향을 받지도 않는다
  - 가장 빠른 시간에 효과적인 테스트를 작성하기 유리
- 외부 리소스를 사용해야만 가능한 테스트는 통합 테스트로 만든다
- 단위 테스트로 만들기 어려운 코드도 있다
  - DAO는 그 자체로 로직을 담고 있기보다는 DB를 통해 로직을 수행하는 인터페이스와 같은 역할을 한다
  - SQL을 JDBC를 통해 실행하는 코드만으로는 고립된 테스트를 작성하기 힘들다
  - DAO는 DB까지 연동하는 테스트로 만드는 편이 효과적
  - DB를 사용하는 테스트는 DB에 테스트 데이터를 준비하고, DB에 직접 확인을 하는 등의 부가적인 작업이 필요
- DAO 테스트는 DB라는 외부 리소스를 사용하기 때문에 통합 테스트로 분류됨
  - 하지만, 코드에서 보자면 하나의 기능 단위를 테스트하는 것이기도 함
  - **DAO를 테스트를 통해 충분히 검증해두면, DAO를 이용하는 코드는 DAO 역할을 스텁이나 목 오브젝트로 대처해서 테스트할 수 있다**
- 여러 개의 단위가 의존관계를 가지고 동작할 때를 위한 통합 테스트는 필요하다. 다만, 단위 테스트를 충분히 거쳤다면 통합 테스트의 부담은 상대적으로 줄어든다
- 단위 테스트를 만들기 너무 복잡하다고 판단되는 코드는 처음부터 통합 테스트를 고려해본다
  - 이때도 통합 테스트에 참여하는 코드 중에서 가능한 한 많은 부분을 미리 단위 테스트로 검증해두는 것이 유리하다
- 스프링 테스트 컨텍스트 프레임워크를 이용하는 테스트는 통합 테스트다
  - 가능한 스프링의 지원 없이 직접 코드 레벨의 DI를 사용하면서 단위 테스트를 하는 편이 좋다
  - 스프링의 설정 자체도 테스트 대상이고, 스프링을 이용해 좀 더 추상적인 레벨에서 테스트해야 할 경우에는 스프링 테스트 컨텍스트 프레임워크를 이용해 통합 테스트를 작성한다


## 목 프레임워크(Mockito)

- 목 오브젝트를 편리하게 작성하도록 도와주는 프레임워크. 주로 Mockito를 이용한다
- 간단한 메소드 호출만으로 다이내믹하게 특정 인터페이스를 구현한 테스트용 목 오브젝트를 만들 수 있다
  - 인터페이스를 이용해 목 오브젝트를 만든다
  - 목 오브젝트가 리턴할 값이 있으면 이를 지정해준다. 메소드가 호출되면 예외를 강제로 던지게 만들 수도 있다
  - 테스트 대상 오브젝트에 DI해서 목 오브젝트가 테스트 중에 사용되도록 만든다
  - 테스트 대상 오브젝트를 사용한 후에 목 오브젝트의 특정 메소드가 호출됐는지, 어떤 값을 가지고 몇 번 호출됐는지를 검증한다


## 프록시

- 프록시: 자신이 클라이언트가 사용하려고 하는 실제 대상인 것처럼 위장해서 클라이언트의 요청을 받아주는 것
- 타깃: 프록시를 통해 최종적으로 요청을 위임받아 처리하는 실제 오브젝트
- 타깃과 같은 메소드를 구현하고 있다가 메소드가 호출되면 타깃 오브젝트로 위임한다
- 지정된 요청에 대해서는 부가기능을 수행한다

## 데코레이터 패턴

- 타깃에 부가적인 기능을 런타임 시 다이내믹하게 부여해주기 위해 프록시를 사용하는 패턴
  - 다이내믹하게 기능을 부가한다는 의미는 컴파일 시점, 즉 코드상에서는 어떤 방법과 순서로 프록시와 타깃이 연결되어 사용되는지 정해져 있지 않다는 뜻
- 제품이나 케익 등을 여러 겹으로 포장하고 그 위에 장식을 붙이는 것처럼 실제 내용물은 동일하지만 부가적인 효과를 부여해줄 수 있기 때문에 데코레이터 패턴이라 불린다
- 데코레이터 패턴에서는 프록시가 꼭 한 개로 제한되지 않는다
- 프록시가 직접 타깃을 사용하도록 고정시킬 필요도 없다
- 자바 IO 패키지의 InputStream과 OutputStream 구현 클래스는 데코레이터 패턴이 사용된 대표적인 예
- 인터페이스를 통해 위임하는 방식이기 때문에 어느 데코레이터에서 타깃으로 연결될지 코드 레벨에선 미리 알 수 없다
- 타깃의 코드를 손대지 않고, 클라이언트가 호출하는 방법도 변경하지 않은 채로 새로운 기능을 추가할 때 유용한 방법


## 프록시 패턴

- 프록시를 사용하는 방법 중에서 타깃에 대한 접근 방법을 제어하려는 목적을 가진 경우를 가리킴
  - 타깃의 기능 자체에는 관여하지 않으면서 접근하는 방법을 제어해주는 프록시를 이용
- 프록시 패턴의 프록시는 타깃의 기능을 확장하거나 추가하지 않고, **클라이언트가 타깃에 접근하는 방식을 변경함**
- 타깃 오브젝트를 생성하기 복잡하거나 당장 필요하지 않은 경우에는 꼭 필요한 시점까지 오브젝트를 생성하지 않는 편이 좋음
  - 클라이언트에 타깃 오브젝트 대신 프록시를 넘기고, 프록시의 메소드를 통해 타깃을 사용하려는 시도가 발생하면, 그때 프록시가 타깃 오브젝트를 생성하고 요청을 위임해주는 식
- 원격 오브젝트를 이용하는 경우에도 프록시를 사용하면 편리
- 특별한 상황에서 타깃에 대한 접근권한을 제어하기 위해 프록시 패턴을 사용할 수도 있음
- 구조적으로 데코레이터와 프록시가 유사하지만, 프록시는 코드에서 자신이 만들거나 접근할 타깃 클래스 정보를 알고 있어야 하는 경우가 많다


## 프록시 생성이 번거로운 이유

- 타깃의 인터페이스를 구현하고 위임하는 코드를 작성하기 번거로움
- 부가기능 코드가 중복될 가능성이 많음


## 다이내믹 프록시

- 자바에는 java.lang.reflect 패키지 안에 프록시를 손쉽게 만들 수 있도록 지원해주는 클래스들이 있음
- 다이내믹 프록시는 리플렉션 기능을 이용해서 프록시를 만들어준다
- 프록시 팩토리에 의해 런타임 시 다이내믹하게 만들어지는 오브젝트. 타깃의 인터페이스와 같은 타입으로 만들어짐
  - 프록시 팩토리에게 인터페이스 정보만 제공해주면 해당 인터페이스를 구현한 클래스의 오브젝트를 자동으로 만들어준다
  - 인터페이스 구현 클래스의 오브젝트는 만들어주지만, 프록시로서 필요한 부가기능 제공 코드는 직접 작성해야 한다
  - 부가기능은 프록시 오브젝트와 독립적으로 InvocationHandler를 구현한 오브젝트에 담는다


## 리플렉션

- 자바의 코드 자체를 추상화해서 접근하도록 만든 것
- 자바의 모든 클래스는 그 클래스 자체의 구성정보를 담은 Class 타입의 오브젝트를 하나씩 가지고 있음
  - `클래스이름.class` 혹은 오브젝트의 getClass() 메소드를 호출하면 클래스 정보를 담은 Class 타입의 오브젝트를 가져올 수 있음
  - 클래스 오브젝트를 이용하면 클래스 코드에 대한 메타정보를 가져오거나 오브젝트를 조작할 수 있음


## InvocationHandler 인터페이스

- `public Object invoke(Object proxy, Method method, Object[] args)`
- 위 메소드 한 개만을 가진 인터페이스
- 다이내믹 프록시 오브젝트는 클라이언트의 모든 요청을 리플렉션 정보로 변환해서 InvocationHandler 구현 오브젝트의 invoke() 메소드로 넘김
- 타깃 인터페이스의 모든 메소드 요청이 하나의 메소드로 집중되기 떄문에 중복되는 기능을 효과적으로 제공할 수 있음
- 리플렉션으로 메소드와 파라미터 정보를 모두 가지고 있으므로 타깃 오브젝트의 메소드를 호출하게 할 수도 있다
- InvocationHandler 구현 오브젝트가 타깃 오브젝트 레퍼런스를 갖고 있다면 리플렉션을 이용해 간단히 위임 코드를 만들 수 있음


## 프록시 생성

- Proxy 클래스의 newProxyInstance() 스태틱 팩토리 메소드를 이용
- 첫 번째 파라미터로 클래스 로더를 제공
  - 다이내믹 프록시가 정의되는 클래스 로더를 지정
- 두 번째 파라미터는 다이내믹 프록시가 구현해야 할 인터페이스
  - 다이내믹 프록시는 한 번에 하나 이상의 인터페이스를 구현할 수도 있으므로 배열을 이용
- 마지막 파라미터로 부가기능과 위임 관련 코드를 담고 있는 InvocationHandler 구현 오브젝트를 제공


## 다이내믹 프록시를 이용한 트랜잭션 부가기능

```java
public class TransactionHandler implements InvocationHandler {
  private Object target;  // 부가기능을 제공할 타깃 오브젝트. 어떤 타입의 오브젝트에도 적용 가능
  private PlatformTransactionManager transactionManater;  // 트랜잭션 기능을 제공하는데 필요한 트랜잭션 매니저
  private String pattern;  // 트랜잭션을 적용할 메소드 이름 패턴

  public void setTarget(Object target) {
    this.target = target;
  }

  public void setTransactionManager(PlatformTransactionManager transactionManager) {
    this.transactionManager = transactionManager;
  }

  public void setPattern(String pattern) {
    this.pattern = pattern;
  }

  public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
    if (method.getName().startsWith(pattern)) {
      return invokeInTransaction(method, args);
    } else {
      return method.invoke(target, args);
    }
  }

  private Object invokeInTransaction(Method method, Object[] args) throws Throwable {
    TransactionStatus status = this.transactionManager.getTransaction(new DefaultTransactionDefinition());

    try {
      Object ret = method.invoke(target, args);
      this.transactionManager.commit(status);
      return ret;
    } catch (InvocationTargetException e) {
      this.transactionManager.rollback(status);
      throw e.getTargetException();
    }
  }
}
```

- 요청을 위임할 타깃을 DI로 제공받음
- 트랜잭션 추상화 인터페이스인 PlatformTransactionManager를 DI 받음
- 타깃 오브젝트의 모든 메소드에 무조건 트랜잭션이 적용되지 않도록 트랜잭션을 적용할 메소드 이름의 패턴을 DI 받음
- 리플렉션 메소드인 Method.invoke()를 이용해 타깃 오브젝트의 메소드를 호출할 때는 타깃 오브젝트에서 발생하는 예외가 InvocationTargetException으로 한 번 포장돼서 전달된다


## 다이내믹 프록시를 위한 팩토리 빈

- DI의 대상이 되는 다이내믹 프록시 오브젝트는 일반적인 스프링의 빈으로는 등록할 방법이 없다
- 스프링의 빈은 기본적으로 클래스 이름과 프로퍼티로 정의됨
  - 스프링은 내부적으로 리플렉션 API를 이용해서 빈 정의에 나오는 클래스 이름을 가지고 빈 오브젝트를 생성
- 다이내믹 프록시 오브젝트의 클래스가 어떤 것인지 알 수 없고, 클래스 자체도 내부적으로 다이내믹하게 새로 정의해서 사용하기 때문에 사전에 프록시 오브젝트의 클래스 정보를 미리 알아내서 스프링의 빈에 등록할 방법은 없다
- 대신 팩토리 빈을 이용


## 팩토리 빈

```java
public interface FactoryBean<T> {
  T getObject() throws Exception;
  Class<? extends T> getObjectType();
  boolean isSingleton();
}
```

- 스프링을 대신해서 오브젝트의 생성로직을 담당하도록 만들어진 특별한 빈
- FactoryBean 인터페이스를 구현하는 방법이 가장 간단
- 스프링은 FactoryBean 인터페이스를 구현한 클래스가 빈의 클래스로 지정되면, 팩토리 빈 클래스의 오브젝트의 getObject() 메소드를 이용해 오브젝트를 가져오고, 이를 빈 오브젝트로 사용한다
  - 실제 빈으로 사용될 오브젝트를 직접 생성하는데, 코드를 이용하기 때문에 복잡한 방식의 오브젝트 생성과 초기화 작업도 가능하다
- 빈의 클래스로 등록된 팩토리 빈은 빈 오브젝트를 생성하는 과정에만 사용된다
- 빈의 타입은 팩토리 빈의 getObjectType() 메소드가 돌려주는 타입으로 결정된다
- 팩토리 빈 자체를 가져오고 싶다면 `&`를 빈 이름 앞에 붙여주면 팩토리 빈 자체를 돌려준다


## 프록시 팩토리 빈 방식의 장점

- 프록시 팩토리 빈을 재사용함으로서 코드의 수정 없이 다양한 클래스에 적용이 가능해진다
- 다이내믹 프록시를 이용하면 타깃 인터페이스를 구현하는 클래스를 일일이 만드는 번거로움을 제거할 수 있다
- 하나의 핸들러 메소드를 구현하는 것만으로도 수많은 메소드에 부가기능을 부여할 수 있어 부가기능 코드의 중복 문제도 해결된다
- 빈을 이용한 DI까지 이용한다면 다이내믹 프록시 생성 코드도 제거가 가능하다


## 프록시 팩토리 빈 방식의 한계

- 하나의 클래스 안에 존재하는 여러 개의 메소드에 부가기능을 한 번에 제공하는 것은 가능하지만, 한 번에 여러 개의 클래스에 공통적인 부가기능을 제공하는 것은 불가능
  - 비즈니스 로직을 담은 많은 클래스의 메소드에 적용할 필요가 있다면 거의 비슷한 프록시 패곹리 빈의 설정이 중복되는 것을 막을 수 없다
- 하나의 타깃에 여러 개의 부가기능을 적용하려 한다면, 프록시 팩토리 빈설정이 부가기능의 개수만큼 따라 붙어야 한다
- 타깃 오브젝트가 달라지면 새로운 오브젝트를 만들어야 한다


## 스프링의 프록시 팩토리 빈

- 스프링은 서비스 추상화를 프록시 기술에도 동일하게 적용하고 있음
- 프록시 오브젝트를 생성해주는 기술을 추상화 한 팩토리 빈을 제공
  - 생성된 프록시는 스프링의 빈으로 등록됨
- ProxyFactoryBean은 프록시를 생성해서 빈 오브젝트로 등록하게 해주는 팩토리 빈
  - 순수하게 프록시를 생성하는 작업만을 담당하고 프록시를 통해 제공해줄 부가기능은 별도의 빈에 둘 수 있음
  - MethodInterceptor 인터페이스를 구현해서 만든다


## MethodInterceptor

- MethodInterceptor의 invoke() 메소드는 ProxyFactoryBean으로부터 타깃 오브젝트에 대한 정보를 함께 제공받는다
  - MethodInterceptor로는 메소드 정보와 함께 타깃 오브젝트가 담긴 MethodInvocation 오브젝트가 전달됨
  - MethodInvocation은 타깃 오브젝트의 메소드를 실행할 수 있는 기능이 있어 MethodInterceptor는 부가기능을 제공하는 데 집중할 수 있음
  - InvocationHandler의 invoke() 메소드는 타깃 오브젝트에 대한 정보를 제공하지 않기에 타깃은 InvocationHandler를 구현한 클래스가 직접 알고 있어야 한다 
- 이 덕분에 MethodInterceptor는 타깃 오브젝트에 상관없이 독립적으로 만들어질 수 있으므로, 타깃이 다른 여러 프록시에서 함께 사용할 수 있고, 싱글톤 빈으로 등록이 가능해진다


## MethodInvocation

- proceed() 메소드를 실행하면 타깃 오브젝트의 메소드를 내부적으로 실행해주는 기능을 가진 일종의 콜백 오브젝트
- ProxyFactoryBean은 작은 단위의 템플릿/콜백 구조를 응용해서 적용했기 때문에 템플릿 역할을 하는 MethodInvocation을 싱글톤으로 두고 공유할 수 있다


## 어드바이스

- 타깃 오브젝트에 적용하는 부가기능을 담은 오브젝트
- 타깃 오브젝트에 종속되지 않는 순수한 부가기능을 담은 오브젝트이다
- 어드바이스는 JDK의 다이내믹 프록시의 InvocationHandler와 달리 직접 타깃을 호출하지 않음
  - 자신이 공유돼야 하므로 타깃 정보라는 상태를 가질 수 없음
  - 타깃에 직접 의존하지 않도록 일종의 템플릿 구조로 설계되어 있음
- 어드바이스가 부가기능을 부여하는 중에 타깃 메소드의 호출이 필요하면 프록시로부터 전달받은 MethodInvocation 타입 콜백 오브젝트의 proceed() 메소드를 호출해주기만 하면 된다
- ProxtFactoryBean에 MethodInterceptor를 설정해줄 때는 일반적인 DI처럼 수정자 메소드를 사용하는 대신 addAdvice() 메소드를 사용
- ProxyFactoryBean에는 여러 개의 MethodInterceptor를 추가할 수 있음
- MethodInterceptor는 Advice 인터페이스를 상속하고 있는 서브 인터페이스


## 포인트컷

- 메소드 선정 알고리즘을 담은 오브젝트
- Pointcut 인터페이스를 구현해서 만들면 된다
- 어드바이스와 포인트컷은 모두 프록시에 DI로 주입돼서 사용된다


## ProxyFactoryBean 방식

- ProxyFactoryBean이 다이내믹 프록시를 생성
- 프록시는 클라이언트로부터 요청을 받으면 먼저 포인트컷에게 부가기능을 부여할 메소드인지를 확인해달라고 요청
- 프록시는 포인트컷으로부터 부가기능을 제공할 대상 메소드인지 확인받으면, MethodInterceptor 타입의 어드바이스를 호출한다
- 어드바이스가 부가기능을 부여하는 중에 타깃 메소드의 호출이 필요하면 프록시로부터 전달받은 MethodInvocation 타입 콜백 오브젝트의 proceed() 메소드를 호출
- 실제 위임 대상인 타깃 오브젝트의 레퍼런스를 갖고 있고, 이를 이용해 타깃 메소드를 직접 호출하는 것은 프록시가 메소드 호출에 따라 만드는 Invocaiton 콜백의 역할
- 재사용 가능한 기능(Advice)을 만들어두고 바뀌는 부분(MethodInvocation)만 외부에서 주입해서 이를 작옵 흐름 중에 사용하도록 하는 전형적인 템플릿/콜백 구조


## 트랜잭션 어드바이스

```java
public class TransactionAdvice implements MethodInterceptor {
  PlatformTransactionManager transactionManager;

  public void setTransactionManager(PlatformTransactionManager transactionManager) {
    this.transactionManager = transactionManager;
  }

  public Object invoke(MethodInvocation invocation) throws Throwable {
    TransactionStatus status = this.transactionManager.getTransaction(new DefaultTransactionDefinition());

    try {
      Object ret = invocation.proceed();
      this.transactionManager.commit(status);
      return ret;
    } catch (RuntimeException e) {
      this.transactionManager.rollback(status);
      throw e;
    }
  }
}
```

- 스프링의 어드바이스 인터페이스(MethodInterceptor) 구현
- 타깃을 호출하는 기능을 가진 콜백 오브젝트(MethodInvocation)를 프록시로부터 받으므로 어드바이스는 특정 타깃에 의존하지 않고 재사용이 가능
- 콜백을 호출(invocation.proceed())해서 메소드를 실행
- 타킷 메소드 호출 전후로 필요한 부가기능을 넣을 수 있음
- JDK 다이내믹 프록시가 제공하는 Method와는 달리 스프링의 MethodInvocation을 통한 타깃 호출은 예외가 포장(InvocationTargetException)되지 않고 타깃에서 보낸 그대로 전달된다


## 스프링 XML 설정 예시

```xml
<bean id="transactionAdvice" class="...">
  <property name="transactionManager" ref="transactionManager" />
</bean>

<bean id="transactionPointcut" class="org.springframework.aop.support.NameMatchMethodPointcut">
  <property name="mappedName" value="upgrade*" />
</bean>

<bean id="transactionAdvisor" class="org.stpringframework.aop.support.DefaultPointcutAdvisor">
  <property name="advice" ref="transactionAdvice" />
  <property name="pointcut" ref="transactionPointcut" />
</bean>

<bean id="userService" class="org.springframework.aop.framework.ProxyFactoryBean">
  <property name="target" ref="userServiceImpl" />
  <property name="interceptorNames">
    <list>
      <value>transactionAdvisor</value>
    </list>
  <property>
</bean>
```

## 어드바이스와 포인트컷의 재사용

- ProxyFactoryBean은 스프링의 DI와 템플릿/콜백 패턴, 서비스 추상화 등의 기법이 모두 적용된 것
- 덕분에 독립적이며, 여러 프록시가 공유할 수 있는 어드바이스와 포인트컷으로 확장 기능을 분리할 수 있다
- 부가기능을 담은 어드바이스를 하나만 만들어서 싱글톤 빈으로 등록해주면, DI 설정을 통해 모든 서비스에 적용이 가능해진다
- 메소드 선정 방식이 달라지는 경우만 포인트컷의 설정을 따로 등록하고 어드바이저로 조합해서 적용해주면 된다


## 자동 프록시 생성

- 부가기능의 적용이 필요한 타깃 오브젝트마다 거의 비슷한 내용의 ProxyFactoryBean 빈 설정정보를 추가해야 함
- target 프로퍼티를 제외하면 빈 클래스의 종류, 어드바이스, 포인트컷 설정이 동일
- 빈 후처리기를 이용하여 자동으로 프록시를 생성하도록 한다
  - 빈 후처러기 중 하나인 DefaultAdfisorAutoProxyCreator를 이용
- 스프링은 빈 후처리기가 빈으로 등록되어 있으면 빈 오브젝트가 생성될 때마다 빈 후처리기에 보내서 후처리 작업을 요청한다


## 빈 후처리기를 이용한 자동 프록시 생성 방법

```xml
<bean class="org.springframework.aop.framework.autoproxy.DefaultAdvisorAutoProxyCreator" />

<bean id="transactionAdvice" class="...">
  <property name="transactionManager" ref="transactionManager" />
</bean>

<bean id="transactionPointcut" class="...">
  <property name="mappedClassName" value="*ServiceImpl" />
  <property name="mappedName" value="upgrade*" />
</bean>

<bean id="transactionAdvisor" class="org.stpringframework.aop.support.DefaultPointcutAdvisor">
  <property name="advice" ref="transactionAdvice" />
  <property name="pointcut" ref="transactionPointcut" />
</bean>

<bean id="userService" class="...ServiceImpl">
  <procerty name="...Dao" ref="...Dao" />
</bean>
```


- DefaultAdvisorAutoProxyCreator 빈 후처리기가 등록되어 있으면 스프링은 빈 오브젝트를 만들 때마다 후처리기에 빈을 보낸다
  - `<bean class="org.springframework.aop.framework.autoproxy.DefaultAdvisorAutoProxyCreator" />` 한 줄만으로 등록이 된다
  - 다른 빈에서 참조되거나 코드에서 빈 이름으로 조회될 필요가 없는 빈이라면 아이디를 등록하지 않아도 무방하다
- DefaultAdvisorAutoProxyCreator는 빈으로 등록된 모든 어드바이저 내의 포인트컷을 이용해 전달받은 빈이 프록시 적용 대상인지 확인한다
- 프록시 적용 대상이면 그때는 내장된 프록시 생성기에게 현재 빈에 대한 프록시를 만들게 하고, 만들어진 프록시에 어드바이저를 연결해준다
- 빈 후처리기는 프록시가 생성되면 원래 컨테이너가 전달해준 빈 오브젝트 대신 프록시 오브젝트를 컨테이너에게 돌려준다
- 컨테이너는 최종적으로 빈 후처리기가 돌려준 오브젝트를 빈으로 등록하고 사용한다
- 모든 빈에 대해 프록시 자동 적용 대상을 선별해야 하는 빈 후처리기인 DefaultAdvisorAutoProxyCreator는 클래스와 메소드 선정 알고리즘을 모두 갖고 있는 포인트컷이 필요
- ProxyFactoryBean을 등록하지 않아도 된다
  - 더 이상 명시적인 프록시 팩토리 빈을 등록하지 않으므로 구현 객체(빈)의 아이디를 바꾸지 않아도 된다

 
## 포인트컷 표현식을 이용한 포인트컷

- 위의 포인트컷은 메소드의 이름과 클래스의 이름 패턴을 각각 클래스 필터와 메소드 매처 오브젝트로 비교해서 선정하는 방식
- 포인트컷 표현식을 지원하는 포인트컷을 적용하려면 AspectJExpressionPointcut 클래스를 사용
- AspectJExpressionPointcut은 클래스와 메소드의 선정 알고리즘을 포인트컷 표현식을 이용해 한 번에 지정할 수 있게 해준다
- 스프링이 사용하는 포인트컷 표현식은 AspectJ 프레임워크에서 제공하는 것을 가져와 일부 문법을 확장해서 사용하는 것으로, AspectJ 포인트컷 표현식이라고도 한다


## 포인트컷 표현식 문법

`execution([접근제한자 패턴] 타입 패턴 [타입 패턴.]이름패턴 (타입패턴 | "..", ...) [throws 예외 패턴])`

- AspectJ 포인트컷 표현식은 포인트컷 지시자를 이용해 작성
  - 대표적으로 사용하는것은 execution() 지시자
- 접근제한자: public, protected, private 등. 생략 시 해당 항목에 대해서는 조건을 부여하지 않음
- 리턴 값 타입: 포인트컷의 표현식에서 리턴 값의 타입은 필수항목. `*`를 써서 모든 타입을 선택할 수 있음
- 패키지와 타입 이름을 포함한 클래스의 타입 패턴: 생략 시 모든 타입을 허용. 
  - 뒤에 이어나오는 메소드 이름 패턴과 `.`으로 연결되기 때문에 작성 시 구분에 유의
  - 패키지 이름과 클래스 또는 인터페이스 이름에 `*`를 사용 가능
  - `..`를 사용하면 한 번에 여러 개의 패키지를 선택할 수 있음
- 메소드 이름 패턴: 필수 항목. 모든 메소드들을 다 선택하고자 하면 `*`를 사용
- 메소드 파라미터의 타입 패턴: 메소드 파라미터의 타입을 `,`로 구분하면서 순서대로 적음. 필수 항목
  - 파라미터가 없는 메소드를 지정하고 싶다면 `()`
  - 파라미터의 타입과 개수에 상관없이 모두 다 허용하는 패턴으로 만들려면 `..`
  - `...`를 이용해서 뒷부분의 파라미터 조건만 생략 가능
- 예외 타입: 예외 이름에 대한 타입 패턴. 생략 가능


## 현재까지의 트랜잭션 적용 과정

### 트랜잭션 서비스 추상화

- 트랜잭션 경계설정 코드를 비즈니스 로직을 담은 코드에 넣으면서 맞닥뜨린 첫 번째 문제는 특정 트랜잭션 기술에 종속되는 코드가 돼버린다는 것
- 트랜잭션 적용이라는 추상적인 작업 내용은 유지한 채로 구체적인 구현 방법을 자유롭게 바꿀 수 있도록 서비스 추상화 기법을 적용
- 구체적인 구현 내용을 담은 의존 오브젝트는 런타임 시에 다이내믹하게 연결해준다는 DI를 활용한 접근방법.
- 인터페이스와 DI를 통해 무엇을 하는지는 남기고, 그것을 어떻게 하는지를 분리한 것

### 프록시와 데코레이터 패턴

- 트랜잭션을 어떻게 다룰 것인가는 추상화를 통해 코드에서 제거했지만, 여전히 비즈니스 로직 코드에는 트랜잭션을 적용하고 있다는 사실이 드러남
- 트랜잭션은 거의 대부분의 비즈니스 로직을 담은 메소드에 필요하며, 트랜잭션의 경계설정을 담당하는 코드의 특성 상 단순한 추상화와 메소드 추출 방법으로는 더 이상 제거할 방법이 없었음
- DI를 이용해 데코레이터 패턴을 적용하여 해결
- 트랜잭션을 처리하는 코드는 일종의 데코레이터에 담겨서, 클라이언트와 비즈니스 로직을 담은 타깃 클래스 사이에 존재하도록 만든다
- 클라이언트가 일종의 대리자인 프록시 역할을 하는 트랜잭션 테코레이터를 거쳐서 타깃에 접근할 수 있게 되었다

### 다이내믹 프록시와 프록시 팩토리 빈

- 비즈니스 로직 인터페이스의 모든 메소드마다 트랜잭션 기능을 부여하는 코드를 넣어 프록시 클래스를 만드는 작업이 오히려 큰 짐이 됨
- 트랜잭션 기능을 부여하지 않아도 되는 메소드조차 프록시로서 위임 기능이 필요하기 때문에 일일이 다 구현을 해줘야 했음
- 해결책으로 프록시 클래스 없이도 프록시 오브젝트를 런타임 시에 만들어주는 JDK 다이내믹 프록시 기술을 적용
- 일부 메소드에만 트랜잭션을 적용해야 하는 겨웅에는 메소드를 선정하는 패턴 등을 이용
- 하지만 동일한 기능의 프록시를 여러 오브젝트에 적용할 경우 오브젝트 단위로는 중복이 일어나는 문제는 해결하지 못 함
- JDK 다이내믹 프록시와 같은 프록시 기술을 추상화한 스프링의 프록시 팩토리 빈을 이용해서 다이내믹 프록시 생성 방법에 DI를 도입
- 내부적으로 템플릿/콜백 패턴을 활용하는 스프링의 프록시 패곹리 빈 덕분에 부가기능을 담은 어드바이스와 부가기능 선정 알고리즘을 담은 포인트컷은 프록시에서 분리가 가능해졌으며 여러 프록시에서 공유해서 사용할 수 있게 됨

### 자동 프록시 생성 방법과 포인트컷

- 트랜잭션 적용 대상이 되는 빈마다 일일이 프록시 팩토리 빈을 설정해줘야 하는 부담
- 스프링 컨테이너의 빈 생성 후처리 기법을 활용해 컨테이너 초기화 시점에 프록시를 만들어주는 방법을 도입
- 프록시를 적용할 대상을 일일이 지정하지 않고 패턴을 이용해 자동으로 선정할 수 있도록, 클래스를 선정하는 기능을 담은 확장된 포인트컷을 사용
  - 트랜잭션 부가기능을 어디에 적용할지에 대한 정보를 포인트컷이라는 독립적은 정보로 완전히 분리 가능해짐
  - 포인트컷 표현식을 사용하여 보다 편리하게 적용 대상을 선택할 수 있게 됨

### 부가기능의 모듈화

- 트랜잭션 경계설정과 같이 애플리케이션 전반에 흩어진 기능은 독립된 모듈로 만드려면 특별한 기법이 필요함
- 클래스를 만들지 않고도 새로운 구현 기능을 가진 오브젝트를 다이내믹하게 만들어내는 다이내믹 프록시
- IoC/DI 컨테이너의 빈 생성 작업을 가로채서 빈 오브젝트를 프록시로 대체하는 빈 후처리 기술 등


## AOP(Aspect Oriented Programming)

- 애스팩트: 그 자체로 애플리케이션의 핵심 기능을 담고 있지는 않지만, 애플리케이션을 구성하는 중요한 한 가지 요소이고, 핵심기능에 부가되어 의미를 갖는 특별한 모듈
- 애스팩트라는 모듈을 만들어서 설계하고 개발하는 방법을 AOP라 함
- 애스팩트는 부가될 기능을 정의한 코드인 어드바이스와, 어드바이스를 어디에 적용할지를 결정하는 포인트컷을 함께 가지고 있음
- AOP는 OOP를 돕는 보조적인 기술이지 OOP를 완전히 대체하는 새로운 개념은 아님
- 애플리케이션을 다양한 측면에서 독립적으로 모델링하고, 설계하고, 개발할 수 있도록 만들어주는 것


## AOP 적용 기술

### 프록시를 이용한 AOP

- 스프링은 IoC/DI 컨테이너와 다이내믹 프록시, 데코레이터 패턴, 프록시 패턴, 자동 프록시 생성 기법, 빈 오브젝트의 후처리 조작 기법 등의 다양한 기술을 조합해 AOP를 지원
- 그 중 가장 핵심은 프록시를 이용했다는 점
- 프록시로 만들어서DI로 연결된 빈 사이에 적용해 타깃의 메소드 호출 과정에 참여해서 부가기능을 제공하도록 만들어졌기 때문에, 스프링 AOP는 자바의 기본 JDK와 스프링 컨테이너 외에는 특별한 기술이나 환경을 요구하지 않음
- 스프링 AOP의 부가기능을 담은 어드바이스가 적용되는 대상은 오브젝트의 메소드
- 프록시 방식을 사용했기 때문에 메소드 호출 과정에 참여해서 부가기능을 제공해주게 되어 있음


### 바이트코드 생성과 조작을 통한 AOP

- AOP 기술의 원조이자, 가장 강력한 AOP 프레임워크로 꼽히는 AspectJ는 프록시를 사용하지 않는 대표적인 AOP 기술
- 타깃 오브젝트를 뜯어고쳐서 부가기능을 직접 넣어주는 직접적인 방식을 사용
- 컴파일된 타깃의 클래스 파일 자체를 수정하고나 클래스가 JVM에 로딩되는 시점을 가로채서 바이트코드를 조작하는 복잡한 방법을 사용
- 바이트코드를 조작해서 타킷 오브젝트를 직접 수정해버리면 스프링과 같은 DI 컨테이너의 도움을 받아서 자동 프록시 생성 방식을 사용하지 안항도 AOP를 적용할 수 있음
- 프록시 방식보다 훨씬 강력하고 유연한 AOP가 가능해짐
- 대부분의 부가기능은 프록시 방식을 사용해 메소드의 호출 시점에 부여하는 것으로도 충분한 편
- JVM의 실행 옵션을 변경하거나, 별도의 바이트코드 컴파일러를 사용하거나, 특별한 클래스 로더를 사용해야 한다


## AOP의 용어

### 타깃

- 부가기능을 부여할 대상
- 핵심기능을 담은 클래스일 수도 있지만, 경우에 따라서는 다른 북가기능을 제공하는 프록시 오브젝트일 수도 있음

### 어드바이스

- 타깃에게 제공할 부가기능을 담은 모듈
- 오브젝트로 정의하기도 하지만 메소드 레벨에서 정의할 수도 있다
- MethodInterceptor처럼 메소드 호출 과정에 전반적으로 참여하는 것도 있지만, 예외가 발생했을 때만 동작하는 어드바이스처럼 메소드 호출 과정의 일부에서만 동작하는 어드바이스도 있음

### 조인 포인트

- 어드바이스가 적용될 수 있는 위치
- 스프링의 프록시 AOP에서 조인 포인트는 메소드의 실행 단계 뿐
- 타깃 오브젝트가 구현한 인터페이스의 모든 메소드는 조인 포인트가 됨

### 포인트컷

- 어드바이스를 적용할 조인 포인트를 선별하는 작업 또는 그 기능을 정의한 모듈
- 스프링 AOP의 조인 포인트는 메소드의 실행이므로 스프링의 포인트컷은 메소드를 선정하는 기능을 가짐
- 포인트컷 표현식은 메소드의 실행이라는 의미인 execution으로 시작하고, 메소드의 시그니처를 비교하는 방법을 주로 이용함
- 메소드는 클래스 안에 존재하는 것이기 때문에 메소드 선정이란 결국 클래스를 선정하고 그 안의 메소드를 선정하는 과정을 거치게 된다

## 프록시

- 클라이언트와 타킷 사이에 투명하게 존재하면서 부가기능을 제공하는 오브젝트
- DI를 통해 타깃 대신 클라이언트에게 주입되며, 쿨라이언트의 메소드 호출을 대신 받아서 타깃에 위임해주면서, 그 과정에서 부가기능을 부여함
- 스프링은 프록시를 이용해 AOP를 지원

### 어드바이저

- 포인트컷과 어드바이스를 하낫씩 갖고 있는 오브젝트
- 어떤 부가기능(어드바이스)을 어디에(포인트컷) 전달할 것인가를 알고 있는 AOP의 가장 기본이 되는 모듈
- 스프링은 자동 프록시 생성기가 어드바이저를 AOP 작업의 정보로 활용함
- 어드바이저는 스프링 AOP에서만 사용되는 특별한 용어이고, 일반적인 AOP에서는 사용되지 않음

### 애스팩트

- 애스팩트는 AOP의 기본 모듈
- 한 개 또는 그 이상의 포인트컷과 어드바이스의 조합으로 만들어지며 보통 싱글톤 형태의 오브젝트로 존재
- 클래스와 같은 모듈 정의와 오브젝트와 같은 실체(인스턴스)의 구분이 특별히 없음
- 스프링의 어드바이저는 아주 단순한 애스팩트라고 볼 수도 있다


## AOP 네임스페이스

- 스프링의 프로시 방식 AOP를 적용하려면 최소한 네 가지 빈을 등록해야 함
  - 자동 프록시 생성기
  - 어드바이스
  - 포인트컷
  - 어드바이저
- 스프링은 AOP를 위해 기계적으로 적용하는 빈들을 간편한 방법으로 등록할 수 있도록 AOP와 관련된 태그를 정의해둔 aop 스키마를 제공
- aop 스키마에 정의된 태그는 별도의 네임스페이스를 지정해서 디폴트 네임스페이스의 <bean> 태그와 구분해서 사용할 수 있음


## 트랜잭션 전파

- 트랜잭션의 경계에서 이미 진행 중인 트랜잭션이 있을 때 또는 없을 때 어떻게 동작할 것인가를 결정하는 방식
- PROPAGATION_REQUIRED: 가장 많이 사용되는 트랜잭션 전파 속성. 진행 중인 트랜잭션이 없으면 새로 시작하고, 이미 시작된 트랜잭션이 있으면 이에 참여
- PROPAGATION_REQUIRES_NEW: 항상 새로운 트랜잭션을 시작. 앞에서 시작된 트랜잭션이 있든 없든 상관없이 새로운 트랜잭션을 만들어서 독자적으로 동작
- PROPAGATION_NOT_SUPPORTED: 트랜잭션 없이 동작하도록 만든다. 진행 중인 트랜잭션이 있어도 무시. 트랜잭션 경계설정은 보통 AOP를 이용해 한 번에 많은 메소드에 동시에 적용하게 되는데, 그 중에서 특별한 메소드만 트랜잭션 적용에서 제외하기 위해 사용


## 격리 수준

- 모든 DB 트랜잭션은 격리 수준을 가지고 있어야 함
- DefaultTransactionDefinition에 설정된 격리수준은 ISOLATION_DEFAULT
  - DataSource에 설정된 디폴트 격리수준을 그대로 따른다는 뜻
- 기본적으로는 DB나 DataSource에 설정되어 있는 디폴트 격리수준을 그대로 따르는 편이 좋지만, 특별한 작업을 수행하는 메소드의 경우는 독자적인 격리수준을 지정할 필요가 있음


## 제한시간

- 트랜잭션을 수행하는 제한시간을 설정할 수 있음
- DefaultTransactionDefinition의 기본 설정은 제한시간
- 제한시간은 트랜잭션을 직접 시작할 수 있는 PROPAGATION_REQUIRED나 PROPAGATION_REQUIRES_NEW와 함께 사용해야만 의미 있음


## 읽기전용

- 트랜잭션 내에서 데이터를 조작하려는 시도를 막음
- 데이터 액세스 기술에 따라 성능이 향상될 수 있음


## TransactionInterceptor

- 스프링에는 편리하게 트랜잭션 경계설정 어드바이스로 사용할 수 있도록 만들어진 TransactionInterceptor가 존재
- PlatformTransactionManager와 Properties 타입의 두 가지 프로퍼티를 가짐
  - Properties: transactionAttributes. 트랜잭션 속성을 정의한 프로퍼티
  - 트랜잭션 속성은 TransactionDefinition의 네 가지 기본 항목에 rollbackOn()메소드를 하나 더 가지고 있는 TransactionAttribute 인터페이스로 정의됨
  - rollbackOn() 메소드는 어떤 예외가 발생하면 롤백을 할 것인가를 결정하는 메소드


## 메소드 이름 패턴을 이용한 트랜잭션 속성 지정

| PROPAGATION_NAME | ISOLATION_NAME | readOnly | timeout_NNNN | -Exception1 | +Exception2 |
|-|-|-|-|-|-|
| 트랜잭션 전파 방식 | 격리수준 | 읽기전용 항목 | 제한시간 | 체크 예외 중 롤백 대상 | 런타임 예외지만 롤백시키지 않을 예외 |

- Properties 타입의 transactionAttributes 프로퍼티는 메소드 패턴과 트랜잭션 속성을 키와 값으로 갖는 컬렉션
- 트랜잭션 전파 항목만이 필수
- 속성을 하나의 문자열로 표현하게 만든 이유는 트랜잭션 속성을 메소드 패턴에 따라 여러 개를 지정해줘야 하는데, 일일이 중첩된 태그와 프로퍼티로 설정하게 만들면 번거롭기 때문
- 트랜잭션 속성 중 readOnly나 timeout 등은 트랜잭션이 처음 시작될 때가 아니라면 적용되지 않음
- 프로퍼티의 키 값으로 메소드 이름 패턴을 정의하며, 메소드 이름이 하나 이상의 패턴과 일치하는 경우 가장 정확히 일치하는 것이 적용됨


## 트랜잭션 포인트컷 표현식은 타입 패턴이나 빈 이름을 이용

- 일반적으로 트랜잭션을 적용할 타깃 클래스의 메소드는 모두 트랜잭션 적용 후보가 되는 것이 바람직
- 조회의 경우에는 읽기 전용으로 트랜잭션 속성을 설정해두면 그만큼 성능의 향상을 가져올 수 있음
- 트랜잭션용 포인트컷 표현식에는 메소드나 파라미터, 예외에 대한 패턴을 정의하지 않는게 바람직
- 가능하면 클래스보다는 인터페이스 타입을 기준으로 타입 패턴을 적용하는 것이 좋음
- bean() 표현식은 빈 이름을 기준으로 선정하기 때문에 클래스나 인터페이스 이름에 일정한 규칙을 만들기 어려운 경우에 유용


## 공통된 메소드 이름 규칙을 통해 최소한의 트랜잭션 어드바이스와 속성을 정의

- 하나의 애플리케이션에서 사용할 트랜잭션 속성의 종류는 그다지 다양하지 않음
- 기준이 되는 몇 가지 트랜잭션 속성을 정의하고 그에 따라 적절한 메소드 명명 규칙을 만들어두면 하나의 어드바이스만으로 애플리케이션의 모든 서비스 빈에 트랜잭션 속성을 지정할 수 있다
- 트랜잭션 적용 대상 클래스의 메소드는 일정한 명명 규칙을 따르게 해야 한다
- 일반화하기에는 적당하지 않은 트별한 트랜잭션 속성이 필요한 타깃 오브젝트에 대해서는 별도의 어드바이스와 포인트컷 표현식을 사용하는 편이 좋음


## 프록시 방식 AOP는 같은 타깃 오브젝트 내의 메소드를 호출할 때는 적용되지 않음

- 프록시 방식의 AOP에서는 프록시를 통한 부가기능의 적용은 클라이언트로부터 호출이 일어날 때만 가능
- 타깃 오브젝트가 자기 자신의 메소드를 호출할 때는 프록시를 통한 부가기능의 적용이 일어나지 않음
- 같은 오브젝트 안에서의 호출은 새로운 트랜잭션 속성을 부여하지 못한다는 사실을 의식하고 개발할 필요성이 있음


## 트랜잭션 경계설정의 일원화

- 일반적으로 특정 계층의 경계를 트랜잭션 경게외 일치시키는 것이 바람직
- 서비스 계층을 트랜잭션이 시작되고 종료되는 경계로 지정했다면, 테스트와 같은 특별한 이유가 아니고는 다른 계층이나 모듈에서 DAO에 직접 접근하는 것은 차단해야 함
- 트랜잭션은 보통 서비스 계층의 메소드 조합을 통해 만들어지기 때문에 DAO가 제공하는 주요 기능은 서비스 계층에 위임 메소드를 만들어둘 필요가 있음


## 애노테이션 트랜잭션 속성과 포인트컷

- 가끔은 클래스나 메소드에 따라 제각각 속성이 다른, 세밀하게 튜닝된 트랜잭션 속성을 적용해야 하는 경우도 있음
- 설정파일에서 패턴으로 분류 가능한 그룹을 만들어서 일괄적으로 속성을 부여하는 대신 직접 타깃에 트랜잭션 속성정보를 가진 애노테이션을 지정하는 방법
- `@Transactional` 애노테이션을 이용


## @Transactional

- @Transactional의 타깃은 메소드와 타입
- 트랜잭션 속성정보로 사용하도록 지정하면 스프링은 @Transactional이 부여된 모든 오브젝트를 자동으로 타깃 오브젝트로 인식
- @Transactional이 타입 레벨이든 메소드 레벨이든 상관없이 부여된 빈 오브젝트를 모두 찾아서 포인트컷의 선정 결과로 돌려줌


## 트랜잭션 속성을 이용하는 포인트컷

- TransactionInterceptor는 메소드 이름 패턴을 통해 부여되는 일괄적인 트랜잭션 속성정보 대신 @Transactional 애노테이션의 엘리먼트에서 트랜잭션 속성을 가져오는 AnnotationTransactionAttributeSource를 사용
- 포인트컷도 @Transactional을 통한 트랜잭션 속성정보를 참조하도록 만든다
  - @Transactional로 트랜잭션 속성이 부여된 오브젝트라면 포인트컷의 선정 대상이기도 하기 때문
- 트랜잭션 부가기능 적용 단위는 메소드이므로, 메소드마다 @Transactional을 부여하고 속성을 지정할 수 있음


## 대체 정책(fallback)

- 스프링은 @Transactional을 적용할 떄 4단계의 대체 정책을 이용하게 해줌
- 메소드의 속성을 확인할 때 타깃 메소드, 타깃 클래스, 선언 메소드, 선언 타입의 순서에 따라서 @Transactional이 적용되었는지 차례로 확인하고, 가장 먼저 발견되는 속성정보를 사용하게 하는 방법
- @Transactional을 사용하면 대체 정책을 잘 활용해서 애노테이션 자체는 최소한으로 사용하면서도 세밀한 제어가 가능
- 기본적으로 @Transactional 적용 대상은 클라이언트가 사용하는 인터페이스가 정의한 메소드이므로 @Transactional도 인터페이스에 두는 것이 바람직하지만, 인터페이스를 사용하는 프록시 방식의 AOP가 아닌 방식으로 트랜잭션을 적용하면 인터페이스에 정의한 @Transactional은 무시되기 때문에 안전하게 타깃 클래스에 @Transactional을 두는 방법을 권장


## 트랜잭션 애노테이션 사용을 위한 설정

`<tx:annotation-driven />`

- 위 태그 하나로 트랜잭션 애노테이션을 이용하는 데 필요한 어드바이저, 어드바이스, 포인트컷, 애노테이션을 이요하는 트랜잭션 속성정보가 등록됨


## 테스트를 위한 트랜잭션 애노테이션

### @Tranactional

- 테스트에도 @Tranactional을 적용할 수 있음
- 테스트에서 사용하는 @Tranactional은 AOP를 위한 것은 아님. 단지 컨텍스트 테스트 프레임워크에 의해 트랜잭션을 부여해주는 용도로 쓰일 뿐이다
- 테스트용 트랜잭션은 테스트가 끝나면 자동으로 롤백된다(롤백 테스트가 됨)

### @Rollback

- 롤백 기능을 제어하기 위한 애노테이션
- @Transactional은 기본적으로 테스트에서 사용할 용도로 만든게 아니기 때문에 롤백 테스트에 관한 설정을 담을 수 없음
- 롤백 여부를 지정하는 값을 가짐
  - 기본값은 true
  - 롤백을 원하지 않는다면 @Rollback(false)

### @TransactionConfiguration

- 롤백 여부에 대한 기본 설정과 트랜잭션 매니저 빈을 지정하는데 사용하는 애노테이션
- @Rollback 애노테이션은 메소드 레벨에만 적용 가능하지만, @TransactionConfiguration은 클래스 레벨에 부여가 가능하다


### @NotTransactional과 Propagation.NEVER

- @NotTransactional을 테스트 메소드에 부여하면 클래스 레벨의 @Transactional 설정을 무시하고 트랜잭션을 시작하지 않은 채로 테스트를 진행
- 스프링 3.0에서 제거 대상이 됨
- @Transactional(propagation=Propagation.NEVER)으로 트랜잭션 전파 속성을 사용하면 트랜잭션이 시작되지 않음
- 트랜잭션 테스트와 비 트랜잭션 테스트를 아예 클래스를 구분해서 만들것을 권장


## 정리

- 트랜잭션 경계설정 코드를 분리해서 별도의 클래스로 만들고 비즈니스 로직 클래스와 동일한 인터페이스를 구현하면 DI의 확장 기능을 이용해 클라이언트의 변경 없이도 깔끔하게 분리된 트랜잭션 부가기능을 만들 수 있음
- 트랜잭션처럼 환경과 외부 리소스에 영향을 받는 코드를 분리하면 비즈니스 로직에만 충실한 테스트를 만들 수 있음
- 목 오브젝트를 활용하면 의존관계 속에 있는 오브젝트도 손쉽게 고립된 테스트로 만들 수 있음
- DI를 이용한 트랜잭션의 분리는 데코레이터 패턴과 프록시 패턴으로 이해될 수 있다
- 번거로운 프록시 클래스 작성은 JDK의 다이내믹 프록시를 사용하면 간단하게 만들 수 있음
- 다이내믹 프록시는 스태틱 팩토리 메소드를 사용하기 때문에 빈으로 등록하기 번거롭기 때문에 팩토리 빈으로 만들어야 한다
  - 스프링은 자동 프록시 생성 기술에 대한 추상화 서비스를 제공하는 프록시 팩토리 빈을 제공함
- 프록시 팩토리 빈의 설정이 반복되는 문제를 해결하기 위해 자동 프록시 생성기와 포인트컷을 활용할 수 있음
  - 자동 프록시 생성기는 부가기능이 담긴 어드바이스를 제공하는 프록시를 스프링 컨테이너 초기화 시점에 자동으로 만들어줌
- 포인트컷은 AspectJ 포인트컷 표현식을 사용해서 작성하면 편리하다
- AOP는 OOP만으로는 모듈화하기 힘든 부가기능을 효과적으로 모듈화하도록 도와주는 기술
- 스프링은 자주 사용되는 AOP 설정과 트랜잭션 속성을 지정하는 데 사용할 수 있는 전용 태그를 제공
- AOP를 이용해 트랜잭션 속성을 지정하는 방법에는 포인트컷 표현식과 메소드 이름 패턴을 이용하는 방법과 타깃에 직접 부여하는 @Transactional 애노테이션을 사용하는 방법이 있음
- @Transactional을 이용한 트랜잭션 속성을 테스트에 적용하면 손쉽게 DB를 사용하는 코드의 테스트를 만들 수 있음