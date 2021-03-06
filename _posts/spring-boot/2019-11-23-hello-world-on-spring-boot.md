---
layout: post
title: Hello World 프로젝트 생성하기

category: Spring Boot
permalink: /spring-boot/:year/:month/:day/:title/
tag: [SpringBoot]
---

# 프로젝트 생성하기

프로젝트 생성은 [Spring Initializer 웹사이트](https://start.spring.io)를 이용해서 생성할 수 있습니다.
또는 CLI(Command line interface)를 이용해서도 동일한 결과를 얻을 수 있습니다.

<br>

## start.spring.io 사이트 이용

해당 페이지에 접속해서 다음과 같이 항목을 선택합니다.

* Gradle Project
* Kotlin
* 2.2.1
* Group: com.snowdeer
* Artifact: hello
* Dependencies: Spring Web

그리고 `Generate` 버튼을 누르면 `hello.zip` 파일이 생성될 것입니다. 원하는 경로에 압축을 풉니다.

<br>

## CLI를 이용한 경우

<pre class="prettyprint">
spring init \
--dependencies=web \
--groupId=com.snowdeer \
--artifactId=hello \
--name="Hello SnowDeer" \
--package-name=com.snowdeer.hello \
--language=kotlin \
--version=2.2.0 \
--build gradle \
hello
</pre>

위 명령어를 이용해서 빈 프로젝트를 생성할 수 있습니다.

<br>

## curl을 이용해서 다운로드하는 방법

그 외에도 `curl` 명령어를 이용해서 가져오는 방법이 있는데, 3가지 전부 `start.spring.io`로부터 가져오는 방법이라 동일합니다.
[여기(https://spring.io/guides/tutorials/spring-boot-kotlin/)를 참고하세요.

<br>

## 실행 확인

해당 프로젝트가 있는 디렉토리로 이동하여

<pre class="prettyprint">
gradle bootRun
</pre>

명령을 내리면 기본 웹사이트가 구동이 됩니다. `http://localhost:8080`으로 접속해서 확인을 해봅니다.
'Whitelabel Error Page' 페이지가 뜨면 정상 동작하고 있는 것입니다.

<br>

## Hello World 요청 페이지 구현하기

이제 해당 프로젝트의 `src/main/kotlin/com/snowdeer/hello` 디렉토리로 이동합니다.
아마 `HelloSnowDeerApplication.kt` 파일은 자동으로 만들어졌을 것입니다.
같은 디렉토리에 `HelloController.kt` 파일을 생성하고 아래 내용을 입력합니다.

<pre class="prettyprint">
package com.snowdeer.hello

import org.springframework.web.bind.annotation.RestController
import org.springframework.web.bind.annotation.RequestMapping

@RestController
class HelloController {
  
 	@RequestMapping("/")
 	fun hello() : String {
 		return "Hello, SnowDeer"
 	}
}
</pre>

여기까지 구현한 다음, 해당 프로젝트를 새로 실행합니다. 그리고 `http://localhost:8080`에 접속하면
위에서 구현한 페이지가 정상적으로 출력되는 것을 확인할 수 있습니다.

* @RestController: 클래스 앞에 붙으며, 해당 클래스가 REST 컨트롤러 기능을 수행하도록 함
* @RequestMapping: 메소드 앞에 붙으며, 해당 함수를 실행할 수 있는 주소를 설정함

<br>

## 컴포넌트 스캔

위에서 `HelloController` 클래스를 생성하고 그 뒤로 아무런 작업을 안해도 어플리케이션이 컨트롤러를 찾고 요청을 처리하는 것을
볼 수 있습니다.
스프링 부트 어플리케이션이 시작될 때, 어플리케이션 컨텍스트 클래스 아래의 모든 클래스와 패키지를 반복적으로 스캔하고,
어떤 클래스라고 하더라도 컴포넌트 어노테이션만 추가하면 클래스의 인스턴스를 생성하고 스프링 부트 어플리케이션 컨텍스트에 추가합니다.
이러한 기능을 컴포넌트 스캔(Component Scan)이라고 합니다.

좀 더 정확히 말하면 컴포넌트 스캔은 `@Component` 어노테이션이 붙은 모든 클래스를 스캔합니다. 
위에서 `@RestController` 어노테이션을 사용했고, 그 외에도 보통 `@Controller` 어노테이션을 사용하는데
선언부로 가면 다음과 같이 `@Component` 어노테이션이 포함되어 있는 것을 확인할 수 있습니다.

<pre class="prettyprint">
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Component
public @interface Controller {

	@AliasFor(annotation = Component.class)
	String value() default "";

}
</pre>