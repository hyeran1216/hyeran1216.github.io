---
layout: post
title:  "검색 hook"
date:   2025-11-12 23:00 +09:00
categories: FE
---

**한줌 : 감정과 이야기를 꽃다발로 디자인하고, 공유하고, 기록하는 서비스🌸**

Github: [ganadiiiii/client](https://github.com/ganadiiiii/client)

2025.06 - 2025.10

---
### 검색
```
    [검색 입력창]
        ↓ (setSearchTerm)
    [searchTerm 상태 변경]
        ↓
    [useEffect로 검색어 입력에 따라 Search api 호출]
        ↓ (friendAPI.search(term))
    [searchResults 업데이트]
        ↓ (검색 결과 상태 저장)
    ┌────────────────────┐
    │ filteredFriends    │ ← 친구 목록에서 검색어와 매칭된 사람
    │ filteredSearchRes. │ ← 검색 결과 중 친구 아닌 사람
    └────────────────────┘
        ↓
    [컴포넌트 렌더링]
```
1. useFriendsManager 훅을 만들어서 내부에서 api 통신, 상태 업데이트, 요청 처리, 검색 필터링을 관리

    -> ui 컴포넌트는 뷰만 담당하기 때문에 ui 변경 시 로직엔 손 안대도 되고 유지보수가 편하다

2. 검색어 기반 필터링을 이중화

    1차: 서버에서 search api 호출 -> 전체 사용자에서 필터링
    
    2차: filteredFriends는 현재 친구 중 검색어로 로컬 필터링

    이렇게 하면 친구 외 사용자가 서버 필터링 되는 사이에 친구 먼저 로컬 필터링으로 빠르게 떠서 거의 실시간처럼 보인다!