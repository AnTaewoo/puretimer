<!-- # Scaffold-ETH 2로 온체인 SVG 기반 다이나믹 NFT 만들기

## 🚩 Step 0. 다이나믹 NFT (feat. 온체인 메타데이터)

NFT를 생성할 때, 메타데이터를 중앙화된 객체 저장소나 IPFS와 같은 탈중앙화 저장소에 저장하는 것이 좋은 방법이다. 이는 이미지나 JSON 객체와 같은 대용량 데이터를 온체인에 직접 저장할 때 발생하는 막대한 가스 비용을 피하기 위함이다다.

하지만 여기에는 문제가 있는데,

메타데이터를 블록체인에 저장하지 않으면 스마트 계약에서 해당 메타데이터와 상호작용하는 것이 불가능하다는 것이다. 블록체인은 "외부 세계"와 통신할 수 없기 때문이다.

메타데이터를 스마트 계약에서 직접 업데이트하려면 온체인에 저장해야 하지만, 매번 발생하는 가스 비용은 어떻게 해야할?

다행히도, Polygon과 같은 L2 체인들이 가스 비용을 대폭 줄여주는 여러 가지 장점을 제공하여 개발자들이 애플리케이션의 기능을 확장할 수 있도록 도와주고있다.

**⏩️ L2 (Layer2)란?** <br/>
L2는 기존 블록체인 위에 구축된 2차 프레임워크 또는 프로토콜을 의미한다. 이러한 프로토콜의 주요 목표는 주요 암호화폐 네트워크가 직면하고 있는 거래 속도와 확장성 문제를 해결하는 것이다.

> 🔥 이번 미션에서는 블록체인과의 상호작용에 따라 메타데이터가 변경되는 완전 동적 NFT를 온체인 메타데이터와 함께 만드는 방법을 배우고, 가스 비용을 낮추기 위해 이를 Polygon Amoy에 배포하는 방법을 배운다.

---

## 🚩 Step 1. 환경

프로젝트 클론해가기

```sh
git clone https://github.com/scaffold-eth/scaffold-eth-2.git dynamic-nft
cd dynamic-nft
yarn install
```

<br/>

**🪪 배포자 (Deployer) 설정**

`packages/hardhat/.env` 및 `packages/nextjs/.env.local`을 수정한다.

