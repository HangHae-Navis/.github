# Navis (나비스)

> 그룹 관리 플랫폼

### 🖥️ [Navis 바로가기](http://navis.kro.kr/)

![image](https://user-images.githubusercontent.com/97386129/233097985-e75a16e0-28af-4e38-ad54-1f9baba88690.png)


[⚓ 프론트엔드 깃허브](https://github.com/HangHae-Navis/navis-fe)

[⚓ 백엔드 깃허브](https://github.com/HangHae-Navis/navis-be)

---

## 🛠️ 서비스 아키텍처


![image](https://user-images.githubusercontent.com/97386129/233099155-9db875e6-fd23-48a1-982b-3a83ab2a6896.png)

---

## 📋 API

https://sparta-kdh.kro.kr/swagger-ui/index.html](https://sparta-kdh.kro.kr/swagger-ui/index.html


---

## 📔 ERD

![image](https://user-images.githubusercontent.com/97386129/233099947-6df57615-e2f5-4fae-977c-5065eddfe3e2.png)


---

## 🔧 **기술적 의사결정**

| 사용 기술 | 기술 설명 |
| --- | --- |
| React-Query | Redux Thunk에 대한 사용 경험이 좋지 않았으며 프론트의 인원이 적어 State와 비동기 로직에 대한 코를 줄이면서 관리가 용이하여 선택하였다. |
| github actions/ngninx | 프론트 엔드와 백 엔드 간의 빠른 연계와 버전 관리를 위해 자동 배포화를 도입하였다.
백엔드 : 무중단 배포를 위해 nginx를 사용하였다. |
| Recoil | 상태 관리에 있어 보일러 플레이트를 크게 줄여주는 Recoil 채택 |
| SMTP | 사용자들의 중복 회원 가입 더 효과적으로 막기 위해 이메일 인증을 도입하였다. |
| OAuth2 | 사용자들에게 더 쉽고 간편한 로그인/회원가입을 구현 하기 위해 사용하였다. |
| redis | 회원가입시 이메일 인증, APILimiter의 기능에 db가 필요했고 둘다 휘발적인 내용이기 때문에 빠르고 간단한 redis를 사용하였다.  |
| WebSocket&Stomp | 실시간 채팅을 구현하기 위해 WebSocket이 사용되었고 간단하게 메시지 송/수신이 가능한 Stomp를 이용하였다. |
| SSE | 사용자들에게 실시간 알림을 보내주기 위해 SSE가 도입되었다. |
| Swagger | 백엔드가 구현한 API들을 프론트 분들이 간단하게 테스트 해볼 수 있고 직관적으로 값이 뭐가 들어가고 리스폰 되는지 알 수 있기 때문에 사용하였다.   |

---

## 📌 **트러블슈팅**

### ✔️ [FE] 소셜로그인 에러

리다이렉트 URL 오류

> 카카오톡 외부 API를 사용하는 과정에서 리다이렉트 경로 설정에 오류가 있었다. 프론트엔드 쪽 경로와 백엔드 쪽의 경로에 에러가 있었는데 이것을 빠르게 캐치하지 못했다.
> 

해결

> 카카오 디벨로퍼의 공식 문서를 참고하여 올바른 리다이렉트 주소와 REST API KEY를 공유받아 해결할 수 있었음
> 

권한 설정의 딜레마

> 나비스의 계정은 이메일만 가능하도록 설정하였는데, 소셜 로그인으로 가입하면 권한 동의에서 계정명(이메일)을 제공하는 항목이 필수가 아니라 선택이었다. 이로 인해 가입자가 이메일 제공에 동의하지 않고 가입하면, 계정명은 없는데 소셜 로그인이 가능한 기묘한 상황이 발생하였다.
> 

1차 해결

> 기술 멘토링에서 이 문제에 대해 상담한 뒤, 이메일 제공을 받지 못한 계정은 임시 난수 ID를 생성하도록 하고(어차피 소셜 로그인으로 들어가기 때문에 복잡한 계정명을 받아도 유저가 신경쓸 부분은 없었다고 판단) 계정명이 필요한 작업이 생길 경우 프로필 페이지에서 본래 계정명을 바꾸도록 유도하는 예외 처리를 하였다.
> 

2차 해결

> 카카오 디벨로퍼에서 공식 문서를 살펴보던 중 비즈앱 동의를 하면 계정명 제공 동의를 필수 항목으로 추가할 수 있다는 정보를 입수한 뒤 이를 실행하였다. 이 과정을 거친 덕에 더 이상 계정명의 부재에 대한 예외 처리를 할 필요가 없어졌다.
> 

---

### ✔️ [FE] 리액트 쿼리 - invalidate

페이지를 넘어가도 살아있는 쿼리

> A 페이지에서 다른 페이지로 이동한 다음 다시 A페이지로 돌아올 때, 기존에 있던 쿼리가 갱신되지않고 남아있어 useQuery로 데이터를 받아오지 않고 에러를 일으키는 문제를 발견함.
> 

---

1차 문제 해결 - refetch, 새로고침

> 각 페이지에 접속할 때 문제가 될 수 있는 쿼리의 키값에 접근해 그것을 refetch하거나, 아예 window.location.reload()로 쿼리를 초기화 시키는 방법을 시도했었다.
> 

---

2차 문제 해결 - 쿼리 삭제

> 그러해도 해결이 되지 않고, invalidate 관련 이슈를 찾다가, 아예 해당 페이지를 거쳐야 하는 부분에서 해당 쿼리키를 찾아 삭제하는 방법을 시도했다. 이렇게 함으로써, 모든 쿼리를 재갱신하지 않고 문제가 될 쿼리만 삭제하고 올바른 순간에 발동할 수 있도록 하였다.
> 

---

### ✔️ [FE] 데이터 전송 - 방식의 차이

소통 오류

> formData를 이용하여 과제 제출의 정보를 보내는데 있어 벡 프론트와의 의견차이가 있었다. 프론트 엔드 입장에서는 복수의 파일을  formData에 넣어 POST를 보내는데, 벡 엔드에서는 각각의 파일에 대응하는 formData를 만들어야 하는 것 아니냐는 의견 차이가 있었다.
> 

---

해결

> 좀 더 심도깊은 대화와 비교를 거친 끝에, 이러한 의견 차이는 벡 프론트 간의 작업 방식에서의 차이에서 기인했음을 알 수 있었다. 벡 엔드 부팀장이 직접 프론트 엔드의 깃허브 레포지토리에서 코드를 받아 로컬 환경에서 실행하며 분석한 결과, 벡 엔드에서 프론트 엔드에서 보내는 방식에 맞춰 작업하는 것으로 마무리했다.
> 

---

---

### ✔️ [FE] SSE 연결 지속 오류

컴포넌트가 언마운트되도 연결이 지속되던 오류

> SSE 연결이 되어 있을 경우에 컴포넌트 언마운트가 되었을 때도 연결이 일정 시간 동안 지속 후 콘솔에서 오류가 무한히 출력되고, 브라우저를 끄기 전까지전체적으로 프로젝트의 속도가 느려지는 오류
> 

---

해결

> useEffect cleanup을 통해서 연결이 되어 있을 경우 연결을 close를 시켜서 성공적으로 끊음.
> 

---

### ✔️ [BE] SSE알림 추가 후 커넥션 문제

---

서버가 멈추는 오류

> SSE를 구현하면서 커넥션 누수가 발생해 서버 배포 후 이용하다 보면 멈추는 현상 발생하였음
> 

1차 해결

> hikari 설정의 문제인가 싶어 properties에 hikari의 현재 상태 로그를 띄울 수 있는 것을 추가하고 확인
SSE가 끊겨도 계속 커넥션이 남아있는 것을 확인하였음

그래서 스케줄러를 이용해서 강제로 30초마다 SSE를 초기화 시키면서 다시 연결 시켰음
 
커넥션 누수는 고쳐졌지만 한 사람당 1개의 커넥션을 가지고 있기 때문에
hikari pool의 기본 갯수인 최대 10명의 사용자만 이용 가능 했음
> 

2차 해결

> 어째서 hikari pool을 끊지 않고 계속 사용하는지 검색을 해봤음

jpa 사용시 기본적으로 OSIV 옵션이 true이기 때문에 일어나는 현상

OSIV란? OSIV는 영속성 컨텍스트를 뷰까지 열어두는 기능
따라서 엔티티도 계속 영속 상태로 관리된다.
SSE의 엔티티가 영속 상태로 계속 남아있어 커넥션이 닫히지 않고 남아있었음
그래서 application.properties에서 spring.jpa.open-in-view=false 설정을 하니 문제가 해결 됨
> 

---

### ✔️ [BE] **nginx 무중단배포**

프록시 오류

> nginx 무중단 배포 적용 후 리버스 프록시가 제대로 안되었던 현상
> 

1차 해결

> 에러 로그를 보고 검색하니 nginx에서 dns서버 설정을 안 해 url 해석을 못한다고 나왔다. 
그래서 찾아보고 nginx 설정에 구글dns인 resolver 8.8.8.8; 추가했지만 안 됐음
> 

2차 해결

> 더 찾아본 결과 resolver에 ec2 프라이빗 IPv4 주소의 dns주소 값을 넣었더니 문제 해결
EC2-VPC 의 DNS 서버는 172.31.0.2 이다.
> 

---

---

### ✔️ [BE] **EC2 메모리 부족**

서버의 램 부족 인한 멈춤 현상 발생

> ngnix 무중단 배포를 추가한 후 서버를 상시 2개를 키고 있어 프리 티어를 사용하고 있는 프로젝트 서버의 매우 적은 1GB 램을 100% 다 사용하고 있어서 서버가 매우 느려지는 현상이 발생
> 

해결

> 혹시 리눅스에도 윈도우처럼 가상 메모리 기능이 있나 찾아봤는데 
스왑 메모리를 알게 되었고 4GB를 할당 하니 매우 잘 되었음
> 

---

---

### ✔️ [BE] 버튼 연타 시 명령이 중복 수행되는 문제

연속된 명령 문제

> 사용자가 버튼을 여러번을 빠르게 눌러 api를 연속적으로 호출할 시 데이터가 중복으로 들어가는 문제가 발생
> 

해결

> Spring AOP, Redis를 이용하여 API 요청 수를 제한하는 기능을 어노테이션으로 만들어
 post기능에 적용 시킨 후 **아이피, 요청URL**을 Redis에 저장시킨 후 초당 1번의 요청이 넘어가게되면 에러를 띄워주게 했음
> 

### ✔️ [BE]  N+1 문제

문제 발생

> 그룹 목록을 불러오기 위해 작성한 서비스에서 쿼리문이 의도한 것보다 훨씬 많이 발생하는 N+1 문제 발생
> 

문제 파악

> 서비스 코드에 행위별로 로그를 남겨 쿼리가 발생하는 위치를 파악
리포지토리를 호출하지 않는 곳에서 연관관계가 있는 엔티티의 하위항목을 참조하는 과정에서 쿼리가 발생하는 것을 확인
> 

해결

> 기존의 JPA 쿼리 메서드에서 Querydsl 쿼리 작성을 통해 참조과정 없이 한 번에 필요한 모든 정보를 조회하도록 변경
> 

---

# ⚓주요기능

# **그룹 생성 및 가입**

- 쉽고 빠르게 그룹을 생성, 가입할 수 있습니다
- 자동으로 생성되는 초대 코드를 공유하여 원하는 사용자만 접근할 수 있게 합니다.

![image](https://user-images.githubusercontent.com/97386129/233100295-e652aa74-7d71-4a70-9810-1d47ec759083.png)


---

# 마크다운 게시글 작성

- 마크다운을 활용하여 글을 자신이 원하는대로 자유롭고 심도있게 작성할 수 있습니다.
- 거의 모든 종류의 게시글에 적용되어 있어 능숙한 만큼 완성도 높은 게시글을 만들 수 있습니다.
    
    ![image](https://user-images.githubusercontent.com/97386129/233100362-c93b667c-b1df-4b3d-8dea-d3897f1078d7.png)
    
    ![image](https://user-images.githubusercontent.com/97386129/233100408-d0524389-28da-4e1a-81e4-28afc8993b41.png)

    ![image](https://user-images.githubusercontent.com/97386129/233101124-5ffd4891-0f5e-4432-8ae5-36e14127bbcc.png)

    

---

# 과제, 제출과 피드백

- 어드민, 서포터는 해당 그룹의 모든 참가자의 과제 제출 현황을 파악할 수 있습니다.
- 어드민, 서포터는 제출자의 과제물을 확인한 뒤, 쉽게 합격 / 반려 여부를 정하고 피드백을 남길 수 있습니다.
- 24시간 남은 과제는 그룹 페이지의 상단에 노출되어 빠른 접근이 가능합니다.
- 과제가 올라오면 해당 그룹에 가입된 유저에게 알림이 뜨도록 되어 있어서 빠르게 확인할 수 있습니다.

![image](https://user-images.githubusercontent.com/97386129/233101497-f0777ba8-b95e-49d9-ba32-cc4a2163835c.png)


![image](https://user-images.githubusercontent.com/97386129/233101536-70558101-6b93-40d4-b4fa-47d209616ec4.png)


![image](https://user-images.githubusercontent.com/97386129/233101562-9dfbf189-a3d7-43bd-a7f6-9da1d79caa82.png)


![image](https://user-images.githubusercontent.com/97386129/233101679-df15ad85-15a4-49d7-a28a-d6b843b0023d.png)


---

# 간편하고 빠른 투표

- 그룹 내 모든 사용자가 이용할 수 있는 투표입니다.
- 모든 투표는 익명 투표이며, 언제든 투표 현황을 빠르게 확인할 수 있습니다.
    
    ![image](https://user-images.githubusercontent.com/97386129/233101721-01587eea-a75b-487d-95c7-36473b5b41ef.png)

    
    ![image](https://user-images.githubusercontent.com/97386129/233101755-a2d326d8-bd67-4dbe-95cb-6a6525dc71be.png)

    

---

# 설문조사와 응답 확인

- 어드민과 서포터는 자유롭게 설문 게시글을 작성할 수 있습니다.
- 전체통계에서는 각 항목에 대한 응답과 선택형, 체크박스의 통계를 확인 할 수 있습니다.
- 개별응답 보기에서는 선택한 유저의 응답만 볼 수 있습니다.

![image](https://user-images.githubusercontent.com/97386129/233102017-5b73bb1d-d025-4728-bb49-1754f51e4f90.png)


![image](https://user-images.githubusercontent.com/97386129/233102033-88095bf3-4f87-4534-8709-20573c9188f1.png)


![image](https://user-images.githubusercontent.com/97386129/233102079-c7751c1c-8b5e-4e61-9146-a53a7a0b4e69.png)


---

# 채팅

- 상대방의 이메일(계정명)을 안다면, 1대1 채팅이 가능합니다.
    
    ![image](https://user-images.githubusercontent.com/97386129/233102126-500a1eec-7155-41ba-9c08-53c3736d0e11.png)

    

---

# 알림

- 가입되어 있는 그룹에 공지사항과 과제 게시글이 등록되면 유저에게 알림이 떠 빠르게 확인할 수 있습니다.
    
  ![image](https://user-images.githubusercontent.com/97386129/233102206-fdf97a72-d6e2-497a-8029-61e7407dd546.png)


    

---

## 👥 팀원소개

| 역할 | 이름 | 분담 |
| --- | ---- | --- |
| FE 👑 | 김재우 | 메인, 그룹, 그룹 게시글 페이지(게시글, 과제, 설문, 투표), 어드민&프로필 페이지 제작 / 메인 페이지 그룹 개설 및 가입 모달 로직 구현 / 어드민&프로필 페이지 CRUD / 그룹 게시글 페이지 과제, 투표, 설문 CURD, 그룹 게시글 페이지 댓글 기초 로직 구현 / 카카오톡 소셜 로그인 구현, 출시 후 버그 관리 |
| FE | 김태현 | 로그인 / 회원가입, 프로젝트의 전체적인 스타일링, 프론트 CI/CD, 프로젝트 설계, 마크다운 렌더링 | 에디터 기능, 게시글 작성 기능, 헤더 푸터 등 글로벌 레이아웃 작업, 채팅/알림 실시간 기능, 추후 QA 사항 수정 / 출시 후 유저 피드백 관리 |
| BE 👑 | 김동현 | 회원가입, 로그인, 채팅, 알림, 투표 CRD, 공지 CRUD, 백엔드 서버, 백엔드 CI/CD구축 |
| BE | 김동민 | 그룹 관련 CRUD / 그룹 관리기능 전반 / 24시간 이내 마감 과제 표시 / 최근 열람한 글 목록 |
| BE | 손채이 | 일반 게시글 CRUD / 과제 게시글 CRUD / 과제 제출, 과제 피드백 기능 / 설문 게시글 |
| Design | 함보라 | 디자인 담당 |

### 📱**팀원 개인 정보 (연락처)**

| 이름 | 연락처 | 깃허브 | 블로그 |
| --- | --- | --- | --- |
| 김재우 | 010-4160-9655 | https://github.com/Nidurolak | https://karoludin.tistory.com/ |
| 김태현 | 010-9688-6780 | https://github.com/tjdrkr2580 |  |
| 김동현 | 010-5647-6176 | https://github.com/kil6176 |  |
| 김동민 | 010-4550-7071 | https://github.com/mydmk7874 | https://velog.io/@mydmk |
| 손채이 | 010-9289-9165 | https://github.com/chaeyi0318 |  |

---
