```
학습시 정확도 대신 손실함수를 사용하는 이유??
- 교재 밑바닥부터 시작하는 딥러닝, 119P. 
- 최적화 부분과 연계 하여 학습 하기 
```


# 오차 함수와 출력층의 설계

|문제의 유형|활성화 함수|오차함수|
|-|-|-|
|회귀|항등사항|제곱오차식 = $$E(w) = \frac{1}{2} \sum_{n=1}^n\parallel d-y(x;w)\parallel^2
$$|
|이진분류|로지스틱 함수|$$ E(w) = -\sum_{n=1}^{N}[d_n \log y(x_n;w) + (1+d_n) \log{1-y(x_n;w)}] $$|
|다클래스|소프트맥스함수|교차엔트로피 = $$ E(w) = -\sum_{n=1}^{N}\sum_{k=1}^{K} d_{nk} \log y_k(x_n;w) $$|

>  교차엔트로피를 ACE(Average Cross-Entropy)라고 부르기도 함
> - 제곱오차식(MSE)와 비슷하게 표현하기 위해

## 1. 회귀 
### 1.1 정의 
* 출력값이 연속값을 같는 함수 대상

### 1.2 활성화 함수
* 목적으로 하는 함수와 같은 치역을 갖는 함수를 출력층의 활성화 함수로 설정 해야 함
    * 목표함수가 [-1"1]= 쌍곡선 정접함수(=쌍곡선 탄젠트 함수)
    * 목표함수가 임의의 실수($$ -\infty:\infty $$) = 항등사상


### 1.3 오차함수 
제곱 오차 (Square Loss) = 평균 제곱 오차(mean-squared error, MSE)

$$
E(w) = \frac{1}{2} \sum_{k}(y_k - t_k)^2
$$
- $$y_k$$: 출력값(예상값)
- $$t_k$$: 정답(원래값)
- k = 데이터의 차원수 

> $$ \frac{1}{2} $$를 하는 이유는 미분시 2가 곱해지는걸 상쇄 하기 위함 


두 벡터간의 유클리드 거리(Euclidian distance)를 측정합니다. 

두 벡터가 가까워질수록 제곱차 손실은 줄어듭니다. 

제곱차 손실은 yy에 대한 볼록함수이기 때문에 손실 함수로 사용하기 좋습니다. 

단점 : 하지만 훈련 속도가 느리고 성능이 다른 손실 함수들보다 많이 떨어짐


## 2. 이진분류 
### 2.1 정의 
* 입력 x에 대하여 두 종류로 구별하는 문제 

### 2.2 활성화 함수
* 로지스틱 함수

### 2.3 오차함수 


* 선형관계에 놓여 있지 않은 일반화 선형모형에서는 `최대우도법`활용 
$$
E(w) = -\sum_{n=1}^{N}[d_n \log y(x_n;w) + (1+d_n) \log{1-y(x_n;w)}] 
$$


## 3. 다클래스 
### 3.1 정의 
* 손글씨 분류등의 문제 (0~9까지 숫자)
* 출력층에 분류하려는 클래스 수(L)과 같은 수의 유닛을 구성 

### 3.2 활성화 함수
* 소프트맥스 함수 
    * 각 결과 확률값을 출력, 가장 높은 확률을 결과로 선택 
    * 출력 총합이 항상 1

### 3.3 오차함수 
* 교차 엔트로피(cross entropy, negative log likehood)
$$
E(w) = -\sum_{n=1}^{N}\sum_{k=1}^{K} d_{nk} \log y_k(x_n;w)
$$


> [왜 크로스-엔트로피를 쓸까?](https://youtu.be/srdDQr07sGg): 크로스-엔트로피를 코스트함수로 사용하는 이유 중 두개를 소개

## 4. 이진분류 + 다클래스 분류 : Entrpy Loss 이용 

### 4.1 Entropy Loss

- 기본적으로 Kullback-Leibler 발산(KL divergence)에 기반을 두고 있습니다. 
    - KL은 두 확률분포 사이의 거리(distance)를 측정합니다. 
    - 다이버전스:서로 다르게 움직이는 현상

- 즉, 두 확률분포가 얼마나 유사한지를 나타내 줍니다. 

###### Step 1. 이산확률변수(discrete random variable)의 경우에 대한 식은 아래와 같습니다. 

$$
D_{KL}(P,Q) = D_{KL}(P \parallel Q) = \sum_i p_i\log\frac{p_i}{q_i}

$$
- p,q는 확률분포: 모든 원소가 0 이상 1 이하의 값, 총 합은 1
    - p : 정답 확률분포
    - q : 예측 확률분포
- p,q가 완전히 동일(분포가 동일)하다면 D=0 
    - 목표 : 정답과 예측값의 확률 분포가 같도록 만들기

###### Step 2. p는 외부에서 주어지(정답확률 분포)는 고정된 값(C)이므로 

$$
D_{KL}(P,Q) = \sum_i p_i\log\frac{p_i}{q_i} = \sum_i p_i\log p_i - \sum_i p_i\log q_i (로그의 뺼셈 성질)


$$
$$
= C- \sum_i p_i\log q_i 
$$

###### Step 3. 최종식 도출 (교차 엔트로피:cross entropy)
$$
L = - \sum_i p_i\log q_i   
$$

> 가능도(likelihood)를 통해 서도 식을 유추 할수 있어 `음수 로그 가능도(negative log-likelihood)`라도고 함 
> cf. 유도식이 다른것 살펴 보기 

### 4.2 Entropy Loss for Binary
- y가 0 혹은 1만 되는 경우(즉, 이진 분류 문제인 경우)

$$
L = - t\log y - (1-t) \log (1-y)
$$


### 4.3 Entropy Loss for Categorical 
- y가 여러 클래스를 갖는 경우

$$
L = -\sum_{i=1}^C t_i \log y_i
$$

- log는 밑이 e인 자연로그 
- $$y_i$$: 신경망의 출력 
- $$t_i$$: 정답 레이블 (One-hot-encording:정답에 해당하는 인덱스만 1, 나머지는 0)

###### [예제] 손글씨 탐지 문제 

- t = 1일때는 $$-\sum 1 \log y_i$$ 계산값 출력 
- t = 0일때는 $$-\sum 0 \log y_i$$이므로 0을 곱하여 모두 0이 됨

![](http://i.imgur.com/XLE1EMh.png)

|분류|1|2|3|4|5|6|7|8|9|10|
|-|-|-|-|-|-|-|-|-|-|
|$$t$$|0|0|1|0|0|0|0|0|0|0|
|$$y_{정답}$$|0.1|0.05|0.6|0.0|0.05|0.1|0.0|0.1|0.0|0.0|
|$$-\sum t_i \log y_i$$|0|0|0.51|0|0|0|0|0|0|0|
> 3번째를 60%확률로 정답이라고 판단. 

|분류|1|2|3|4|5|6|7|8|9|10|
|-|-|-|-|-|-|-|-|-|-|
|$$t$$|0|0|1|0|0|0|0|0|0|0|
|$$y_{오답}$$|0.1|0.05|0.1|0.0|0.05|0.1|0.0|0.6|0.0|0.0|
|$$-\sum t_i \log y_i$$|0|0|2.30|0|0|0|0|0|0|0|
> 8번째를 60%확률로 정답이라고 판단

정답은 3번째 로그값이므로 오답일때가 값이 더 큼 2.30 > 0.51 


---
[참고: About loss functions, regularization and joint losses : multinomial logistic, cross entropy, square errors, euclidian, hinge, Crammer and Singer, one versus all, squared hinge, absolute value, infogain, L1 / L2 - Frobenius / L2,1 norms, connectionist temporal classification loss](http://christopher5106.github.io/deep/learning/2016/09/16/about-loss-functions-multinomial-logistic-logarithm-cross-entropy-square-errors-euclidian-absolute-frobenius-hinge.html)
   
 
---

###### [참고] Cross-Entropy(ACE)와 학습 속도 향상 
- 기존 방식(MSE + Sigmoid함수) : 오차가 클수록 학습 속도가 빨라야 할 것 같은데 그렇지 못하다
- 개선 방식(ACE + Sigmoid함수) : 오차 비례하는 결과를 얻을 수 있음

> 자세한 내용은 라온피플 블로그 참고 : [Class 14](http://laonple.blog.me/220489989951), [Class 15](http://laonple.blog.me/220489989951)