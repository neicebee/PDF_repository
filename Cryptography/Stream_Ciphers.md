# 💻 2025-1 Cryptography

## 👀 [02] Stream Ciphers

### 스트림 암호와 블록 암호(Stream Cipher & Block Cipher)
- 스트림 암호
  - bit 단위로 암호화 수행
  - 평문과 동일한 길이의 키스트림(key stream) 수열을 생성하며, 평문과의 **XOR**연산을 통하여 수행
    - 동기식(Synchronous) 스트림 암호: 키스트림 수열 생성 시, 평문과 독립적으로 생성하는 경우
    - 비동기식(Asynchronous) 스트림 암호: 평문이 키스트림 수열에 영향을 미치는 경우
  - 일반적으로 작고 빠름 => 임베디드 디바이스에 주로 사용(예: GSM 이동 통신에서의 A5/1)
- 블록 암호
  - 블록 단위(Several bits)로 암호화 수행하며, 각 블록마다 동일한 키 사용
    - 고정된 크기로 평문을 나누기 때문에, 원하는 길이를 맞추기 위해 패딩(padding) 기법 사용
    - 하나의 키로 여러 블록을 안전하게 처리하기 위해 운용 방식(mode of operation) 절차 사용
  - Shannon의 암호 이론: 전치와 환자를 반복시켜 평문의 통계적 성질이나 암호 키와의 관계가 나타나지 않기에 안전한 암호를 구성할 수 있음
  - 인터넷 응용 분야에서 다양하게 사용되고 있음

<br>

### 스트림 암호의 암호화 및 복호화
- 수학적 표현
  - Plaintext $x_i$, Ciphertext $y_i$, Key Stream $s_i(x_i,y_i,s_i\in{{0,1}})$에 대하여
  - Encryption: $y_i=e_{s_i}(x_i)\equiv{x_i}+s_i$ $mod$ $2$
  - Decryption: $x_i=d_{s_i}(y_i)\equiv{y_i}+s_i$ $mod$ $2$
- 암호화와 복호화 모두 모듈러 2 상의 덧셈(XOR)
- _암호화와 복호화 함수가 동일_

<br>

### 동기식(Sychronous)과 비동기식(Asynchronous) 스트림 암호
- 스트림 암호의 안전성은 온전히 키 스트림 $s_i$에 달려있음
  - 필히 랜덤이어야 함
    - $Pr(s_i=0)=Pr(s_i=1)=0.5$
    - 송신자와 수신자 모두 재생성할(Reproducible) 수 있어야 함
- 동기식 스트림 암호
  - 키 스트림 생성이 오직 키(그리고 초기화 벡터(IV; Initialization Vector))의 영향을 받음
- 비동기식 스트림 암호
  - 키 스트림 생성이 키뿐만 아니라 암호문 자체의 영향을 받음

<br>

### 왜 모듈러 2 상의 덧셈이 좋은 암호화 함수(Good Encryption Function)인가?
- 모듈러 2 덧셈은 XOR(Exclusive-OR) 연산과 동일
- 완벽하게 랜덤한 키 스트림 $s_i$에 대하여, 각 암호문의 출력 bit가 0 또는 1이 될 확률이 50%라는 사실은 **좋은 통계적 특성**을 가진다는 의미
- XOR의 역변환(Inversion)이 단순함
  - XOR 연산과 XOR의 역변환 연산이 동일

<br>

### 스트림 암호의 성능(Throughput)
- 대칭 암호의 성능 비교(Pentium 4)
  - DES
    - 키 길이: 56
    - 36.95 Mbit/s
  - 3DES
    - 키 길이: 112
    - 13.32 Mbit/s 
  - AES
    - 키 길이: 128
    - 51.19 Mbit/s
  - RC4(stream cipher)
    - 키 길이: choosable
    - 211.34 Mbit/s

<br>

### 난수 생성기(Random Number Generators)
- 진정한 난수 생성기(TRNGs; True RNGs)
- 의사 난수 생성기(PRNGs; (General)Pseudo-RNGs)
- 암호학적으로 안전한 의사 난수 생성기(CSPRNGs; Cryptographically Secure PRNGs)

<br>

### TRNGs
- 물리적으로 무작위인 과정에 기반
  - 동전 던지기, 주사위 던지기, 반도체 노이즈, 방사성 붕괴 등
