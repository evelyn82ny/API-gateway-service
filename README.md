## Spring Cloud Gateway

![png](/_img/msa_v230211.png)

account, item, order service 등 여러 Microservice의 포트 번호를 기억하고 있다면, 각 마이크로서비스의 포트 번호를 이용해 접근하면 된다. 
하지만 다양한 Microservice가 생기고, **Scale-out을 위해 여러 인스턴스를 구동한다면 모두 다른 포트 번호를 할당하고 관리하는 것이 번거로울 것**이다. 
이를 위해 Spring Cloud Gateway를 사용한다.
<br>

Gateway의 포트 번호가 8080번, Item-Service의 포트 번호가 57814번일 경우 다음 2가지 방법으로 Item-Service에 접근할 수 있다.

- ```http://localhost:8080/item-service/items/23```
- ```http://localhost:57814/item-service/items/23```

<br>

특정 Microservice를 Scale-out 하는 경우도 마찬가지다. 2개의 Item-Service를 실행하면 중복되지 않는 포트 번호를 사용해야 하고, 포트 번호를 일일이 확인하는 과정은 상당히 번거로울 것이다.
<br>

Spring Cloud Gateway를 사용하면 Gateway의 포트 번호로 등록된 모든 마이크로서비스에 쉽게 접근할 수 있다.

- ```http://localhost:8080/item-service/items/23```
- ```http://localhost:8080/order-service/orders/7```
- ```http://localhost:8080/account-service/account/9584```

<br>

## Route 등록

```yml
# application.yml
spring:
  cloud:
    gateway:
      routes:
        - id: account-service
          uri: lb://ACCOUNT-SERVICE
          predicates:
            - Path=/account-service/**
          filters:
            - RewritePath=/account-service/(?<segment>.*), /$\{segment}
        - id: item-service
          uri: lb://ITEM-SERVICE
          predicates:
            - Path=/item-service/**
          filters:
            - RewritePath=/item-service/(?<segment>.*), /$\{segment}
```

```application.yml``` 에 위와 같이 Microservice를 추가하면 된다.

<br>

![png](/_img/eureka_instances.png)

Eureka server와 Client로 등록한 Microservice, API Gateway를 실행하면 ```http://localhost:8761```에서 등록된 모든 instance를 확인할 수 있다.
<br>

API Gateway는 8080번 포트 번호로 설정했고, 모든 Microservice는 Random 포트 번호로 설정했기 때문에 실행할 때마다 랜덤하게 할당된다. 포트 번호를 확인하고 싶다면 클릭하거나 마우스 커서를 올려 왼쪽 하단에서 확인할 수 있다.
