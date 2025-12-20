---
layout: post
title: 주요 HTTP Status
category: 개발상식
permalink: /common-sense/:year/:month/:day/:title/

tag: [용어, http]
---

# 주요 HTTP Status Code

## 200 OK

정상 응답 상태 코드

## 301 Moved Permanently

HTTP 요청을 보낸 주소의 URL이 변경되었다는 상태 코드.  
보통 `Location` 헤더가 포함되는 것이 일반적이며,  
`Location` 헤더에 새로운 주소가 포함되어 있음

```
HTTP/1.1 301 Moved Permanently
Location: http://www.snowdeer.com/index.html
```

## 400 Bad Request

잘못된 요청일 때 보내는 응답 코드.  
주로 Request에 잘못된 값들이 보내졌을 때 사용함

## 401 Unauthorized

사용자가 로그인이 필요한 경우 `401`을 리턴

## 403 Forbidden

로그인은 되어 있으나, 해당 리소스에 대한 접근 권한이 없는 경우 리턴

## 404 Not Found

HTTP 요청을 보내는 URL 주소가 존재하지 않을 경우 보내는 응답 코드

## 500 Internal Server Error

서버 내부 오류가 발생했을 때 리턴하는 응답 코드
