---
title: 42seoul에서 채팅서버 만들기 - 1

layout: single

author_profile: true

read_time: false

comments: true

share: true

related: true

categories:
- Java

tag:
- spring

description: 생애 처음으로 TDD를 해보자!

meta_keywords: Java, Spring

last_modified_at: '2021-02-12 21:00:00 +08000'

toc: true

toc_sticky: true

toc_label: 목차
popular: true
---

자칭 42TechCourse를 진행하면서 남기는 기록.

2021-02-12(월)

### MVC vs Webflux

그래서 MVC의 다양한 예제를 따라치면서 채팅서비스의 기초를 이해 하려고 했습니다.
이 과정에서 작동하지 않은 예제코드 알지 못하는 Js 어떻게 해야하지 하는 등 일주일 간 고통의 나날을 보내면서 프로그래밍은 나의 길이 아니었던가 자책하기도 했습니다.

우여곡절 끝에 Webflux로 진행해보기로 했습니다.

### Webflux는 달랐다.

MVC에는 WebSocket Stomp의 구독과 발행을 활용해서 채팅 서비스를 구현 하는데
Webflux에는 Stomp를 지원하지 않았습니다. 저는 당황했고 어떻게 해야 할지 몰랐습니다.
그래서 webflux chat등 구글을 보라색으로 물들였지만 답을 찾지 못했습니다.
그렇게 돌고 돌아 결국 Spring docs에서 답을 찾아보기로 했습니다.

**그렇게 찾아 낸 단서**

Websocket만 활용하면 메시지 프로토콜을 애플리케이션에서 구현해야 한다.
- STOMP가 하시던 일을 내가 직접해야 한다고 이해했습니다. 그 순간 아 그냥 MVC를 쓰자 생각했지만 분명 여기에도 길이 있을거라 생각했습니다.

RSocket를 사용하면 한다면?
- RSocket은 TCP 또는 WebSocket을 통해 전달되는 양방향 통신을 할 수 있는 채널 기능이 있었습니다. 이를 통해 채팅 서비스를 구현 할 수 있지 않을가 생각했습니다.

