---
layout: post
title: 객체 지향 프로그램의 특징
category: S/W Architecture
permalink: /sw-architecture/:year/:month/:day/:title/

tag: [객체지향, 패러다임]
---

객체 지향 프로그래밍(Object-Oriented Programming, OOP)의 특징입니다.

<br>

# Abstraction

가장 기본 적인 개념입니다. 불필요한 정보는 숨기고 필요한 정보만 일반적인 형태로 표현하는 것을 '추상화'라고 합니다.

<br>

# Inheritance

기존의 클래스의 데이터나 형태 등을 물려받는 파생 클래스를 만들어내는 것을 '상속'이라고 하고, 기존 클래스는 부모 클래스, 물려받는 클래스를 자식 클래스라고 합니다. 상속을 통해 기존 클래스의 재활용성을 높이고, 불필요한 코드의 중복을 방지할 수 있습니다. 그리고 객체간의 종속 관계를 형성하여 다형성(Polymorphism)이라는 OOP의 핵심적인 특성을 제공할 수 있게 해줍니다.

<br>

# Polymorphism

OOP의 핵심입니다. '다형성'이라는 말 그대로 특정한 요소가 다양한 형태를 가지게 되는 것을 말합니다.

예를 들면, Animal 이란 클래스가 있다고 가정하면, 이를 상속 받는 Dog 클래스와 Cat 고양이가 있을 수 있습니다.
Dog 클래스와 Cat 클래스는 언제든지 Animal 클래스로 대체해서 사용이 가능합니다.
(Dog 클래스와 Cat 클래스는 Animal의 모든 속성을 갖고 있기 때문입니다.)

이와 같이 추상화된 클래스를 상속받은 구체화된 클래스는 언제든지 부모 클래스로 취급을 받을 수 있습니다. 이러한 속성은 향후 동적 바인딩(Dynamic Binding)을 가능하게 해줍니다.

메소드 레벨에서의 다형성은 대표적으로 '오버로딩(Overloading)'과 '오버라이딩(Overriding)'이
있습니다.

<br>

## Overloading vs Overriding

Overloading은 추가해서 더 싣는다고 생각하면 되며, 파라메터만 바꿔서 같은 이름의 메소드(함수)를 생성할 수 있는 것을 말합니다. 컴파일시에 결정되며 런타임 중에는 변경될 수 없습니다.

Overriding은 아예 태우는 것이라고 생각하면 되며, 상속시 부모 클래스와 자식 클래스의 메소드가 같을
경우 부모 클래스의 메소드 위에 자식 클래스를 태워서 실행시에 자식 클래스의 메소드가 호출되는 것을 말합니다. Dynamic Binding을 위한 중요한 개념입니다. Dynamic Binding은 이름 그대로 런타임 중에 변경될 수 있는 것을 의미합니다.

![image -fullwidth](/assets/2017-02-14-oop-paradigm/01.png)

