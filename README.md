<div align="center">

# **서비스 하나를 처음부터 끝까지 만들고, 배포까지 직접 책임지는 백엔드 개발자**

- 공주대학교 컴퓨터공학과
- Spring Boot 백엔드
- iOS

</div>

<br>

## 👋 About Me

- **Java · Spring Boot** 백엔드를 주력으로, 프론트엔드(Next.js)와 iOS(Swift)까지 직접 만들어 보며 서비스 전체 흐름을 이해하려고 합니다.
- 기능을 만드는 것에서 끝내지 않고 **Docker로 실행 환경을 묶고, GitHub Actions로 테스트를 자동화**하는 것까지를 개발의 범위로 생각합니다.
- 기능 단위로 브랜치를 나누고 PR로 병합하는 흐름을 혼자 하는 프로젝트에서도 지키고 있습니다.
- 막혔던 문제와 해결 과정을 코드 주석과 커밋, 그리고 obsidian에 트러블 슈팅 일지를 남기며 이해하려고 노력을 합니다.

<br>

## 🛠 Tech Stack

| 분야 | 기술 |
| :-- | :-- |
| **Backend** | ![Java](https://img.shields.io/badge/Java_21-007396?style=flat-square&logo=openjdk&logoColor=white) ![Spring Boot](https://img.shields.io/badge/Spring_Boot-6DB33F?style=flat-square&logo=springboot&logoColor=white) ![Spring Security](https://img.shields.io/badge/Spring_Security-6DB33F?style=flat-square&logo=springsecurity&logoColor=white) ![JPA](https://img.shields.io/badge/Spring_Data_JPA-6DB33F?style=flat-square&logo=spring&logoColor=white) |
| **Frontend** | ![Next.js](https://img.shields.io/badge/Next.js-000000?style=flat-square&logo=nextdotjs&logoColor=white) ![TypeScript](https://img.shields.io/badge/TypeScript-3178C6?style=flat-square&logo=typescript&logoColor=white) |
| **Mobile** | ![Swift](https://img.shields.io/badge/Swift-F05138?style=flat-square&logo=swift&logoColor=white) ![SwiftUI](https://img.shields.io/badge/SwiftUI-0D96F6?style=flat-square&logo=swift&logoColor=white) |
| **DevOps** | ![Docker](https://img.shields.io/badge/Docker-2496ED?style=flat-square&logo=docker&logoColor=white) ![GitHub Actions](https://img.shields.io/badge/GitHub_Actions-2088FF?style=flat-square&logo=githubactions&logoColor=white) ![AWS](https://img.shields.io/badge/AWS_EC2_%7C_S3-232F3E?style=flat-square) |
| **Database** | ![MySQL](https://img.shields.io/badge/MySQL-4479A1?style=flat-square&logo=mysql&logoColor=white) ![Redis](https://img.shields.io/badge/Redis-FF4438?style=flat-square&logo=redis&logoColor=white) |
| **Language** | ![C](https://img.shields.io/badge/C-A8B9CC?style=flat-square&logo=c&logoColor=black) ![C++](https://img.shields.io/badge/C%2B%2B-00599C?style=flat-square&logo=cplusplus&logoColor=white) |

<br>

## 🚀 대표 프로젝트

### 🛒 ShoppingMall — 혼자 만드는 풀스택 쇼핑몰

> 회원가입부터 장바구니, 주문, **토스페이먼츠 결제**까지 실제 쇼핑몰의 핵심 흐름을 구현한 프로젝트

| 기간 | 역할 | 기술 | 저장소 |
| :-- | :-- | :-- | :-- |
| 2026.02 ~ 진행 중 | 1인 개발 (설계 · 백엔드 · 프론트 · 인프라) | Spring Boot 3 · Java 21 · Next.js · MySQL · Redis · Docker · GitHub Actions | [potatostore/ShoppingMall](https://github.com/potatostore/ShoppingMall) |

**핵심 구현**
- **인증**: Spring Security + JWT. Access/Refresh 토큰을 `HttpOnly · Secure` 쿠키로 발급하고, Refresh 토큰은 **Redis에 TTL과 함께 저장**해 로그아웃 시 서버에서 즉시 무효화
- **결제**: 토스페이먼츠 결제 승인 API 연동. 주문 생성 → 결제 → 결과 확인까지 이어지는 E2E 시나리오 화면 구성
- **도메인 API**: 회원 · 상품 · 장바구니 · 주문 REST API와 Swagger(OpenAPI) 문서화, 전역 예외 처리와 공통 응답 포맷(`ApiResponse`) 통일
- **테스트 · CI**: 서비스/컨트롤러 테스트 52개 작성. GitHub Actions에서 MySQL 컨테이너를 띄우고 준비될 때까지 대기한 뒤 테스트를 실행
- **실행 환경**: MySQL · Redis · Spring 서버 · Next.js 서버를 `docker compose up` 한 번으로 실행. 백엔드는 멀티 스테이지 빌드로 런타임 이미지 경량화
- **작업 방식**: 1인 프로젝트지만 기능별 브랜치 → PR → 병합 흐름으로 관리 (PR #20까지 진행)

<details>
<summary><b>💡 설계 포인트 & 트러블슈팅</b></summary>

<br>

**1. 결제 금액 위변조 방지**
클라이언트가 보낸 결제 금액을 그대로 승인 요청에 쓰면 금액을 조작할 수 있습니다. 승인 전에 **서버에 저장된 주문 총액과 요청 금액을 비교**하고, 다르면 토스 API를 호출하지 않고 예외로 처리했습니다.

**2. 결제 실패 기록이 롤백되는 문제**
결제 실패 시 주문 상태를 `FAILED`로 바꾸고 예외를 던지면, 트랜잭션이 롤백되면서 실패 기록까지 사라졌습니다. `@Transactional(noRollbackFor = PaymentException.class)`로 **실패 상태는 커밋하면서 클라이언트에는 에러를 전달**하도록 바꿨습니다.

**3. 결제 응답 분기 누락 방지**
토스 API는 성공과 실패 시 응답 형태가 다릅니다. 응답을 `sealed interface TossResponse permits Payment, TossError`로 모델링하고 Java 21 패턴 매칭 `switch`로 처리해, **새로운 응답 타입이 생기면 컴파일 단계에서 누락을 잡을 수 있게** 했습니다.

**4. CI에서 DB가 준비되기 전에 테스트가 실행되는 문제**
컨테이너가 떴다고 MySQL이 바로 연결을 받는 것은 아니어서 테스트가 간헐적으로 실패할 수 있습니다. `mysqladmin ping`이 성공할 때까지 기다린 뒤 테스트를 실행하도록 워크플로를 구성했습니다.

</details>

<br>

### 📷 Graphos — 자연어로 찾는 온디바이스 사진 검색 iOS 앱

> "기프티콘 찾아줘", "고양이 사진인데 사람은 없는 거" 처럼 **말하듯 검색하면 갤러리에서 사진을 찾아주는 앱**. 모든 처리는 기기 안에서 이루어집니다.

| 기간 | 역할 | 기술 | 저장소 |
| :-- | :-- | :-- | :-- |
| 2026.05 ~ 진행 중 | iOS 앱 개발 | Swift · SwiftUI · SwiftData · Vision · Foundation Models | [Graphos-Application/Graphos-IOS](https://github.com/Graphos-Application/Graphos-IOS) |

<!-- TODO: 팀 인원과 본인 역할을 확인해 위 '역할' 칸을 구체적으로 적어 주세요. (예: iOS 1인 개발 / 3인 팀 중 iOS 담당) -->

**핵심 구현**
- **사진 자동 라벨링**: Vision 이미지 분류로 갤러리 사진마다 라벨을 붙이고 SwiftData에 저장
- **자연어 검색**: Apple 온디바이스 언어 모델(Foundation Models)로 사용자 문장에서 **포함할 라벨 / 제외할 라벨**을 구조화된 형태(`@Generable`)로 추출
- **검색 기록 분석**: 검색 로그를 SwiftData에 저장하고 CSV로 내보내 실제 사용 패턴 분석
- **사진 뷰어**: 그리드에서 진입하는 전체화면 뷰어, 좌우 스와이프와 핀치 줌 지원
- Apple Intelligence를 쓸 수 없는 기기에서도 앱이 멈추지 않도록 사용 가능 여부에 따라 기능 분기

<details>
<summary><b>💡 트러블슈팅</b></summary>

<br>

**1. 한국어 검색어와 라벨이 맞지 않는 문제**
검색어를 영어로 단순 번역하면 "병"을 *bottle*이 아닌 *illness*로 옮기거나, *bottles*처럼 복수형이 나와 Vision이 붙인 라벨(*bottle*)과 일치하지 않았습니다. **현재 라이브러리에 실제로 존재하는 라벨 목록을 프롬프트로 함께 전달**해 그 안에서만 고르도록 제약을 걸어 해결했습니다.

**2. 기프티콘이 검색되지 않는 문제**
Vision의 범용 분류에는 "기프티콘"이라는 개념이 없어, 초콜릿 기프티콘은 초콜릿으로만 분류됐습니다. 모바일 쿠폰에는 거의 항상 바코드가 있다는 점에 착안해 **바코드 감지 결과로 `barcode` 라벨을 추가**하고, 기프티콘 검색 시 이 라벨을 사용하도록 했습니다.

**3. 라벨링 도중 앱이 멈추는 문제**
Vision의 분석 함수는 동기 블로킹 호출이라 Swift Concurrency의 협력 스레드풀에서 실행하면 풀 전체가 막힐 수 있었습니다. **분석은 별도 GCD 큐에서 실행**하고 TaskGroup으로 4초 타임아웃과 경합시켜, 한 장의 사진 때문에 전체 작업이 멈추지 않게 했습니다.

**4. 거의 모든 사진에 "document" 라벨이 붙는 문제**
실기기 테스트에서 넓은 범주의 라벨이 낮은 신뢰도로 자주 붙는 것을 확인하고, 신뢰도 임계값을 0.6으로 올려 잡음 라벨을 줄였습니다.

</details>

<br>

### 🔐 NIMDA — 정보보안 동아리 통합 웹 플랫폼

> 공주대학교 정보보안 동아리 NIMDA의 커뮤니티 · 운영 · 자체 CTF 대회(님다콘)를 하나로 묶은, **실제 운영 중인 서비스**

| 기간 | 역할 | 기술 | 링크 |
| :-- | :-- | :-- | :-- |
| 2025.09 ~ | 백엔드 · CI 기여 | Spring Boot · React · MySQL · Redis · Nginx · Docker · AWS S3 | [nimda.kr](https://nimda.kr) · [저장소](https://github.com/Nimda-Security/Nimda) |

<!-- TODO: 본인이 참여한 기간과 담당 기능을 확인해 '기간'·'역할' 칸과 아래 '내 기여'를 채워 주세요. -->

**서비스 소개**
- 게시판 · 댓글 · 알림 · 첨부파일, 출석 · 마일리지 · 배지 등 동아리 커뮤니티 기능
- JWT 인증과 역할 기반 권한(USER / ADMIN / DEV) 분리
- CTF 대회 플랫폼(문제 업로드 · 제출 · 스코어보드)
- Nginx 기반 Blue-Green 무중단 배포

**내 기여**
- 배포 파이프라인(GitHub Actions)에 **S3 연결 통합 테스트 단계 추가**: 배포 전에 S3 설정을 Spring이 정상적으로 읽는지, 실제 버킷에 접근 가능한지 검증해 잘못된 설정으로 배포되는 것을 방지

<br>

### 🦁 멋쟁이사자처럼 공주대 — 백엔드 트랙

> 객체지향 기초부터 Spring Boot REST API까지, 백엔드 트랙 과제를 수행하며 **PR 리뷰를 받고 반영**한 경험

| 기간 | 내용 | 저장소 |
| :-- | :-- | :-- |
| 2026.03 ~ 2026.05 | Java 객체지향 과제, E-commerce REST API | [E-commerce](https://github.com/potatostore/E-commerce) · [oop-practice](https://github.com/potatostore/oop-practice-likelion) |

- **E-commerce API**: 상품 · 장바구니 · 주문 CRUD, 결제 승인 토큰 처리, Swagger 문서화, 전역 예외 처리
- **리뷰 반영**: 계층 분리, Setter 사용 제한, 재고 수량 로직 수정, 예외 표준화 등 리뷰 내용을 정리해 반영
- **객체지향 과제**: `if-else`로 역할을 분기하던 코드를 상속과 오버라이딩을 이용한 **다형성 구조로 리팩터링**

<br>

<!-- ## 🏆 수상 & 활동

| 구분 | 내용 | 시기 |
| :-- | :-- | :-- |
| 🥇 수상 | 교내 SW 알고리즘 경진대회 **우수상** | 2026년 1학기 |
| ☁️ 교육 | AWS 클라우드 하계방학 특강 이수 | 2026년 여름 |
| 📘 교육 | 코드트리 KOEIC 과정 이수 | |
| ⚡ 활동 | 해커톤 참가 (2회) | | -->

<!-- TODO: 비어 있는 '시기' 칸을 채워 주세요. 해커톤은 대회 이름과 만든 서비스를 한 줄씩 적으면 더 좋습니다. -->

<br>

## 📂 그 밖의 저장소

- [**Obsidian**](https://github.com/potatostore/Obsidian) — 대학교때 배운, 프로젝트를 진행하면서 막힌 부분들을 해결하며 얻은 경험들을 문서화하여 저장하는 공간입니다.

<br>

## 📫 Contact

- GitHub: [@potatostore](https://github.com/potatostore)

<!-- TODO: 공개해도 괜찮은 연락 수단을 추가하세요. (이메일, 기술 블로그, 노션 이력서 등)
- Email: your@email.com
- Blog: https://...
-->
