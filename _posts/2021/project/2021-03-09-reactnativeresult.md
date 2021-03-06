---
title: 두번째 react native 채팅앱 만들기

layout: single

author_profile: true

read_time: false

comments: true

share: true

related: true

categories:
- project

tag:
- result

description: java & srping으로 채팅만드려다가 react native로 만들게 된 이야기

meta_keywords: Java, Spring

last_modified_at: '2021-03-09 14:00:00 +08000'

toc: true

toc_sticky: true

toc_label: 목차
popular: true
---
<br>

### 갑자기 왜 리엑트 네이티브로 앱을 만들게 되었을까?

![](https://images.velog.io/images/42seouler/post/f9189f4d-1663-449a-96b4-df46cebdbe37/img.png)

42과정을 하면서 C -> CPP -> JAVA 공부를 하게 되었습니다.  
왜냐하면 채팅 서버를 만들기 위해서 스프링을 사용하려고 했습니다.  
단순하게 Restful API를 통해 애플리케이션을 만든다고 알고 있었습니다.  
애플리케이션을 구성하기 위해 js는 어떻게 구성되고 어떤식으로 처리하는지 궁금했습니다.
하지만 프론트엔드라고 하는 html, css, js 이 요소에 관해 지식이 단 1도 없었습니다.

> "처음엔 백엔드만 하면되지 무슨 프론트엔드까지 하냐? 풀스텍은 무리야" 라고 생각했습니다.


![](https://images.velog.io/images/42seouler/post/cdda8d7d-aa7c-4b6e-98d3-34a05e085d0b/image.png)

# 하지만! 다 경험해봐야 한다고 하셨다.

그래서 저는 자바로 만든 채팅서버를 테스트 하기위해 꼼수를 부리기로 했습니다.
html, css를 직접 다루지 않는 react-native를 하기로 했습니다. 이때까지만 해도 잘 생각 한줄 알았습니다.

#### 자바 스프링으로 백엔드만 만들면서 혼자 상상 했던 것들.

백엔드, 프론트 어디서 처리하는 것인지 잘 모르던 것들입니다.

**1. 내가 보내는 메시지는 오른쪽 타인은 왼쪽으로 표현하기.**

![](https://images.velog.io/images/42seouler/post/2a6499ef-061e-4bdd-a299-9fc186cc743b/image.png)

위와 같은 구조를 백엔드서버에서 저장하고 프론트엔드에서 그대로 표현해주는 것인지 궁금했습니다.

```js
{messages.map(({id, data}) => (
       data.email === auth.currentUser.email ? (
           <View>메시지의 주인이 본인이면 행하는 로직</View>
       ) : (
            <View>메시지의 주인이 타인이면 행하는 로직</View>
       )
))}
```
위와 같이 객체를 맵으로 돌면서 style의 Right, Left를 통해 처리 할 수 있었습니다.
이를 통해 굳이 따로 저장 할 필요없이 프론트에서 처리하면 되겠구나 생각했습니다.

**2. 백엔드 서버와 프론트엔드 서버는 코드의 어느 부분에 상호작용할까?**

간단히 데이터를 주고 받는다고 알고 있었기에 지지고 볶고 해서 유효한 URL를통해 get, post등을
하지 않을까라고 막연하게 생각하고 있었습니다. 그래서 가끔 이야기 할 때 안갯속에서 이야기 하는
기분으로 이야기 할 때가 있었습니다.

현재 코드 블럭은 메시지를 보내는 메시지창의 코드 부분입니다.

![](https://images.velog.io/images/42seouler/post/eb3f8339-d707-44ff-af2f-79bc5b113b7a/image.png)

사용자가 입력창을 통해 텍스트를 입력하게 되고 엔터를 누르면
onpress를 통해 sendMessage를 호출하게 되고 메시지 규칙에 따라 프론트에서 백엔드로 메시지를 전송합니다.  
이를 통해 프론트엔드 코드가 어디서 어떻게 사용자의 행동에 반응 할 수 있는지 알 수 있었습니다.


**3. 왜 firebase를 주로 쓸까?**

파이어베이스를 활용하는 교육자료가 많았습니다. 파이어베이스를 사용하면 서버에서 수행 해야하는 로직들을
API를 통해 해결 할 수 있어서 서버를 구성하지 않고도 애플리케이션을 만들 수 있었습니다.
하지만 그를 통해 서버를 어떻게 구성해야 하는지 중요한 것을 놓칠 수도 있다고 생각했습니다.


### react-native + firebase로 1:1 단순 채팅 완성

<p align="center"><img src="https://images.velog.io/images/42seouler/post/234f0df7-f2fb-4249-8780-f702ae52e622/nativefirebase.gif"/></p>

js에 관해 단 1도 알지 못했지만 풍부한 인터넷 자료를 통해 단순하게 만들어 볼 수 있었습니다.
이를 통해 프론트, 백엔드의 통신 방법, 어느 부분에서 처리 해줘야할 책임인지를 알 수 있었습니다.

# 이호준 멘토님의 피드백

1. DOM, html, js, v8, 브라우저, 애플리케이션이란 무엇인지 핵심을 설명해주셨습니다.
- 무턱대고 자바스크립트, 리액트를 할 것이 아니라 브라우저의 작동방식부터 이해해야 겟다고 생각했습니다.

2. Spring도 MVC 고 django도 MTV 인데 왜 리액트 같은걸 포함해서 만들지 않을까?
- 서버에서 포함해서 만들 수 있지만 그렇게 되면 서버의 부하가 커진다. 서버는 데이터를 핸들링한다.
- 포함해서 만들어도 되지만 바람직하지 않을 수 있다. 해보면 안다.

3. 리액트 + 자바스프링을 통해 채팅이라는 시퀀스를 구현해보기.
- 파편화 되어있는 경험과 상상을 하나의 결과물을 만들면서 최대한 하나로 만들기

## 결론

# 큰 반성
> 프론트, 백엔드 하나만 제대로 하기도 어려운데 그림 그리는 것 보다 로직을 만드는게 더 좋아
그러니까 나는 백엔드 해야지라고 생각하고 그렇게 말하고 행동 했던 자신을 반성합니다.
결국 프론트, 백엔드 모두 로직이 있는 것이고 역활이 다른 것인데 그리고 둘다 경험해봐야 웹 프로그래밍이
무엇인지 이해 할 수 있었는데 하기 싫어서 주워 들은 걸로 상상의 날개를 펼쳤었는데 그러지 말았어야 했습니다.

#### 나의 생각

이번 경험을 통해 단 한번도 중요하게 생각하지 않았던 브라우저에 공부하고 이해해야 한다고 생각했습니다.
단순히 프론트엔드를 만들면 브라우저를 통해 보여지고 백엔드 서버는 프론트랑 데이터를 주고 받겠지 라고
생각했었습니다. 정말 짧은 생각이었다는 것을 알았습니다.
나는 왜 웹페이지가 아닌 애플리케이션부터 만들게 되었는지
왜 자꾸 프론트, 백엔드의 역활에 관해 끊임없이 혼란스러워 하는지
등등 많은 오해와 상상에 관해 실마리를 경험하게 해주셨습니다.


#### 앞으로의 공부 순서

브라우저 -> html -> css -> js -> 리액트 순으로 나아가면서 공부 해볼 예정입니다.
3월 15일 이전까지 프론트 + 백엔드를 합쳐서 하나의 채팅서버를 만들도록 할 것 입니다.











