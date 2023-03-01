---
aliases: []
categories: []
date-created: September 15, 2022 12:56 PM
Project: Recody
tags: [Spring, 개발 일반]
date-updated: 2023-02-27 05:59
---
<br><br>
## 자바 객체를 url encoded 로 변환하는 컨버터는 기본적으로 없다.

해결방법으로

1. 직접 컨버터를 구현한다.
2. 자바객체를 MultiValueMap 으로 변환하고, 이를 `FormHttpMessageConverter` 로 변환되도록 만든다.

보다 간편해 보이는 방법을 사용하기로 했다. 

하지만 MultiValueMap 을 사용하면, 필드 이름을 그대로 key 에 써야한다. 물론 따로 snake case 로 변환하여 MultiValueMap 을 만들어도 되긴 하지만 번거로운 과정이다. 가장 편한건 Serialize 하는 과정에서 자동으로 변환되게끔 하는 것. 

Map 으로 만들기

### 1 안

필드가 고정되어 있다면 이렇게 해도 되지만, 간편하게 필드를 순회할 수 있는 방법을 찾기로 했다.

```java
public MultiValueMap<String ,String> toMultiValueMap(){
		LinkedMultiValueMap<String, String> map = new LinkedMultiValueMap<>();
		map.add("client_id", this.clientId);
		map.add("refresh_token", this.refreshToken);
		map.add("grant_type", this.grantType);
		return map;
}
```

### 2 안

리플렉션을 사용해서 필드를 순회하는 방법.

필드에 setAccessible(true) 로 설정하여 이 필드 객체에 접근가능하도록 열어줘야 한다.

이때 private 필드가 열리는 것에 대해서 private 선언이 바뀌는 것인가 궁금했는데,

그건 아니고 해당 Field 인스턴스에 대해서만 적용되는 것이라고 한다.

따라서 클래스의 모든 인스턴스에 적용되는 것은 아님.

```java
public MultiValueMap<String ,String> toMultiValueMap(){
		LinkedMultiValueMap<String, String> map = new LinkedMultiValueMap<>();
		Field[] declaredFields = getClass().getDeclaredFields();
    for (Field declaredField : declaredFields) {
        String name = declaredField.getName();
        System.out.println("Field name: " + name);
        declaredField.setAccessible(true);
        Object value = declaredField.get(this);
        System.out.println("Field value: " + value);
        map.add(name, (String) value);
    }
		return map;
}
```

### 3 안

spring 의 ReflectionUtils 라는 유틸클래스를 쓰는 방법

결국 같은 리플렉션을 사용한다. 

```java
public MultiValueMap<String ,String> toMultiValueMap(){
		LinkedMultiValueMap<String, String> map = new LinkedMultiValueMap<>();
		ReflectionUtils.doWithFields(getClass(), field -> {
            String name = field.getName();
            System.out.println("Field name: " + name);
            field.setAccessible(true);
            Object value = field.get(this);
            System.out.println("Field value: " + value);
            map.add(name, (String) value);
        });
		return map;
}
```

### 4 안

아파치 커먼스 BeanUtils