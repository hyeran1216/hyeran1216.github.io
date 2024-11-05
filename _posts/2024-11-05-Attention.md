---
layout: post
title:  "심화세션 - NLP.Attention"
date:   2024-11-05 20:00 +09:00
categories: KHUDA
---

# Lecture Recap

## Chapter 5

### 📌 GPT

**GPT** ? Generative Pre-trained Transformer의 약자     

**Generative :** 텍스트를 생성하는 봇

**Pre-trained :** 모델이 방대한 양의 데이터로부터 학습하는 과정을 거쳤음을 의미

**Transformer :**  머신러닝 모델인 신경망의 일종

---

### 📌 Transformer 구조

1. **입력 처리**
    - 입력 문장을 **토큰(token)** 단위로 분할함
        - 텍스트 : 각 단어
        - 이미지 : 이미지의 조각
        - 음성 : 사운드의 작은 덩어리


    - 각 토큰은 그 의미를 인코딩하기 위한 숫자 리스트(**벡터**)와 연결됨
        - 유사한 의미를 가진 단어는 공간에서 서로 가까운 벡터에 위치하는 경향이 있음


2. **어텐션 블록(Attention Block)**
    - 문맥에서 어떤 단어가 다른 단어의 의미와 관련이 있는지, 
    그리고 그 단어들이 문맥적으로 어떤 의미를 가지는지 파악함
    - 이러한 벡터는 퍼셉트론 등의 연산을 거침
    - 모두 동일한 연산을 병렬로 진행


다음에 올 단어를 예측하는 과정에서 퍼셉트론과 어텐션 블록 사이를 오가는 과정이 계속 반복됨

---

### 📌 The premise of Deep Learning

**딥러닝 (Deep Learning)** ? 머신러닝의 한 종류로, 데이터로부터 모델이 학습하여 패턴을 인식하고, 예측하는 능력을 갖추는 방식

ex. 이미지를 입력받아 이미지를 설명하는 레이블을 생성하는 함수

ex. 텍스트 구절이 주어지면 다음 단어를 예측하는 예제 또는 직관 및 패턴 인식


