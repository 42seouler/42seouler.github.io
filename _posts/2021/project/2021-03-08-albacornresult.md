---
title: 첫번째 프로젝트(albacorn) 회고

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

description: 처음이자 실패한 프로젝트 albacorn 후기

meta_keywords: Java, Spring

last_modified_at: '2021-03-08 14:00:00 +08000'

toc: true

toc_sticky: true

toc_label: 목차
popular: true
---
<br>

기간 : 2020년 12월 04일 ~ 1월 31일  
목표 : 지하철 근처의(1km)의 구인중인 가게 보여주기  
방법 : 알바몬, 알바천국에서 크롤링해서 데이터를 가공해서 저장하기  

**총 과정**

#### 저는 42Seoul에서 C, CPP 언어만 배웠습니다.

진로에 대한 고민과 평소에 관심을 갖던 웹서비스 개발에 대해 알고 싶었습니다.  
그래서 멘토님과의 대화를 신청하였고 제 아이디어 기반으로 서비스를 만들기를 권유 받았습니다.

![](https://images.velog.io/images/memorego/post/92d73b50-cdef-44e9-a7f3-ddee923d6a18/project.jpeg)

42Seoul과제만 했던 저는 웹에 대한 지식이 단 1도 없었습니다.  
다행히도 이호준 멘토님께서 무엇을 만들지 서비스를 어떤 덩어리로 분리 해야 할지  
어떤 개념인지 하나하나 설명 해주셨습니다.

#### 이호준 멘토님의 정리

#### 내가 처음으로 만들어야 할 것

데이터를 정의하고 CRUD를 만드는 것

#### Golang으로 백엔드 만들기로 잘못된 결정을 내리다.

<p align="center"><img src="https://images.velog.io/images/memorego/post/f085aceb-ae3f-4173-82fb-7364cd2b5f22/image.png"/></p>

제가 그나마 알고 있던 웹 개발이 가능한 언어가 Go라서 Go로 만들겠다고 했습니다.  
굳이 하겠다면 말리지 않겠지만 그럼 일단 해보라고 하셨습니다.  
그땐 당장 새로운 언어를 배우지 않고 무언가를 할 수 있어서 좋다고 생각 했었습니다.


## GO로 진행하면서 많은 문제점을 만나다!

> 이호준 멘토님 왈 "일단 해봐 그럼 알아"
이때 알아차리고 제대로 했어야 했는데.. 그땐 죽어도 몰랐다. 그냥 Go언어를 싫어하시는구나 했다.

<p align="center"><img src="https://images.velog.io/images/memorego/post/ffeb8e63-50b2-40c6-8d3e-e34ffe178e19/image.png"></p>

#### 1. 코드를 작성 하기 전 구조를 정하고 시작 하기로 했습니다.
- 왜냐하면 팀플로 규칙도 구조도 없어서 API서버를 만들었는데 서로 구조가 다르니 합치기도 어렵고
  남의 코드를 보기도 어려웠기 때문입니다.

[그래서 밥 아저씨의 클린 아키텍처 링크](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html)를 읽어보고 도입 하기로 했습니다.

<p align="center"><img src="https://images.velog.io/images/memorego/post/15d179fb-78af-4dcb-b1da-c3db4de18f20/image.png"/></p>

#### 2. 예제 코드들을 찾아보니 command, query를 나누던데? 이건 또 무엇인지?

- 굳이 나누지 않아도 문제는 없었습니다. 요즘 유행하는 마이크로서비스에 잘 맞는
  구조라고 정보를 획득하고 그래도 이러한 예제들이 많으니 알아두면 좋겠지라고 생각하고 해봤습니다.

-CQRS패턴을 위해 command/query 디렉토리를 나눴고, CQ를 포함하는 usecase.go를 만들었습니다.

<p align="center"><img src="https://images.velog.io/images/memorego/post/f3b5eb47-66fb-4362-914f-146d6c46513d/image.png"></p>

#### 3. web에 익숙하지 않지만 그것들을 스스로 처리해야 한다.
- 웹서비스에 관해 지식이 없는데 스스로 처리해야되서 그것들을 찾아보는데 시간이 오래 걸렸습니다.  
  물론 그렇다고 시간 대비 지식이 늘어난것도 아니었습니다.

#### 멘토님 피드백

<p align="center"><img src="https://images.velog.io/images/memorego/post/c5fe8aaa-6773-4538-87bd-f8be9d756648/image.png"/></p>

제가 겪었던 문제점은 자바 스프링을 통해 학습을 했다면 풍부한 레퍼런스와 작성 규칙을 통해 자연스럽게
체득이 가능 했을텐데 초보자가 너무 돌아 돌아 알게 되었단 것을 알려주셨습니다.  
위의 문제점은 고작 3가지 였지만 적은 검색결과와 블로그 글들로 알아야 했기 때문에 처음에 쉽사리 이해 할 수 없었습니다.

## 반성 후 자바 스프링을 배우기로 결심하다.

12월 04일부터 ~ 12월 17일까지 Go로 헤매다가 자바로 변경하기로 했다.  
완강한 강의는 12월 17일부터 ~ 1월 10일 무려 3주 내내 들었습니다.

![](https://images.velog.io/images/memorego/post/bde80bb4-edd5-4110-85da-ecb3e0b51535/image.png)

docs를 보고 시작하는게 좋을까 했는데 처음엔 docs로 공부하는게 어렵더라 그래서 김영한님의 인프런 강의패키지를 구매하고 강의를 들었다. Go로 하면서 대체 이게 뭐지? 왜 그런지 몰라서 답답 하던것들이 고민 했던 것들이 프레임워크에서 제공된다는 것을 강의를 통해 듣게되니 오호 너무 재밌잖아 하면서 강의들을
순식간에 완강해버리고 말았다 물론 순식간이라고 해봐야 한달동안 꼬박 프레임워크 강의만 들었지만 말이다!

#### 드디어 돌아오다!

[20년차 웹/앱 서비스 기획](https://m.blog.naver.com/durandot/221024396344)를 따라서 리부트하기로 하였다.

#### 1. 서비스 정의

- 지하철역 근처의 알바 찾기

![](https://images.velog.io/images/memorego/post/0a4a48d1-6875-49f7-96e1-afb3105a071c/image.png)

지하철 기준으로 가게 정보를 중복없이 제공한다. 가게를 선택하면 오전/오후/저녁/새벽으로 구분된 구인정보를 볼 수 있다. 구인정보를 선택하면 상세한 구인URL로 연결된다.


**# 2. 서비스 구조**

![](https://images.velog.io/images/memorego/post/8d139ea2-1df7-410d-b455-0de7ea2b8384/servicestructure.png)

처음으로 진행하는 프로젝트이기 때문에 내가 생각하는 MVP(mininum viable product)로 만들도록 한다. 지하철역, 가게정보를 바탕으로 필터링한 검색 결과를 제공하는 것이다.

#### 3. 사용자의 예상 사용 방식

![](https://images.velog.io/images/memorego/post/e27169f0-af1a-4ae2-9a51-fef3735641d6/flow.png)

아무것도 설정하지 않으면 전체리스트를 보여줍니다.



#### 4. 어떤 정보가 필요한지 마인드맵


![](https://images.velog.io/images/memorego/post/536d662e-f53b-45b4-9efb-d79029561948/mindmap.png)

지하철역 기반으로 가게에 관한 것을 제공 하려면 무엇이 필요할지 생각해보았다.  
생각보다 적은 양의 정보를 갖고 구현 할 수 있다는 것을 알 수 있다. 아니면 아직 내가 필요한데 생각하지 못한걸수도 있다.

## 프로젝트를 진행하면서 겪은 문제와 해결 방법

진행하면서 정말 많은 문제점을 만났고 나름의 생각과 해결방안은 별도의 링크를 첨부하겠습니다.  
아무래도 이 글에 적으면 너무나도 방대해지기 때문이죠.

![](https://images.velog.io/images/memorego/post/74a22680-01a6-44ce-8995-33fc101dfc5b/image.png)


#### 1. 스프링 프로젝트를 진행하면서 문제 정의와 나만의 해결 방안들

- [albacorn 개인 프로젝트 과정 01](https://42seouler.github.io/java/albacorn01/)
- [albacorn 개인 프로젝트 과정 02](https://42seouler.github.io/java/albacorn02/)

#### 2. TDD 개발 방법에 인지하고 맛보기

- [Spring 계층 단위로 테스트 하는 방법](https://42seouler.github.io/java/spring-test/) 계층 간 테스트는 어떻게 하는지 간단히 정리해보았습니다.
- [TDD테스트 방법을 나의 프로젝트에 적용](https://42seouler.github.io/java/tddstart/) 해보기

#### 3. Spring 테스트 과정 중 알 수 없는 오류를 만난 줄 알았을 때

- [lombok사용에 익숙하지 않아서](https://velog.io/@memorego/reboot02) 내장 톰캣 서버가 작동 중일 땐 PostMan에서 제대로 작동 되었으나 테스트 코드에선 객체가 생성되지 않아 알 수 없는 오류 인 줄 알고 토~일(1박2일)동안 삽질 했던 이야기.
- [테스트 사용이 익숙하지 않아서](https://velog.io/@memorego/reboot03) 서버가 작동 중일 땐 문제가 없었는데 테스트 코드 작성 할때 객체의 행동을 정의하고 호출했어야 했는데 호출 순서 때문에 작동되지 않아 존재하지 않는 오류와의 사투

# 결과

Go로 크롤링 하는 예제를 따라 만들어 알바천국, 알바몬을 크롤링 했지만!!  
결과는 IP차단을 당했습니다. IP를 차단 당한 다음에 알게 된 사실은 크롤링은 불법이라는 것!  
이것도 차단당하고 멘토링 하던 중 알게 된 사실!! 그래서 이 비지니스 모델은 폐기!!  

![](https://images.velog.io/images/memorego/post/73f99387-088c-4b9e-be9d-e10370942048/image.png)

실망한 나머지 이 프로젝트는 여기서 마무리하기로 했습니다.

## 그래서 코드를 모두 삭제하고 정리했습니다.

<p align="center"><img src="https://images.velog.io/images/memorego/post/8e96b5bc-0e06-407c-ba81-99c60e1b1dee/image.png"/></p>

과정은 남겨두었지만 코드를 남겼어야 했는데 코드를 모두 지워버렸습니다.  
나중에 알게 된 사실이지만 실패한 과정도 하나하나 모두 남겨두어야 한다고 멘토님께서 알려주셨습니다.  
이 깃허브는 제가 몸무림 쳤던 흔적들만 남겨두도록 하겠습니다!! ㅠ0ㅜ












