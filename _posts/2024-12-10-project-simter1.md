---
layout: post
title:  "project 회고록 - simter1"
date:   2024-12-10 23:00 +09:00
categories: FE
---

**SIMTER : 고민을 부담없이 털어놓는 나만의 공간 🏡**

Github: [git1uv/client](https://github.com/git1uv/client)

2024.05 - 2024.10

---
### react-calendar 사용해서 달력 만들기

달력에서 날짜를 선택하고 오늘의 감정, 일기, todolist 등을 기록하는 기능을 구현하는중, 달력에서 추가되어야 할 기능은 없어서 직접 구현하기보단 라이브러리를 쓰기로 결정했다. 
    
달력 라이브러리는 많지만 우린 디자인이 있으므로 
    
1. 커스텀이 가능할 것 
2. 참고할 글이 많을 것(내가 경험이 부족하기 때문)
    
를 중점으로 react-calendar 라이브러리를 골랐다. 블로그도 많고 커스텀에 대한 글이 많았다는게 좋았다.
    
이제 디자인대로 달력을 커스텀할 차례, 근데 기본 css 파일을 들어가보니 코드가 너무 많고 비슷했다. now, active, hasActive 어느게 어떤 건지... 

상위클래스를 파악 못하고 코딩해서 day 뷰에서 적용한 스타일이 month 뷰에 영향을 주기도 하고, 이래저래 삽질도 좀 했다.
- 달력 큰 버전: [CustomCalendar.style.js](https://github.com/git1uv/client/blob/main/src/components/Calendar/CustomCalendar.style.js)
- 달력 작은 버전: [MiniCalendar.style.js](https://github.com/git1uv/client/blob/main/src/components/Calendar/MiniCalendar.style.js)

디자인은 이렇다.

<img width="50%" alt="Image" src="https://github.com/user-attachments/assets/9d23b65f-f920-4c6d-b034-6af6ce17ffa3" />

- 내비게이션 버튼은 애셋으로 대체
- 폰트 변경 및 주말/평일 디자인 똑같이
- 일요일부터 시작
- 상담 내역 연동해서 오늘 내가 대화한 챗봇 종류에 따라 일 tile 색이 바뀐다.(빨강, 노랑, 초록)
- 저장한 감정에 따라 이모지가 뜬다.

<img width="50%" alt="Image" src="https://github.com/user-attachments/assets/9c5271e1-ae5e-46ae-84ad-ccd81992b45f" />

- 보고있는 현재 페이지 날짜는 노란색 배경
- 오늘 날짜는 회색 보더
- 상담 내역이 있으면 노란색 점으로 표시

---
라이브러리처럼 남의 걸 갖다 쓸 때는 제대로 파악하고 시작해야 나중에 두번 일 안 하겠구나 하는 생각도 들었고 코드를 구현하는 능력보다 읽고 잘 이해하는 능력이 더 중요하겠다는 생각도 들었다.

<br />
이후에는 공식 문서를 보며 필요 없는 기능들은 지우고, 클릭하면 navigate하는 기능이라던가 해당 날짜의 사용자 정보를 불러오는 api라던가 등등을 추가하는 과정을 거쳤다. 라이브러리를 써본 경험이 처음이였는데 엄청 복잡하지 않고 달력이 부수적인 기능이라면 충분히 쓸 법하다고 생각한다. 

<br />

참고링크는 다음과 같다
- [참고링크1](https://www.npmjs.com/package/react-calendar)
- [참고링크2](https://velog.io/@pikadev1771/react-calendar-%EC%BB%A4%EC%8A%A4%ED%85%80%ED%95%98%EA%B8%B0-%EB%82%A0%EC%A7%9C-%EB%B3%80%ED%99%98-%ED%98%84%EC%9E%AC-%EB%8B%AC-%EA%B5%AC%ED%95%98%EA%B8%B0-%EC%BD%98%ED%85%90%EC%B8%A0-%EC%B6%94%EA%B0%80%ED%95%98%EA%B8%B0)
- [참고링크3](https://velog.io/@hyerani/React-%EC%BA%98%EB%A6%B0%EB%8D%94-%EB%A7%8C%EB%93%A4%EA%B8%B0-react-calendar)
- [참고링크4](https://ojspp41.tistory.com/42)