```bash
# .env
ALCHEMY_API_KEY=
DEPLOYER_PRIVATE_KEY=
POLYGONSCAN_API_KEY=
```
본인 계정의 [Alchemy](https://dashboard.alchemy.com/apps) Apps API key와 소유하고 있는 지갑의 프라이빗 키, [Polygonscan](https://polygonscan.com/apis) API Key를 기입한다.

> Metamask 지갑의 경우, 계정 세부 정보로 들어가면 프라이빗 키를 얻을 수 있다.

<br/>

**🪝 컨트랙트 배포하기**

`packages/hardhat/hardhat.config.ts`에서 defaultNetwork를 `polygonAmoy`로 변경한다.

```sh
yarn deploy
```

> ⚠️ Polygon Amoy 네트워크의 MATIC을 가지고 있지 않다면, `🚩 Step 3`를 먼저 선행한다.

<br/>

**🏛️ 프론트엔드 배포하기**

`packages/nextjs/scaffold.config.ts`를 아래처럼 변경한다.

```typescript
const scaffoldConfig = {
  targetNetworks: [chains.polygonAmoy],

  // ...

  onlyLocalBurnerWallet: false,
} as const satisfies ScaffoldConfig;
```

NestJS 애플리케이션을 배포한다. [Vercel](https://vercel.com/) 에서 로그인 후 dashboard로 이동해 `Add New -> Project` 를 클릭한 후 GitHub repository를 임포트해온다.

```shell
yarn vercel
```

📱 Vercel이 제공하는 url 로 접속해서 애플리케이션 열기

---

## 🚩 Step 2. Polygon PoS - 더 낮은 가스 비용과 더 빠른 거래

Polygon은 탈중앙화된 EVM 호환 스케일링 플랫폼으로, 개발자가 보안성을 유지하면서 저렴한 거래 수수료로 확장 가능한 사용자 친화적인 DApp을 구축할 수 있도록 한다.

Polygon은 Layer2 체인(L2)으로 설명되는 체인 그룹에 속하며, 이는 Ethereum 위에 구축되어 그 특성을 해결하는 동시에 Ethereum에 의존하여 기능하는 것을 의미한다.

Ethereum은 속도와 비용적인 면에서 효율적이지 않기 때문에, 이때 Polygon이나 Optimism과 같은 L2 솔루션이 중요한 역할을 한다.

예를 들어, Polygon은 다음과 같은 두 가지 주요 장점이 있다.

1. 더 빠른 거래 속도 (초당 65,000건 거래)
2. Ethereum보다 약 10,000배 낮은 거래당 가스 비용

이 두 번째 이유 때문에 우리는 온체인 메타데이터를 포함한 NFT 스마트 계약을 Polygon에 배포한다. Ethereum에 메타데이터를 저장할 때는 거래당 수백 달러의 비용이 들 수 있지만, Polygon에서는 몇 센트밖에 들지 않는 것이다.

> 🔍 [L2 체인이 거래 비용을 낮추고 거래 속도를 높이는 방법](https://www.one37pm.com/nft/what-are-layer-2-solutions-and-why-are-they-important)

---

## 🚩 Step 3. Metamask 지갑에 Polygon Amoy 추가

1. MetaMask 확장 프로그램 아이콘을 클릭
2. 좌측 상단의 네트워크 드롭다운 메뉴를 클릭하고 `+ 네트워크 추가` 버튼을 클릭
3. 네트워크 세부 정보 입력

    **네트워크 이름** <br/>
    Mumbai <br/>
    **네트워크 URL** <br/>
    https://rpc-amoy.polygon.technology <br/>
    **체인 ID** <br/>
    80002 <br/>
    **통화 기호** <br/>
    MATIC <br/>
    **블록 탐색기 URL** <br/>
    https://mumbai.polygonscan.com

혹은 [Chainlist](https://chainlist.org/chain/80002)에 들어가서 원하는 네트워크를 찾아 쉽게 추가할 수 있다.

네트워크가 추가 되었으면 [Polygon faucet](https://faucet.polygon.technology/)에서 테스트 MATIC을 받는다.

<img src='./images/test_faucet.png' width=300px></img>

---

## 🚩 Step 4. SVG (scalable vector graphic file)

SVG 파일은 확장 가능한 벡터 그래픽 파일의 약자로, 인터넷에서 2차원 이미지를 렌더링하는 데 사용되는 표준 그래픽 파일 유형이다. 다른 인기 있는 이미지 파일 형식과 달리 SVG 형식은 이미지를 벡터로 저장한다.

> ⏩️ **벡터** <br/>
수학 공식에 따라 점, 선, 곡선 및 모양으로 구성된 그래픽 유형

SVG 파일은 디지털 정보를 저장하고 전송하는 데 사용되는 마크업 언어인 XML로 작성된. SVG 파일의 XML 코드는 이미지를 구성하는 모든 모양, 색상 및 텍스트를 지정한다.

```xml
<svg xmlns="http://www.w3.org/2000/svg" preserveAspectRatio="xMinYMin meet" viewBox="0 0 350 350">
   <style>.base { fill: white; font-family: serif; font-size: 14px; }</style>
   <rect width="100%" height="100%" fill="black" />
   <text x="50%" y="40%" class="base" dominant-baseline="middle" text-anchor="middle">Warrior</text>
   <text x="50%" y="50%" class="base" dominant-baseline="middle" text-anchor="middle">Levels: getLevels(tokenId)</text>
 </svg>
```

SVG는 코드를 사용하여 쉽게 수정하고 생성할 수 있으며, Base64 데이터로 쉽게 변환할 수 있는 장점이 있다.

Base64 이미지는 호스팅 제공업체 없이 브라우저에서 표시할 수 있기 때문에 체인 상에 객체 스토리지 없이 이미지를 저장할 수 있게 된다.

---

## 🚩 Step 5. 민팅 (Minting)

> ✏️ 'Home' 탭에서 `Title`과 `Color`를 입력한 후 **MINT NFT** 버튼을 클릭한다.

<img src='./images/mint.png' width=300px></img>

입력한 Title과 초기 레벨 0을 메타데이터로 가진 NFT가 생성된 것을 확인할 수 있다.

---

## 🚩 Step 5. 메타데이터 업데이트

이번에는 NFT를 훈련시켜 레벨을 올려보자.

우측 하단의 `TRAIN NFT` 버튼을 클릭하면, NFT의 레벨이 올라가는 것을 확인할 수 있다.

<img src='./images/train.png' width=300px></img>


**[Mint & Train Sequence]**
<img src='./images/mint_train_sequence.png' width=400px></img>
# Puretimer: AI 기반 학습 타이머 플랫폼

`Puretimer`는 학습 집중도를 높이기 위한 **AI 기반 학습 타이머 플랫폼**입니다. 실시간 카메라 분석과 YOLOv11을 사용하여 **핸드폰 사용 여부**를 감지하고, 학습 중 핸드폰 사용 시 타이머가 자동으로 멈춥니다. 사용자는 자신의 학습 데이터를 다른 사용자와 공유하거나, 그래프로 시각화하여 분석할 수 있습니다.

<br />

---

## 🚀 주요 기능

1. **실시간 핸드폰 감지**  
   - YOLOv11과 커스텀 학습 모델(`best.pt`)을 통해 사용자의 핸드폰 사용 여부를 분석.
   - 핸드폰 사용 감지 시 학습 타이머가 자동으로 중지.
   
2. **학습 데이터 관리 및 공유**  
   - 학습한 과목, 실제 학습 시간, 낭비된 시간 등을 기록하고, 시각화된 그래프로 확인 가능.  
   - 데이터는 `Posts`로 공유 가능하며, 다른 사용자들의 학습 데이터를 열람 가능.

3. **유저 편의성 기능**  
   - 비밀번호 변경, 로그아웃, 회원 탈퇴 등 제공.

4. **보안 강화**  
   - 비밀번호는 `CryptoJS`를 사용하여 해시 처리 후 저장.  
   - SSL 인증서를 통해 데이터 암호화.

<br />

---

## 📦 기술 스택

### 🖥️ 프론트엔드
- **프레임워크**: React (TypeScript)
- **스타일링**: Tailwind CSS, Shadcn
- **데이터 시각화**: React Google Charts
- **배포**: Vercel, AWS ec2
- **데이터 저장**: LocalStorage 활용 (e.g., `isLogin`, `isDetect`)

### 🔧 백엔드
- **프레임워크**: Flask
- **구조**: MVC 아키텍처
- **데이터베이스**: MySQL
- **AI 모델**: YOLOv11 (`best.pt`)  
  - 핸드폰 사용 여부를 1초 단위로 감지하여 API로 전달.  

### 🌐 API
- HTTP 기반 통신 (`AWS 서버 제약으로 WebSocket 미지원`).  
- Proxy를 사용해 DNS 이슈 해결.

<br />

---

## 🛠️ 주요 아키텍처 설명

### 프론트엔드 구조
![Frontend Architecture](./images/frontend_architecture.png)

1. **라우팅**: React Router로 경로 관리.  
2. **레이아웃**: 경로에 따라 적합한 레이아웃 로드.  
3. **컴포넌트**: `components` 디렉토리에서 관리.  
4. **데이터 관리**:  
   - `hooks`로 공통 로직 재사용.  
   - `context`를 활용한 전역 상태 관리.  
   - Type은 `types` 디렉토리에 정의.


<br/>

---

## 백엔드 구조 및 설계

>백엔드는 Flask로 구성되었으며, MVC 패턴을 유지하여 구현되었습니다.

### 1. 초기 Config 단계
- `.env` 파일에서 MySQL 구성 정보를 로드.
- 실행 시 데이터베이스와 테이블의 존재 여부를 확인하고, 없을 경우 자동으로 생성.

### 2. 데이터베이스 설계

#### 2.1 `user` 테이블
- **사용 목적**: 회원 정보를 관리.
- **주요 컬럼**:
  - `uuid CHAR(32)`: 32자리 해시코드로 무작위 생성된 고유값.  
    → 이를 통해 유저 식별 및 관리.  
  - `email VARCHAR(120) NOT NULL UNIQUE`: 사용자 이메일 (고유값).  
  - `password VARCHAR(255) NOT NULL`: 비밀번호.  
    - 보안 이슈 방지를 위해 **CryptoJS**를 사용해 8자리 헥사코드로 암호화된 형태로 저장.  
  - `created`, `updated DATETIME`: 생성 및 수정 일시.  

#### 2.2 `studysession` 테이블
- **사용 목적**: 유저의 학습 데이터를 관리.
- **주요 컬럼**:
  - `id INT AUTO_INCREMENT`: 고유 ID로 각 기록을 구분.  
  - `user_uuid CHAR(32)`: `user` 테이블의 `uuid`를 외래키로 참조.  
  - `subject VARCHAR(255)`: 학습 과목.  
  - `start_time`, `end_time DATETIME`: 학습 시작 및 종료 시간.  
  - `real_time TIME`: 실질적인 학습 시간.  
    → **트리거**를 사용해 자동으로 계산 및 업데이트.  
  - **Cascade** 설정:  
    - 유저 탈퇴 시 해당 유저의 모든 학습 기록도 자동 삭제.  

### 3. 보안 설계

#### 비밀번호 암호화
- **문제점 해결 사례**: Meta가 GDPR 규정을 위반한 사건을 참고하여, 비밀번호 평문 저장 방지를 구현.  
- **방법**:
  - 프론트엔드: `CryptoJS`를 사용해 비밀번호를 암호화 후 전송.
  - 백엔드: SSL 인증서를 통해 페이로드 데이터를 암호화.

### 4. MVC 패턴 구조

#### 4.1 View
- 요청 경로 확인 후, 트리거된 함수를 호출.
- JSON 데이터를 `param`으로 전달.
- URL에 직접 데이터를 제공하는 경우도 지원 (`/user/read/<uuid>`).

#### 4.2 Controller
- View와 Model 간의 입출력 데이터를 관리.
- 다양한 상태에 따른 응답(Response) 및 에러(Error) 처리.

#### 4.3 Model
- **SQL 처리**:  
  - `config` 단계에서 정의한 `mysql-connector`의 connection을 활용.  
  - SQL 쿼리를 실행하고, 결과를 반환.  

#### 4.4 DetectModel 클래스
  **핸드폰 감지 로직**:
  - **Blob 데이터 처리**:  
    - API로 전달받은 Blob 이미지를 변환.  
    - OpenCV 기반의 `opencv_detector_yolov11` 라이브러리의 `detect_phone_yolo` 함수 호출.  
  - **YOLOv11 모델**:
    - Custom Dataset으로 학습된 `best.pt` 모델 사용.
    - 1초마다 분석하여 감지된 객체 중 `confidence > 0.65`인 경우 핸드폰으로 판단.  
  - **결과**:
    - `num_phone`: 감지된 핸드폰 수.
    - `confidences`: 정확도의 배열 -->

# PureTimer: AI 기반 학습 타이머 플랫폼

`PureTimer`는 YOLOv11과 커스텀 학습 데이터를 활용하여 사용자의 실시간 핸드폰 사용 여부를 감지하고, 이를 통해 학습 집중도를 향상시키는 웹 플랫폼입니다. 사용자가 카메라를 통해 학습 상황을 관찰하면, 핸드폰 사용 시 타이머가 자동으로 멈추며, 그 외에도 다양한 학습 데이터를 관리·공유할 수 있습니다.

<br />

---

## 주요 기능

1. **실시간 핸드폰 감지**  
   - YOLOv11 + 커스텀 데이터셋(`best.pt`) 기반 실시간 핸드폰 사용 여부 감지  
   - 핸드폰 사용 시 학습 타이머 자동 정지

2. **학습 데이터 관리 및 공유**  
   - 학습 과목, 실질적 학습 시간, 낭비된 학습 시간(핸드폰 사용) 데이터 관리  
   - 다른 사용자와 학습 데이터(포스트) 공유 및 열람  
   - React Google Charts 기반 그래프 시각화를 통한 데이터 분석

3. **유저 관리 기능**  
   - 비밀번호 변경, 회원 탈퇴, 로그아웃 등의 계정 관리 기능 지원

4. **보안 강화**  
   - 비밀번호는 CryptoJS를 활용한 8자리 Hex 암호화 후 저장  
   - SSL 인증서 적용을 통한 payload 암호화

<br />

---

## 기술 스택

### 프론트엔드

- **언어/프레임워크**: TypeScript, React  
- **라우팅/아키텍처**: 라우터별 Layout, `pages`, `components` 구조화  
- **스타일링**: Tailwind CSS, Shadcn  
- **시각화**: React Google Charts  
- **배포**: Vercel  
- **상태 관리**: Context, Hooks를 통한 상태 및 로직 관리  
- **LocalStorage 활용**: `isLogin`, `isDetect` 등을 저장해 특정 상황(새로고침 등)에서도 상태 유지  
- **Proxy 사용**: DNS 문제로 인한 http API 통신 제한 해결

### 백엔드

- **프레임워크**: Flask (MVC 패턴 유지)  
- **DB 연동**: MySQL  
  - **user 테이블**: uuid, email, password 등의 회원정보 관리  
  - **studysession 테이블**: user_uuid 외래키 연동, subject, start_time, end_time, waste_time, real_time 관리  
    - real_time은 트리거를 통해 자동 업데이트  
    - user 삭제 시 CASCADE로 해당 유저의 post도 삭제  
- **API**: AWS 서버 호스팅, HTTP 기반 통신  
- **보안**: 비밀번호 해시 + SSL로 payload 암호화

### YOLO 모델

- **YOLOv11**:  
  - yolov3 → yolov5 → yolov11로 업그레이드  
  - 커스텀 데이터셋을 통한 `best.pt` 모델 활용  
  - 1초 단위 Blob 이미지 처리 후 핸드폰 감지  
  - conf ≥ 0.65인 경우 핸드폰으로 판단

<br />

---

## PureTimer 아키텍처 구조

`PureTimer`는 프론트엔드와 백엔드를 분리하여 구성하였으며, 각각 명확한 디렉토리 구조를 통해 유지보수성과 확장성을 보장합니다.

### 프론트엔드 구조 (`puretimer-front/`)

프론트엔드는 **React**와 **TypeScript** 기반으로 구현되었습니다. 각 페이지별 라우팅 및 레이아웃 처리, 공통 컴포넌트와 훅(Hooks) 관리, 컨텍스트를 통한 전역 상태 관리, 타입 정의를 통한 안정적인 개발 환경을 갖추고 있습니다.

```plaintext
puretimer-front/
└─ src/
   ├─ assets/            # 정적 리소스(이미지, 아이콘)
   │  ├─ auth/
   │  ├─ dashboard/
   │  ├─ footer/
   │  └─ landing/
   │
   ├─ components/        # 재사용 가능한 UI 컴포넌트 및 페이지 전용 컴포넌트
   │  ├─ dashboard/      # 대시보드 관련 컴포넌트
   │  │  ├─ _components/
   │  │  ├─ index.ts
   │  │  ├─ Main.tsx
   │  │  └─ Profile.tsx
   │  ├─ landing/        # 랜딩 페이지 관련 컴포넌트
   │  │  ├─ _container/
   │  │  ├─ About.tsx
   │  │  ├─ index.ts
   │  │  ├─ Main.tsx
   │  │  └─ Overview.tsx
   │  ├─ mypage/         # 마이페이지 관련 컴포넌트
   │  │  ├─ _componentns/
   │  │  ├─ Analysis.tsx
   │  │  ├─ index.ts
   │  │  └─ Profile.tsx
   │  ├─ setting/        # 환경설정 관련(비밀번호 변경, 계정 삭제 등)
   │  │  ├─ DeleteAccount.tsx
   │  │  └─ SettingForm.tsx
   │  ├─ signin/         # 로그인 관련 컴포넌트
   │  │  ├─ index.ts
   │  │  ├─ SigninForm.tsx
   │  │  └─ SigninTitle.tsx
   │  ├─ signup/         # 회원가입 관련 컴포넌트
   │  │  ├─ index.ts
   │  │  ├─ SignupForm.tsx
   │  │  └─ SignupTitle.tsx
   │  ├─ study/          # 학습(감지) 관련 컴포넌트
   │  │  ├─ DetectForm.tsx
   │  │  └─ StudyForm.tsx
   │  └─ ui/             # 공용 UI 컴포넌트(aside, auth, chart, footer, header, shadcn 등)
   │     ├─ aside/
   │     ├─ auth/
   │     ├─ chart/
   │     ├─ footer/
   │     ├─ header/
   │     ├─ shadcn/      # shadcn UI 커스텀 컴포넌트
   │     │  ├─ button.tsx
   │     │  ├─ card.tsx
   │     │  ├─ chart.tsx
   │     │  ├─ input.tsx
   │     │  ├─ select.tsx
   │     │  └─ sonner.tsx
   │     └─ title/
   │
   ├─ hooks/             # 공용 로직 훅 (ex: useAlert, useCryptoValue, useDate, useFetchApi, useLocalStorage)
   │  ├─ useAlert.tsx
   │  ├─ useCryptoValue.tsx
   │  ├─ useDate.tsx
   │  ├─ useFetchApi.tsx
   │  └─ useLocalStorage.tsx
   │
   ├─ layouts/           # 라우팅별 레이아웃 (AuthLayout, DashBoardLayout, LandingLayout)
   │  ├─ AuthLayout.tsx
   │  ├─ DashBoardLayout.tsx
   │  └─ LandingLayout.tsx
   │
   ├─ lib/               # 공용 유틸 함수
   │  └─ utils.ts
   │
   ├─ pages/             # 페이지 컴포넌트 (라우팅 엔트리 포인트)
   │  ├─ dashboard/
   │  │  └─ DashBoard.tsx
   │  ├─ landing/
   │  │  └─ Landing.tsx
   │  ├─ mypage/
   │  │  └─ Mypage.tsx
   │  ├─ setting/
   │  │  └─ Setting.tsx
   │  ├─ signin/
   │  │  └─ Signin.tsx
   │  ├─ signup/
   │  │  └─ Signup.tsx
   │  └─ study/
   │     ├─ Detect.tsx
   │     └─ Study.tsx
   │
   ├─ provider/context/  # 전역 상태 관리(Context API)
   │  ├─ ContextProvider.tsx
   │  ├─ DetectContext.tsx
   │  ├─ index.ts
   │  ├─ LandingContext.tsx
   │  └─ UserContext.tsx
   │
   ├─ router/            # 라우터 설정
   │  └─ Router.tsx
   │
   ├─ types/             # 타입 정의
   │
   ├─ index.css          # 전역 스타일
   └─ main.tsx           # 앱 엔트리 포인트
   ```
### 백엔드 구조 (puretimer-back/)

백엔드는 **Flask** 기반의 **MVC 패턴**으로 구현되었습니다. View를 통한 라우팅 및 JSON 응답 처리, Controller를 통한 요청/응답 로직 및 예외 처리, Model을 통한 DB 연동과 YOLO 감지 로직 호출을 통해 명확한 관심사 분리와 유지보수성을 확보하였으며, config.py를 통해 DB 접속 정보 및 초기 테이블 생성 로직을 관리하고, opencv_detector_yolov11.py를 활용해 실시간 핸드폰 감지 기능을 구현하였습니다.

```planetext
puretimer-back/
├─ controller/
│  └─ controller.py          # 요청/응답 처리 및 비즈니스 로직 제어
│
├─ model/
│  └─ model.py               # DB 쿼리 및 YOLO 감지 로직 호출
│
├─ runs/                     # YOLO 실행 결과, 추가 리소스
│
├─ view/
│  └─ view.py                # Flask 라우팅, JSON 응답 반환
│
├─ .env                      # 환경 변수 (DB 접속 정보 등)
├─ .gitignore
├─ app.py                    # Flask 앱 실행 엔트리 포인트
├─ best.pt                   # 커스텀 학습된 YOLOv11 모델 가중치
├─ config.py                 # DB 설정, 초기화 로직
└─ opencv_detector_yolov11.py # YOLOv11 기반 핸드폰 감지 함수 구현
```