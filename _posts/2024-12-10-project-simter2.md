---
layout: post
title:  "Swiper 사용해서 슬라이더 만들기"
date:   2024-12-10 23:00 +09:00
categories: FE
---

**SIMTER : 고민을 부담없이 털어놓는 나만의 공간 🏡**

Github: [git1uv/client](https://github.com/git1uv/client)

2024.05 - 2024.10

---
### Swiper 사용해서 슬라이더 만들기
슬라이더를 구현해야 했는데 간편하게 구현하고 싶어서 라이브러리들을 찾아보았다.

react-slick, slick-carousel, react-bootstrap, swiper.js 등등 되게 많았지만 달력과 마찬가지로 디자이너분들의 디자인대로 해야했기에 swiper.js를 쓰기로 결정했다. (react-slick, slick-carousel은 커스텀이 어렵고 react-bootstrap은 디자인이 너무 단조로움)

```javascript
// Import Swiper React components
import { Swiper, SwiperSlide } from 'swiper/react';

// Import Swiper styles
import 'swiper/css';

export default () => {
  return (
    <Swiper
      spaceBetween={50}
      slidesPerView={3}
      onSlideChange={() => console.log('slide change')}
      onSwiper={(swiper) => console.log(swiper)}
    >
      <SwiperSlide>Slide 1</SwiperSlide>
      <SwiperSlide>Slide 2</SwiperSlide>
      <SwiperSlide>Slide 3</SwiperSlide>
      <SwiperSlide>Slide 4</SwiperSlide>
      ...
    </Swiper>
  );
};
```
기본형은 이렇다. Swiper 안에 SwiperSlide로 슬라이드를 구성하면 된다.

Swiper 안에 옵션을 구성할 수 있다.
- direction : 슬라이드의 방향
- speed : 슬라이드의 속도
- loop : 무한 루프 설정 여부(default-true, false)
- autoplay : 자동 재생 여부(default-false, true)
- spaceBetween : 슬라이드 간의 간격
- slidesPerView : 한 번에 보이는 슬라이드 수
- onSlideChange : 슬라이딩되었을 때 실행될 함수

**구현**
[CustomSlider.jsx](https://github.com/git1uv/client/blob/main/src/components/Calendar/CustomSlider.jsx)
- `freeMode: true` : 터치(드래그)하여 슬라이드를 넘길 때, 특정한 위치에서 멈추지 않고 자유롭게 움직일 수 있도록
- ``pagination={{ clickable: true }}`` : 하단에 어느 슬라이드에 있는지 점 표시, 클릭해서 넘어가기도 가능
- 슬라이드의 배경을 챗봇 유형에 따라 동적으로 설정

---
슬라이드를 구현해야 할 때는 어떻게 해야하지? 로 막막했는데 찾아보니 라이브러리도 너무 쓰기 편하고 옵션이나 css 모듈도 많아서 걱정했던게 무색하게 쉽게 끝낼 수 있었다. 같이 고민해준 동료에게 감사하다.