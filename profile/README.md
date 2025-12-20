# CAU-ClearCrew

## 🎯 프로젝트 개요

**CAU-ClearCrew**는 인사(HR) 관리 기능과 익명 신고(Whistleblowing) 시스템을 결합한 모바일 애플리케이션입니다.  
블록체인과 Zero-Knowledge Proof(ZKP)를 활용하여 신고자의 신원을 노출하지 않으면서도 검증 가능한 신고 환경을 제공합니다.

본 프로젝트는 **중앙대학교 모바일 앱 개발 수업 팀 프로젝트**로 진행되었습니다.

---

## ✨ 주요 기능

- **HR 관리**
  - 직원 정보 등록 / 수정 / 삭제
  - 직무 및 부서 관리
- **익명 신고**
  - 블록체인 기반 검증
  - ZKP를 통한 신고자 신원 보호
- **신고 처리 워크플로우**
  - 신고 접수 → 검토 → 처리 → 기록 보관

---

## 🛠 기술 스택

- Design: Figma
- Frontend: Android (Kotlin, Jetpack Compose)
- Backend: Kotlin / Node.js
- Blockchain & ZKP: Solidity, Noir, JavaScript
- Network: Arbitrum / Arbitrum Sepolia

---

## 📁 Repository 구조

```
.
├── Frontend/     # Android 앱
├── Backend/      # API 서버
├── contracts/    # 스마트 컨트랙트
├── zkCircuit/    # ZKP 서킷 (Noir)
└── profile/      # 프로젝트 소개 문서
```

각 디렉터리에는 모듈별 상세 설명과 실행 방법이 포함된 README가 있습니다.

## 시퀀스 다이어그램

아래 시퀀스 다이어그램은 어플리케이션의 주요 흐름을 요약합니다.

![시퀀스 다이어그램](../assets/sequence.png)

사용자가 앱에서 제보를 생성하면 전체 처리 흐름은 다음과 같습니다.

1. 사용자가 앱에서 제보 내용을 작성하고 제출합니다.
2. 제보 내용은 암호화되어 IPFS에 업로드되고, 생성된 IPFS CID를 확보합니다.
3. 오프체인(앱 또는 백엔드)에서 ZK 서킷을 이용해 증명을 생성하기 위해 필요한 Merkle path 등 데이터를 준비합니다.
4. IPFS CID와 ZK 증명을 스마트 컨트랙트에 제출하는 트랜잭션을 전송합니다(앱에서 직접 전송하거나 백엔드를 통해 전송할 수 있음).
5. 스마트 컨트랙트는 제출된 증명을 검증하고, 검증 성공 시 이벤트를 발생시킵니다.
6. 백엔드 서버는 해당 이벤트를 리스닝하여 IPFS에서 제보 데이터를 가져오고, 복호화한 뒤 데이터베이스에 저장하거나 상태를 업데이트합니다.

---

## ▶️ How to Run (Quick Start)

로컬 환경에서 실행하기 위해서는  
**Smart Contract → Backend → Frontend** 순서로 실행하는 것을 권장합니다.

---

### 1️⃣ Smart Contract 배포

Arbitrum 또는 Arbitrum Sepolia 네트워크를 사용합니다.  
Foundry가 설치되어 있어야 합니다.

```bash
cd contracts
forge script script/deployer.s.sol:ClearCrewDeploy \
  --rpc-url <RPC_URL> \
  --private-key <DEPLOYER_PRIVATE_KEY> \
  --broadcast
```

배포 완료 후 출력되는 `CONTRACT_ADDRESS`를 기록합니다.

---

### 2️⃣ Backend 실행

```bash
cd backend
npm install
npx prisma migrate dev
npm start
```

#### 필수 환경 변수 (.env)

```env
DATABASE_URL="mysql://USER:PASS@localhost:3306/DBNAME"
JWT_SECRET="your_jwt_secret"
BLOCKCHAIN_NODE_URL="https://..."
SERVER_WALLET_PRIVATE_KEY="0x..."
CONTRACT_ADDRESS="0x..."
```

백엔드는 직원 관리, Merkle Tree 관리, 신고 데이터 수신 및  
스마트 컨트랙트 연동을 담당합니다.

---

### 3️⃣ Frontend 실행 (Android)

1. Android Studio에서 `Frontend/` 폴더 열기
2. `local.properties`에 아래 값 추가

```properties
PINATA_JWT="your pinata jwt token"
RPC_URL=https://...
CONTRACT_ADDRESS=0x...
DEV_PRIVATE_KEY=0x...
```

3. Gradle Sync 후 에뮬레이터 또는 실제 디바이스에서 실행

---

### 4️⃣ ZK Circuit (선택)

Noir 기반 ZKP 서킷은 **오프체인 증명 생성**에 사용됩니다.  
프론트엔드 또는 백엔드에서 Merkle path 및 leaf 데이터를 받아  
증명을 생성한 뒤 스마트 컨트랙트로 제출합니다.

---

### ⚠️ 주의 사항

- 프론트엔드 및 백엔드에서 **온체인 트랜잭션**이 발생할 수 있으므로  
  사용 지갑에는 해당 네트워크의 ETH(테스트 시 Sepolia ETH)가 필요합니다.
- RPC URL, 개인키 등 민감 정보는 **절대 Git에 커밋하지 마세요**.

---

## 🎯 프로젝트 목표

기술적으로 검증 가능한 익명 신고 시스템을 통해  
조직 내 **신뢰성 있는 신고 문화**와 **투명한 인사 관리 환경**을 구축하는 것을 목표로 합니다.

---

## 🤝 기여

- 버그 제보 및 기능 제안은 Issue로 등록해 주세요.
- 코드 기여는 Pull Request를 통해 진행합니다.

---

_Mobile App Development Course · Team Project · Chung-Ang University_