- 출력 키 스트림 $s_i$가 좋은 통계적 특성을 가져야 함
  - $Pr(s_i=0)=Pr(s_i=1)=0.5$
  - 종종 후처리(Post-Processing)되기도 함
- 출력 결과가 절대 예측되어서도, 재성성되어서도 안됨
  - 불규칙적이어야 함
- 일반적으로 Key, Nonces(오직 한 번만 사용되는 값) 생성 등에 사용
- Session-Key 생성에도 활용

<br>

### PRNGs
- 초기 시드 값(Initial Seed Value)으로부터 계산되는 난수열(Random Sequences)
- 일반적으로, 출력되는 스트림(Output Stream)은 좋은 통계적 특성을 가짐
  - 단, 출력 결과는 재생성될 수 있고 예측될 수 있음
- 수학적 표현
  - $s_0=seed$
  - $s_{i+1}=f(s_i),$ $i=0,1,...$
  - (Generally, $s_{i+1}=f(s_i,s_{i-1},...,s_{i-t}),$ $t\in{Z}$)
- ANSI C의 rand 함수
  - $s_0=12345$
  - $s_{i+1}=1103515245s_i+12345$ $mod$ $2^{31},$ $i=0,1,...$
- 대부분의 PRNGs는 좋은 암호학적 특성을 갖고 있지 않음

<br>

### 간단한 PRNG에 대한 암호분석(Crptanalysis)
- 선형 합동 생성기(Linear Congruential Generator)
  - $S_0=seed$
  - $S_{i+1}=AS_i+B$ $mod$ $m$ $i=0,1,...$ $with$ $A,B,S_i\in{Z_m}$
  - Assumptions
    - $m$은 공개된 데에 반해, $A,B,S_0$ 등은 Key이며 알려지지 않음
    - $A,B,S_0,m$ 등의 크기는 100-bit
    - 300-bit 크기의 출력값($S_1,S_2,S_3$)이 알려짐
  - Solving
    - $S_2=AS_1+B$ $mod$ $m$
    - $S_3=AS_2+B$ $mod$ $m$
    - $A\equiv(S_2-S_3)/(S_1-S_2)$ $mod$ $m$과 $B\equiv{S_2}-S_1(S_2-S_3)/(S_1-S_2)$ $mod$ $m$ 유추 가능
      - 이를 이용해 모든 $S_i$ 계산 가능
- 대부분 PRNGs가 가지고 있는 선형성(Linearity)으로 인해 좋지 않은 암호학적 특성 존재

<br>

### CSPRNGs
- 부가적인 특성을 갖는 특별한 PRNG
  - 출력 결과가 반드시 **_예측 불가능(Unpredictable)_** 해야 함
  - $n$개의 연속적인 bit의 출력 $s_i$에 대해, 그 다음 출력 bit $s_{i+1}$이 예측될 수 없어야 함
  - 암호기술(특히, 스트림 암호)에서 필요
- 예측 불가능성을 팔요로 하는 암호기술 분야를 제외하고 대부분의 응용 시스템에서는 PRNGs를 사용

<br>

### One-Time Pad(OTP)
- 무조건적인 안전성(Unconditional Security)
  - 무한의 계산 자원을 가지고도 해독할 수 없는 암호시스템을 무조건적으로(Unconditionally) 또는 정보 이론적(Information-Theoretically)으로 안전하다고 함
- OTP
  - Vernam 스트림 암호에 기반
  - 특성
    - Plaintext $x_i$, Ciphertext $y_i$, Key $k_i$, with $x_i,y_i,k_i\in{[0,1]}$에 대하여
    - Encryption: $y_i=e_{k_i}(x_i)\equiv{x_i}+k_i$ $mod$ $2=x_i⊕k_i$
    - Decryption: $x_i=d_{k_i}(y_i)\equiv{y_i}+k_i$ $mod$ $2=y_i⊕k_i$
  - Key가 **오직 한 번만 사용되는(Only Used Once)** 경우라면 OTP는 무조건적으로 안전함
  - 키의 길이가 반드시 메시지만큼 길어야 하므로 OTP는 **비실용적(Impractical)**

<br>

### 실용적인(Practical) 스트림 암호
- 대부분 잘 알려진 암호시스템은 무조건적인 안전성을 지니지 않음
- 계산적 안전성(Computational Security)
  - 암호시스템을 해독하는데 가장 좋다고 알려진 알고리즘이 적어도 $t$번의 연산이 필요할 때 해당 암호시스템을 계산적으로 안전하다고 함
