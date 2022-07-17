http://3.39.30.117:3000/

# 내부 서버에서 실행 할 경우!
> 1. client/src/api/http.js 의 baseURL을 Api서버가 실행된 PC의 IP(ex:192.168.0.1)로 변경해야 합니다.
> 2. database는 외부 ec2 서버에 설치되어있습니다.

--------------------


# To Do List Project

--------------------

### 1. 명세 분석
>Trello의 디자인, 구성을 모방 할 예정이다.   
>0:46 부터 1:56 영상에서 나오는 주요 기능에 대한 명세는 아래와 같은 것으로 보인다.   
>  
>* 목록추가 가능한 To Do List 게시판 생성하기
> > 1) To Do List 형태의 게시판 모듈 작성
> > 2) List 내의 아이템 생성, 삭제, 수정
> > 3) 작업 요청 시에도 새로고침이 되지 않도록
> > 4) 새로운 To Do List의 추가, 삭제가 가능
> > 5) 마우스 드래그 앤 드롭 기능

> 위 내용을 토대로 To Do List를 개발하려고 한다.  
> 다만 여러 목록이 존재하며 사용자가 동적으로 추가/삭제가 가능하다  
> 
> 3)번 항목의 Single Page Application을 구현 및 여러 동적 화면을 구성하기 위해서  
> 프론트엔드 프레임워크(Angular, React, Vue)를 사용하는게 좋을 것으로 판단된다.   
> 개인적으로 가장 익숙한 Vue로 진행하려고 한다.
> 
> Api 서버 구현 언어는 Node.js로 진행, 서버는 구현하기 쉬운 Express로 진행한다.  
> 2개 테이블의 많지 않은 데이터베이스 저장을 위해서는 mysql이 가장 효율적으로 보인다.  
> 

#### 최종적으로 To Do List 개발을 위한 [언어스택]은 아래 형태로 진행하고자 한다.

> [Vue.js] (>=2.6.14)               
> [Node.js] 활성 LTS (>=16.13.0)  
> [Express] (>=4.17.1)            
> [MariaDB] (Version 없음)        

--------------------------------


> 이제 어떠한 플러그인이 사용되어야 할지 대해서 생각해보자.  
> Vue.js의 웹 요청처리는 [Axios] 로 진행한다. 가장 익숙하기도 하고, 원하는 바를 전부 구현 할 수 있다.  
> 브라우저 호환성을 위해 [Babel]을 사용한다. Vue Default로 설치된다.  
> Vue.js를 정적파일로 변환하여 사용하기 위해서 [Webpack]을 사용한다. 마찬가지로 Vue Default로 설치된다.  
> 단순기능 구현이기 때문에, 가장 많이 쓰이는 [Vuex]나 [vue-router]는 사용하지 않는다.  
> ICON 사용을 위해서 [FontAwesome] 플러그인을 사용 할 예정이다.  
> Vue.js에서 5) 번 항목의 드래그 앤 드롭 기능을 구현해본 바가 없어 [Vuedraggable] 를 추가하여 진행해보려고 한다.  
> Vue.js와 Node.js는 서로 다른 포트 실행되며 정책 상 문제가 발생 할 수 있을 것으로 보이니   
> Cors 설정을 진행하기 위해 [Cors] 플러그인이 필요할 것 으로 보인다.  

#### 최종적으로 추가 할 [플러그인]은 아래와 같다.

> [Axios] - Vue.js  
> [Babel] - Vue.js default  
> [Webpack] - Vue.js default  
> [FontAwesome] - Vue.js  
> [Vuedraggable] - Vue.js  
> [Cors] - Node.js  
  
--------------------------------


### 2. 설계 설명

Vue 파일 구조는 기존 Vue의 Default 형태를 사용하고 일부 필요한 부분만 추가 하였다.\n
가능하면 MVC 패턴을 직관적으로 보이게 하고 싶어 폴더 구조에서 표현하였다.

|1depth|2depth       |3depth     |내용                                        |
|------|-------------|---------- |--------------------------------------------|
|src/  |             |           |                                            |
|      | api/        |           | Node.js로 요청하기 위한 함수를 모아둔 폴더   |
|      | lib/        |           | 공통사용 모듈                               |
|      | assets/     |           | 자산관리                                    |
|      |             | js/       | 외부 라이브러리 js 보관                     |
|      |             | css/      | main.css 등 전체 css를 보관                 |
|      |             | img/      | logo등의 파일 보관                          |
|      | components/ |           | (default) Vue 컴퍼넌트 보관                 |
|      |             | template/ | Header, Footer 등의 공통사용 파일           |
|      | views/      |           | Views 폴더                                 |
|      |             | Home.js   | main Home vue                              |
|      | App.vue     |           | vue root 파일                              |

Node 파일 구조는 아래와 같다.

|경로|내용|
|-------|------|
|controllers/              | Controller 폴더|
|lib/                      | 공용모듈 및 Database 연동 모듈|
|models/                   | Model 폴더|
|route/                    | Api 요청에 대한 라우팅 파일 |
|index.js                  | node root 파일|

------------------------------------
### 3. 선택하지 않은 설계 대안

1. nodemon 추가
> 수정 할 때마다 서버 재시작을 해야하는 수고로움이 있어 nodemon 설치를 진행하였다.
2. mysql2 추가
> DB와 동기 처리를 위해 promise를 사용하려고 하니 Callback 함수로 인해
> 코드 가독성이 급격하게 내려가는 것으로 판단되어 async/await를 사용하였다.
> mysql의 경우 async/await를 제공하지 않아, mysql2로 재설치하였다.
3. Linked list 일부 구현
> 별다를 것없이 단순 배열에 데이터를 쌓을 생각으로 진행했으나 실제로 To Do List내의 아이템들이
> Update, Insert, Delete 되는 시점에서 문제가 발생하였다.
> 기존 방식대로 진행하자니 업데이트가 발생 할 때마다 모든 데이터 값을 수정해야했다.
> 규모가 적은 경우에는 문제가 되지 않지만, 어느 정도 데이터가 쌓일 수록 업데이트 시점에서 발생하는
> 과부하가 심해질 것으로 판단되었고, 가장 해당 프로젝트와 어울릴 것으로 판단되는 자료구조로
> 링크드 리스트를 사용하기로 했다.
> 오픈소스를 찾지 못해 일부 필요한 기능만 함수형태로 구현하여 작업을 진행하였다.

