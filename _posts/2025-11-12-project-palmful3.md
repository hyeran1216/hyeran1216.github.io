---
layout: post
title:  "framer-motion"
date:   2025-11-12 23:00 +09:00
categories: FE
---

**한줌 : 감정과 이야기를 꽃다발로 디자인하고, 공유하고, 기록하는 서비스🌸**

Github: [ganadiiiii/client](https://github.com/ganadiiiii/client)

2025.06 - 2025.10

---
이번 프로젝트에서 가장 인상깊은 부분은 바로 애니메이션... 확실히 페이지가 그림처럼 딱 박혀 있어서 클릭만 가능한 느낌이 아니라 실제로 이 서비스를 쓰고 있구나 느낌이 난다. 인터랙션이 이렇게 중요하구나를 체감했다. 난 framer-motion에서도 spring 정도만 쓴 게 다인데 동료가 다양한 방식으로 활용해서 공부할 겸 분석해봤다.

### spring
```typescript
const springConfig: SpringOptions = {
	stiffness: 150,
	damping: 20,
};
```
stiffness: 얼마나 빠르게 목표 값으로 수렴할지 (높을수록 빠름)
damping: 얼마나 흔들림 없이 정착할지 (높을수록 잔진동 없음)

---

### 간단한 랜덤 로테이션
```typescript
const handleMouseEnter = () => {
	const rotationList = [-2, -1, 1, 2];
	const randomIndex = Math.floor(Math.random() * 4);
	const randomRotation = rotationList[randomIndex];
		setRotation(randomRotation);
	};

return (
	<motion.div
		className={}
		style={}
		onClick={onClick}
		onMouseEnter={handleMouseEnter}
		whileHover={{
			rotate: rotation,
			scale: 1.05,
			boxShadow: "0.2em 0.2em 0.4em rgba(0, 0, 0, 0.15)",
		}}
		transition={{
			rotate: { type: "spring", bounce: 0.6 },
			scale: { type: "tween", duration: 0.1 },
		}}
	>
    ...
)
```
호버 시에 랜덤 방향으로 기울어진다.

---

### 마우스 호버 틸트 효과
```typescript
const rotateX = useSpring(0, springConfig);
const rotateY = useSpring(0, springConfig);
const scale = useSpring(1, springConfig);

const handleMouseMove = useCallback((e: React.MouseEvent<HTMLDivElement>) => {
	if (!ref.current) return;
	const rect = ref.current.getBoundingClientRect();
	const offsetX = e.clientX - rect.left - rect.width / 2;
	const offsetY = e.clientY - rect.top - rect.height / 2;
	const rotationX = (offsetY / (rect.height / 2)) * -ROTATE_AMPLITUDE; //ROTATE_AMPLITUDE = 8
	const rotationY = (offsetX / (rect.width / 2)) * ROTATE_AMPLITUDE;
	rotateX.set(rotationX);
	rotateY.set(rotationY);
	}, []);

const handleMouseLeave = useCallback(() => {
	rotateX.set(0);
	rotateY.set(0);
}, [rotateX, rotateY]);

return (
	<motion.div
		className=""
		style={{ rotateX, rotateY, scale }}
		onMouseMove={handleMouseMove}
		onMouseLeave={handleMouseLeave}
	>
    ...
)
```
- handleMouseMove: 카드 중심 대비 마우스 오프셋 비율을 회전 각도로 변환
- handleMouseLeave: 마우스가 카드 영역을 벗어나면 회전 각도 초기화

카드를 잡아 땡기는? 누르는? 느낌이 된다.

---

### 플립
```typescript
const [isFlipped, setIsFlipped] = useState(false);
const canFlip = Boolean(flowerCard.message && flowerCard.receiver);

const handleFlip = useCallback(() => {
	if (!canFlip) return;
	setIsFlipped((v) => !v);
}, [canFlip]);

return (
	<motion.div
		className=""
		style={{
			transformStyle:
				"preserve-3d" as React.CSSProperties["transformStyle"],
		}}
		animate={{ rotateY: isFlipped ? 180 : 0 }}
		transition={{ duration: 0.6, ease: [0.22, 1, 0.36, 1] }}
	>
		{/* Front */}
		<div
			className={`${canFlip ? "cursor-pointer" : "cursor-default"}`}
			style={{
				backfaceVisibility:
					"hidden" as React.CSSProperties["backfaceVisibility"],
			}}
			onClick={handleFlip}
		>
			<Front />
		</div>

		{/* Back */}
		<div
			className={`${canFlip ? "cursor-pointer" : "cursor-default"}`}
			style={{
				backfaceVisibility:
					"hidden" as React.CSSProperties["backfaceVisibility"],
				transform: "rotateY(180deg)",
			}}
			onClick={handleFlip}
		>
            <Back />
        </div>
    ...
)
```
- transform-style: preserve-3d로 입체 회전
- 뒷면은 transform: "rotateY(180deg)"로 미리 180도 돌아간 상태로 렌더링해놔야 전체 카드가 180도 회전했을 때 바르게 정면을 보게 됨
- backface-visibility: hidden으로 회전 시 뒷면(정면이 아닌 면)은 안 보이도록

클릭에 따라 플립하는 애니메이션 완성

---

### 날아오기
```typescript
<motion.div
	initial={{
		scale: 0.3,
		rotateY: 0,
		rotateX: 40,
		x: "100vw",
		y: "100vh",
	}}
	animate={{
		scale: 1,
		rotateY: 720,
		rotateX: 0,
		x: 0,
		y: 0,
		transition: {
			duration: 1.5,
			ease: [0.33, 1, 0.68, 1],
		},
	}}
	exit={{
		scale: 0.1,
		rotateY: 360,
		rotateX: 25,
		x: "-15vw",
		y: "-10vh",
		opacity: 0.05,
		transition: {
			duration: 1,
			ease: [0.33, 1, 0.68, 1],
		},
	}}
>
	<AnimatedFlowerCard ... />
</motion.div>
```
- 처음에는 화면 바깥 (100vw, 100vh)에서 시작
- 중심으로 날아오며 크기가 커지고(scale: 1), 720도 rotateY 회전하며 착지
- 종료 시에는 작아지며 특정 위치로 빠지는 exit 애니메이션 실행