![](https://images.velog.io/images/42seouler/post/c1966762-7f13-45ab-8dd2-39d9a0b8c092/image.png)

[RSocket channel spring.io 블로그 글](https://spring.io/blog/2020/04/06/getting-started-with-rsocket-spring-boot-channels)

### 그렇다면 이제 어떻게 구현해야 할까?

키워드는 찾았으나 어떻게 구현해야 할지 막막하다. 생각보다 Webflux 예제도 많지 않고
그리고 docs만 보고 아하 유레카하면서 할 정도는 아니기 때문입니다.
레고 조립하듯 하나하나 차근차근 올리도록 하겠습니다.

내가 원하는 예제를 찾기위한 여정.. 제대로 하는게 하나도 없다.
우선 가장 중요 한건 Rscoket연결은 단순했는데 html js를 하나도 몰라서 제대로 되는지 확인 불가.

![](https://images.velog.io/images/42seouler/post/6bcfb626-5bde-4179-9241-53fd7338cfe5/image.png)

2021-02-21(일)

### 위의 단서를 갖고 1주일 넘게 찾아보았지만 헛 수고!

무지막지하게 검색해봤지만 나의 검색 실력도 미숙 한 것 같고 !!
Rsocket으로는 어렵지 않나 생각이 되었습니다. 검색의 끝은 안된다는 것을 알았습니다.

![](https://images.velog.io/images/42seouler/post/bdf25c5f-fc1a-47b1-ab03-56eca24d7fe9/image.png)

### 그래서 한국 스프링캠프 구경 하던 중!

![](https://images.velog.io/images/42seouler/post/5888f787-4acf-48a5-9de1-3d62bb040b5e/image.png)

결국 돌고 돌아 Rsocket으로 구현하는건 포기하기로 하였습니다. 1주일 간 구글을 보라색으로 만들었지만 이 영상을 보고 그래 지원하지 않을 땐 아직 나의 레벨이 아니다.
무언가 하고 싶은데 뭘 검색 해야 할지 몰라 무작정 검색하기도 하고 뭔가 안되서 짜증나서 잠이 오질 않았습니다. 그래서 마지막으로 검색 또 검색!!
[알리바바 Broker](https://github.com/alibaba/alibaba-rsocket-broker)를 사용하면 가능 할 것도 같았지만.. 정식 지원 되면 쓰는것으로 !! 다시 WebMVC로 !!

### 메시지 모델링을 해보자

20일부터 21일까지 어떻게 메세지를 구성 해야 할지 고민했습니다.

1. User, Message 기준으로 만든다.

```java
public class Message {
    private Long messageId;
    private User from_user_id;
    private User to_user_id;
    private String content;
    private Timestamp timestamp;

```

```
// 대화 리스트
elect to_user_id from message_table
				where from_user_id = A

//모든 메시지 출력
select * from message_table 
where from_user_id = A and to_user_id = B 
	  or from_user_id = B and to_user_id = A
order by create_at desc
```

모든 메시지를 출력하기 위해 where절에 and로 검색 해야합니다.
이것은 그룹채팅에서 사람이 늘어나면 그만큼 to_user 조건문이 사람수만큼 늘어 나게 됩니다.
그리고 카카오톡처럼 새로운 메시지가 오면 대화방 목록 중 최상단에 올려줘야 하는데 그것이 가능하지 않을 것 같았습니다.

# 고민 끝에 채팅앱 디자인 하기


## 구축 하고자 하는 목표

일대일 채팅, 그룹채팅 애플레케이션을 구축하려고 합니다.
한쌍의 사용자 또는 다중 사용자가 하나의 쓰레드를 통해 통신하도록 하려고 합니다.

** 디자인 할 때 고려한 점 **

- 각자의 사용자는 자신만의 메세지 사본을 갖도록 만들 예정 입니다.
- 가장 단순한 메시지 모델에서 쿼리문이 문제가 되었기 때문에 가능 한 단일 사용자의 데이터를 같은 위치에 보관 하도록 할 것입니다.

## 엔티티

#### 사용자

- 고유한 User_id를 갖습니다. 고유키는 (email, uuid, Long)일 수 있습니다.

#### 메시지

- 사용자가 서로에게 보내는 메시지입니다. 실질적으로 메시지를 받는것은 쓰레드입니다.

#### 대화방(쓰레드)

- 대화방은 두 사용자 간에 대화를 구성하는 메시지 집합니다. 일대일, 그룹 채팅은 각 사용자 별로 하나의 쓰레드를 갖습니다. 그리고 메시지 교환은 쓰레드를 통해서만 가능합니다.

## 액션

#### 메시지 보내기 / 받기

- 메시지가 해당 사용자들의 쓰레드에 모두 추가 됩니다. 또한 수신자의 쓰레드 타임 스탬프를 업데이트하여
  맨위에 표시되도록 해야 합니다.

## 앱 모델링

![](https://images.velog.io/images/42seouler/post/0dba40ba-1f45-442d-8f29-5f2f49ff3f71/image.png)

### 사용자

- 사용자의 경우 사용자의 프로필를 모델링하고 그 정보를 가져오면 되지 않을까 싶어서 생략합니다.

### 메시지
- 메시지를 직접 유저에게 보내는 형식이 아니라 대화방(쓰레드)를 통해 전달하는 방식으로 변경하였습니다.
- 새 메시지는 지정된 쓰레드의 메시지 목록에 추가되야 합니다.
- 스레드의 메시지 목록은 시간이 지남에 따라 증가하기 때문에 마지막 50개의 메시지를 검색 할 수 있도록 합니다.
- 메시지는 일대일, 그룹채팅인지 구분하여 전달 합니다.

```java
메시지

MessageID Long 메시지 인식키
author User 메시지 작성자
content String 내용
timestamp TimeStamp 밀리세컨드로 표시 (시간 순으로 메시지를 정리하기 위해서)
forwarded boolean 메시지가 제대로 전달 되었는지 확인
thread_id Long 대화방의 식별키
thread_type String 개인 또는 그룹 채팅인지 구분함

```

### 대화방

- owner_id를 통해 사용자가 참여한 모든 쓰레드를 검색 할 수 있습니다.
- update_at을 통해 최신 메시지가 온 순서대로 쓰레드를 정렬 할 수 있습니다.

```java
대화방(쓰레드)

owner_id	Long 각 사용자에 관한 아이디
thread_id	Long 메시지 교환이 이뤄지는 쓰레드
participants_ids	json	각 대화에 참가자 명단
ismuted	bool	대화방 알람을 온오프 기능
nickname	String	대화방 이름
create_at	timestamp	2019-07-15 12:00:00
update_at	timestamp	2019-07-15 12:00:00

```

```
// 유저의 대화방 리스트 조회
$threadId_list = select thread_id from thread_table
where owner_id == A

// 본체 대화방 별 조회하기
select * from message_table 
where thread_id = A
order by create_at desc

// 대화방에 참여하고 있는 모든 유저 조회
$participantsId_list = select participants_ids from thread_table
where thread_id in $threadId_list
order by update_at desc
```

## 제작 중 이슈!

### 1. 사용자 목록을 JSON으로 저장하기

말 그대로 사용자목록을 DB에 저장하려고 했습니다. 그렇기 때문에 유저리스트를 적용했습니다.

![](https://images.velog.io/images/42seouler/post/79ab8062-e661-4bfe-bf7f-391bd7657b8f/image.png)

map를 사용해서 저장하는 것은 부모 엔티티를 참조하는 외래키를 저장하는 조인 테이블이 항상 필요합니다.
제가 처음에 의도했던 json필드를 엔티티가 포함하는 것이기 때문에 다른 방법을 찾아보기로 했습니다.

두번째 방법은 List-User를 통해 json 배열을 저장하는 방법입니다. 이것으로 해결 할 수 있으리라고 생각했는데 H2에서는 필드가 생성되지 않았습니다. 분명 최신버전에선 지원 한다고 적혀있는데 제가 이해도가 부족했나봅니다. 그래서 MySql로 확인 해보니 제대로 작동하였습니다.








  