![alt text](https://github.com/hyeran1216/hyeran1216.github.io/blob/b0f7c0d79c1bdb8d906951fe9e06106dd58a3bf9/_posts/images/attention_1.png?raw=true)

- 지난 수십 년 동안 잘 확장된 것으로 입증된 모델 종류들을 설명함
    
    → 역전파 알고리즘 사용
    

먼저. 어떤 모델을 만들던 간에 입력 형식은 실수 배열로 지정해야 함
(실수 배열 ? 숫자 목록, 2차원 배열, 텐서)

이때, 배열 내의 데이터를 **가중치** 라고 함

모델의 각 계층은 입력 데이터를 변환하여 최종 출력에 도달할 때까지 실수 배열로 계산을 이어감

---

### 📌 Word Embedding

**Word Embedding** ? 텍스트 데이터를 벡터로 변환하는 과정

- 문장의 각 단어는 고유한 벡터로 변환되며, 이 벡터들은 고차원 공간에 위치하게 됨

**※ 단순히 개별 단어를 인코딩하는 것이 아닌, 더 풍부한 문맥적 의미를 담는 것을 목표로 함**


> **임베딩 과정**
> 
1. 먼저, 입력 문장에서 각 단어들로 나눈다. 
    
    ![alt text](https://github.com/hyeran1216/hyeran1216.github.io/blob/b0f7c0d79c1bdb8d906951fe9e06106dd58a3bf9/_posts/images/attention_2.png?raw=true)
    

2. 행렬에는 이러한 단어 각각에 대한 단일 열이 존재한다.
    
    ![alt text](https://github.com/hyeran1216/hyeran1216.github.io/blob/b0f7c0d79c1bdb8d906951fe9e06106dd58a3bf9/_posts/images/attention_3.png?raw=true)
    
    - 각 열은 첫 번째 단계에서 각 단어가 어떤 벡터로 변하는지를 결정하는 요소가 됨


> **ex. tower와 가까운 단어 임베딩**
> 

![alt text](https://github.com/hyeran1216/hyeran1216.github.io/blob/b0f7c0d79c1bdb8d906951fe9e06106dd58a3bf9/_posts/images/attention_4.png?raw=true)

> **ex. 단어 ‘man’과 ‘woman’의 차이, 그리고 ‘queen’과 ‘king’의 차이**
> 

![alt text](https://github.com/hyeran1216/hyeran1216.github.io/blob/b0f7c0d79c1bdb8d906951fe9e06106dd58a3bf9/_posts/images/attention_5.png?raw=true)

⇒ "king"과 "queen", "man"과 "woman" 사이의 벡터 차이는 유사한 방향을 가리킴

if queen이라는 단어를 모름 → king 을 가져와서 women-men 방향 추가하고 그 지점에 가장 가까운 지점에 임베딩하면 그 지점이 queen

**모델이 학습 과정에서 이 공간의 한 방향이 성별 정보를 인코딩하도록 임베딩을 선택하는 것이 유리하다는 것!!**

---

### 📌 UnEmbedding

**UnEmbedding** ? 벡터를 다시 텍스트 데이터(단어)로 변환하는 과정

> **단어 예측 과정**
> 
- 해당 컨텍스트의 맨 마지막 벡터를 어휘의 각 토큰에 대해 하나씩 매핑하는 또 다른 행렬 사용
- 확률 분포로 정규화하는 함수(**softmax**) 사용
- 학습 과정에서 최종 레이어 있는 각 벡터를 사용하여 그 직후에 올 것을 동시에 예측함

![alt text](https://github.com/hyeran1216/hyeran1216.github.io/blob/b0f7c0d79c1bdb8d906951fe9e06106dd58a3bf9/_posts/images/attention_6.png?raw=true)

- **UnEmbedding Matrix** : 어휘의 각 단어마다 하나의 행을 가짐
    - 각 행은 임베딩 차원과 동일한 수의 요소를 가짐
    (임베딩 행렬의 행과 열의 수와 반대)


---

### 📌 Softmax with temperature

> **problem**
> 

확률 분포의 합을 계산할 때, 모든 답을 더했을 때 1이 나와야 하나,
행렬 상에서 합을 계산하면 음수가 나오거나 1보다 큰 값이 나오기도 함

⇒ **소프트맥스(softmax)** : 임의의 숫자 목록을 가장 큰 값은 1에 가깝게, 가장 작은 값은 0에 가깝게 되는 방식으로 변환함

> **계산 방식**
> 

![alt text](https://github.com/hyeran1216/hyeran1216.github.io/blob/b0f7c0d79c1bdb8d906951fe9e06106dd58a3bf9/_posts/images/attention_7.png?raw=true)

$e$의 거듭제곱을 사용함

> **Temperature**
> 

![alt text](https://github.com/hyeran1216/hyeran1216.github.io/blob/b0f7c0d79c1bdb8d906951fe9e06106dd58a3bf9/_posts/images/attention_8.png?raw=true)

- T가 크면, 낮은 값에 더 많은 가중치를 부여하여 분포가 조금 더 균일해짐
T가 작으면, 큰 값에 더 많은 가중치를 부여하여 극단적인 분포 발생
- 가장 **높은 확률**을 가진 단어가 선택됨
- 현재는 너무 무의미한 단어 생성을 방지하기 위해 최대 2로 제한


## Chapter 6

### 📌 Attention mechanism

: 문장에서 각 단어의 문맥을 고려하여 어떤 단어들이 더 중요한지, 또는 어떤 단어들이 서로 관련이 있는지를 계산하는 역할을 한다.

> ex. American shrew **mole** (두더지)
One **mole** of carbon dioxide (몰)
Take a biopsy of the **mole** (점 사마귀)
> 
> 
> 단어 "mole"은 문맥에 따라 "두더지", "몰", "점" 등 다양한 의미로 해석될 수 있는데, Attention을 통해 문맥에 맞는 의미를 결정
> 

![alt text](https://github.com/hyeran1216/hyeran1216.github.io/blob/b0f7c0d79c1bdb8d906951fe9e06106dd58a3bf9/_posts/images/attention_9.png?raw=true)

임베딩을 통해 문맥을 파악하고 attention mechanism을 통해 해당 단어의 적절한 의미를 추론함

---

### 📌 Attention pattern

세 가지 주요 구성 요소로 동작

- **Query** : 해당 토큰이 다른 단어들과 어떤 관계가 있는지 질문을 던짐
- **Key** : Query에 대한 잠재적 답변
- **Value** : 최종적으로 주어진 관계를 통해 전달해야 할 정보의 내용
    - query가 어떤 key에 얼마나 일치하는지 계산된 결과를 통해 value값을 가져옴
    - 이 값이 최종적으로 문맥에 따라 업데이트


> ex.
> 
> 
> ![alt text](https://github.com/hyeran1216/hyeran1216.github.io/blob/b0f7c0d79c1bdb8d906951fe9e06106dd58a3bf9/_posts/images/attention_10.png?raw=true)
> 

목표 : 해당하는 단어가 해당 형용사에서 의미를 가져온 새로운 임베딩 집합 생성

1. 명사가 주변 형용사에게 형용사가 있냐는 질문을 던짐 → **Query**
2. 형용사는 나!라고 답함 → Key
3. Query 계산 - 각 단어(토큰)의 임베딩 벡터에 **Query 행렬**을 곱해서 **Query 벡터**를 만듦
4. 키 행렬 또한 각 단어의 임베딩에 **Key 행렬**을 곱해 **Key 벡터**를 만듦


    - 이때, 쿼리와 키가 서로 밀접하게 있을 때, 각 답변이 일치함
        
        ![alt text](https://github.com/hyeran1216/hyeran1216.github.io/blob/b0f7c0d79c1bdb8d906951fe9e06106dd58a3bf9/_posts/images/attention_11.png?raw=true)
        
    - key-query의 관련도를 계산하는 방법 : 각 key-query 쌍 사이의 도트 곱(내적) 계산
        
        ![alt text](https://github.com/hyeran1216/hyeran1216.github.io/blob/b0f7c0d79c1bdb8d906951fe9e06106dd58a3bf9/_posts/images/attention_12.png?raw=true)
        
        값이 클수록 일치함 **(하얀색 숫자 주목)**


5. 각 열의 가중치의 합을 **소프트맥스**를 사용하여 정규화함 

    → **Value** 벡터에서 정보를 추출하여 각 단어의 임베딩을 업데이트해 새로운 행렬 만듦 : **attention pattern**
    
    

$Attention(Q,K,V) = softmax(\frac{QK^T}{\sqrt{d_k}})V$

Q, K : 각 쿼리, 키를 임베딩에 곱하여 얻는 벡터

$d_k$ :  Key 벡터의 차원

1. $QK^T$ : key-query 쌍 사이의 도트 곱 계산값 → Query와 Key 벡터 간의 유사도
2. **소프트맥스**를 통해 각 단어 간 가중치를 정규화하여 확률 분포로 변환
3. 이 가중치 값을 **Value 벡터**에 곱해 각 단어의 정보를 반영하여 최종 임베딩을 업데이트

---

### 📌 Masking

언어모델에서 나중에 나오는 단어가 앞의 단어에 영향을 미치지 않도록 해야함

why ? 그렇게 해야 다음에 나올 단어에 대한 답을 알려줄 수 있음

이전 토큰에 영향을 미치는 이후 토큰을 나타내는 지점을 0으로 만들고 싶으나, 
그렇게 하면 합이 1이 되지 않아 정규화 불가

![alt text](https://github.com/hyeran1216/hyeran1216.github.io/blob/b0f7c0d79c1bdb8d906951fe9e06106dd58a3bf9/_posts/images/attention_13.png?raw=true)

→ 소프트맥스 적용 전, 해당 항목을 **음의 무한대**로 만듦. 이를 **masking** 이라고 함

---

### 📌 Value matrix

![alt text](https://github.com/hyeran1216/hyeran1216.github.io/blob/b0f7c0d79c1bdb8d906951fe9e06106dd58a3bf9/_posts/images/attention_14.png?raw=true)

다른 단어들의 연관성을 파악하여 정보를 전달함 - value matrix 사용

- value matrix를 첫 번째 단어의 임베딩에 곱함 → 두 번째 단어의 임베딩에 곱함
- value matrix의 각 열에 대해 해당하는 가중치를 곱함
- value matrix는 키-쿼리 관계가 얼마나 잘 맞는지에 따라 업데이트되며, 이 값을 기반으로 컨텍스트에 따라 임베딩을 업데이트

---

### 📌 다중 헤드 (Multi-head)

![alt text](https://github.com/hyeran1216/hyeran1216.github.io/blob/b0f7c0d79c1bdb8d906951fe9e06106dd58a3bf9/_posts/images/attention_15.png?raw=true)

앞에서 나오는 모든 계산들은 트랜스포머 내부에서 multi-headed attention을 통해 여러 개의 Attention 계산을 병렬적으로 실행하고 각각 고유한 **key, query, value** 사용함

→ 모델은 다양한 관점에서 단어 간의 관계를 학습할 수 있으며, 문맥에 따라 의미가 다르게 해석되는 경우에도 대응할 수 있음

![alt text](https://github.com/hyeran1216/hyeran1216.github.io/blob/b0f7c0d79c1bdb8d906951fe9e06106dd58a3bf9/_posts/images/attention_16.png?raw=true)

제안된 모든 변경 사항을 각 헤드에 대해 하나씩 합산한 다음 그 결과를 해당 위치의 원래 임베딩에 추가

---

### 📌 Output Matrix

Attention 메커니즘을 통해 얻은 결과는 Output Matrix에서 처리됨

특정 단어의 문맥적 의미가 업데이트되면, 이러한 의미가 문장의 다른 단어들로부터 영향을 받을 가능성이 더 많아진다. 이때 네트워크 계층이 내려갈수록 각 단어 임베딩이 더욱 추상적이고 고차원적인 개념으로 확장된다. 최종적으로, 각 단어는 문장 전체에서 문맥을 반영한 새로운 정보를 얻게 된다