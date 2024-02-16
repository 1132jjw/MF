- 넘쳐나는 정보속에서 사용자에게 알맞는 상품을 추천하기 위해 개인화 추천이 필요하다.

### 추천 시스템 전략

- 추천 시스템의 전략은 크게 두 가지로 나뉜다.
    - content filtering
    - collaborative filtering
- collaborative filetering은 도메인이 상관없다는 매력이 있다
- 하지만 cold start라는 단점이 존재
- collaborative filtering에는 두 가지 영역이 존재
    - 메모리 기반
    - 모델 기반
- Latent Factor model의 latent factor는 20~100개로 추론이 되는데, 이때 각각이 정확히 어떤 의미를 갖는지를 알 순 없음
- Latent Factor는 Hypterparameter로 K를 적당하게 잡아야 핵심적인 정보외의 노이즈를 제거할 수 있다

### A Basic Matrix Factorization Model

- 레이팅을 유저와 아이템 벡터로 행렬 분해로 나타낸다
- $\hat r_{ui} = q^T_ip_u$
- 유저의 latent는 유저의 장르 선호도를 나타내고, 아이템의 latent는 그 장르의 성격을 얼마나 띠는지를 나타냄
- SVD는 sparse한 환경에서 잘 작동하지 않음
- SVD는 잘 작동하지 않기 때문에 최근의 연구에서는 관찰한 rating만을 사용하기로 함.
- 또한 overfitting을 경계하기 위해 l2 regularization을 함.

### Learning Algorithms

- 두 가지 방식의 최적화가 있다.
    - SGD, ALS

**SGD**

- $e_{ui} = r_{ui} - q^T_ip_u$
- 에러를 구한 후, 하나씩 업데이트
- $q_i \leftarrow q_i + \gamma(e_{ui}p_u-\lambda q_i)$
- $p_u \leftarrow p_u + \gamma(e_{ui}q_i-\lambda p_u)$

**ALS**

- q, p 둘 다 모르기 때문에 convex(집합안에 두 점을 이었을때 끊기는 곳이 없음, 수학적으로 최적화하지 못한다는 뜻)하지 않다.
- 사용하기 좋은 상황
    - 병렬 프로그래밍: $q_i$를 학습할 때, 다른 item factor와 독립적으로 계산되기 때문에 병렬 적용이 용이함. ($p_u$도 마찬가지)
    - implicit data: Explicit data에 비해 결측치가 비교적 적기 때문에, SGD를 사용하게 되면 연산량이 증가함.

### Adding Biases

- 영화마다, 유저마다 편향되어있을 수 있기 때문에 이를 고려해주기 위해서 bias를 더해준다.
- $b_{ui} = \mu + b_i + b_u$
- 규제항도 똑같이 적용한다.
- $(\|p_u\|^2 + \|q_i\|^2 + b^2_u + b^2_i)^2$

### Additional Input Sources

- cold start 문제를 해결하기 위해 explicit data를 제외한 것들을 포함한다.
- user u가 아이템에 대해 implicit feedback을 나타낸 데이터 $\sum\limits_{i\in N(u)}x_i$
- 거기에 $\|N(u)\|^{-0.5}$를 곱함
- 이런식으로 추가할 수 있음

$\hat r_{ui} = \mu + b_i + b_u + q^T_i[p_u + \|N(u)\|^{-0.5}\sum\limits_{i\in N(u)}x_i]$

### Temporal Dynamics

- 시간에 따라 유저의 취향이 달라지고, 아이템의 평가, 여론이 달라질 수 있으므로 시간과 관련된 함수로 나타내야 함.

$\hat r_{ui}(t) = \mu + b_i(t) + b_u(t) + q^T_ip_u(t)$

### Inputs with varying confidence levels

- 어떤 아이템을 엄청난 광고로 인해 사람들이 많이 봤을 수 있다. 또한 어떤 유저는 같은 아이템을 여러 번 봤을 수 있다.

### Netflix prize competition

- 성능 순서
- plain
- with biases
- with implicit
- with temporal dynamics
- 시간의 흐름 넣은게 결과가 제일 좋았음