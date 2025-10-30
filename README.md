# nvr-lite
C/C++ 20, openCV, live555, FFmpeg, wxWidgets을 사용하여 RTSP 파이프라인을 직접 구현해보는 미니 프로젝트.

# Level 1 - 최소 요구사항
1. RTSP 프로토콜을 사용하는 IP 카메라 1개 주소를 PC 실행 프로그램에서 설정해서 연결할 수 있다.
2. 카메라를 PC에 연결하고, 프로그램에서 실시간 영상을 송출한다.
3. 실시간 영상에 대한 로그가 파일에 별도로 기록된다.
4. 카메라와의 연결이 끊기면 프로그램에서 Alert 창이 팝업되고, 영상 스트리밍이 종료된다.
5. **프로그램을 하루 종일 실행했을 때도 영상 송출에 문제가 없어야 한다.**

# Level 2 - 추가 기능
1. 실시간 영상을 녹화하여 PC에 저장할 수 있다.
2. 녹화된 영상에 대한 재생, 정지, 되감기, 빨리감기(1배~10배 슬라이드 UI로 제공) 기능을 제공한다.

# Level 3 - 추가 기능
1. 실시간 영상에 대한 로그를 프로그램 안에서 표 형식의 UI로 확인할 수 있다.
2. 로그 파일을 CSV 형식으로 내보내서 HDD 또는 USB에 저장할 수 있다.

# Level 4 - 추가 기능
1. 최대 4개까지 카메라 주소를 설정하고, 연결할 수 있다.
2. 채널 뷰 UI는 2X2 그리드로 배치한다.
3. 실시간 스트리밍 중인 각 채널 영역을 클릭하면 전체화면으로 볼 수 있고, 다시 클릭하면 2X2그리드 상태로 돌아온다.

# Level 5 - 기능 개선
1. 이 프로그램이 Clash될 때까지 계속 켜놓으면서 성능을 측정하고, 프로그램이 죽었을 때 왜 죽었는지 원인을 파악한다.
2. 성능 개선을 목표로 코드를 리팩토링한다.

# 개발 규칙
1. SOLID 원칙을 따를 것.
2. TDD 방법론을 따를 것.
3. **기존 NVR 소스코드를 참고하고, 동일한 기술스택을 사용할 것.**
4. AI를 사용하더라도, 자기가 짠 코드를 설명할 수 있어야 한다. 예를 들어, 누가 나한테 아래와 같이 질문했을 때 대답할 수 있어야 함:
   - **왜 이렇게 만들었나요?**
   - **왜 이 기술/라이브러리를 사용했나요?**
   - **왜 이런 방식으로 구현했나요? 다른 방법은 없었나요?**
   - **이 영역을 눌렀을때, 코드 내부 동작방식이 어떻게 되나요?**

## TDD 사이클
**테스트 프레임워크는 GoogleTest (vcpkg gtest)를 사용.**

1. 테스트 먼저 작성: 요구사항 단위(예: “연결 끊김 3초 내 재연결 시도”).
2. 실패하는 테스트 확인.
3. 테스트를 통과할 만큼만 최소 구현.
4. 위 과정을 반복.

# 협업 규칙
## 리포지토리 구조
```
nvr-lite/
 ├─ include/                  # .hpp 파일(index 역할), .h파일
 ├─ src/                      # .cpp 파일
 ├─ tests/                    # TDD 단위/통합 테스트
 ├─ .clang-format.
 ├─ .clang-tidy
 └─ README.md
```
## 브랜치 전략
- 기본 브랜치: main
- 개인 브랜치: 작업자 이름에서 각자 작업
- PR 규칙: 개인 브랜치 → main 으로 PR 생성
   1. 코드리뷰 1명 이상 승인 필수
   2. CI 체크 전부 통과 필수: 빌드/포맷(clang-format)/린트(clang-tidy)/테스트
- 직접 푸시 금지: main 에 직접 푸시 금지
- 작은 단위 머지 권장: 기능이 크면 작은 PR로 쪼개어 자주 머지

## 커밋 컨벤션
| type       | 언제 쓰나                          | 예시                                                   |
| ---------- | ------------------------------------ | ---------------------------------------------------- |
| **feat**   | 새 기능 추가               | feat: rtsp client 구현       |
| **fix**    | 버그 수정              | fix: decoder 클래스 댕글링포인터 해결 |
| **refact** | 코드 구조 개선/리팩토링           | refact: render 시스템 공통 인터페이스 추가해서 의존성 분리         |
| **test**   | 테스트                        | test: rtsp 연결 끊김 3초 내 재연결 시도하는 테스트 추가       |
| **docs**   | 문서/주석/README                  | docs: main_app에 doxygen 기반 주석 작성       |
| **chore**  | 그 외 나머지 | chore: 파일명 변경             |

## 네이밍 규칙
### 공통
- 파일명: snake_case
- 클래스: camelCase
- 인터페이스: I 접두사 + PascalCase (예: IDecoder, IRtspClient)
- 함수/메서드: snake_case
- 상수: SCREAMING_SNAKE_CASE
- 열거형/멤버: snake_case
- 네임스페이스: 기능 단위를 표현하는 단어 하나로 표현. (shf, nvr, rtsp, decode, render, storage, ui, vm, model, core 등)
- 헤더: nvr-lite의 메인 헤더파일은 hpp, 나머지는 h를 사용.
- 엔트리포인트: app_main.cpp
- 단위 테스트: *_test.cpp

### 파일명
```
<feature>_<entity>_<role>.<hpp|cpp>
```
- <feature>: 기능(ex: login, channel, rtsp, decode, render, storage, search…)
- <entity>: 객체(ex: client, manager, scheduler, context, frame…)
- <role>: ROLE 접미사(ex: view, vm, model, manager, coord, sink, service, work, config, util, log...)

## Lint/Formatting 
[Modern C++ Coding Guideline](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines)을 준수할 것.
```
// .clang-tidy
Checks: >
  clang-diagnostic-*,
  bugprone-*,
  performance-*,
  readability-*,
  modernize-*,
  cppcoreguidelines-*,
  hicpp-*,
  portability-*,
  misc-*
WarningsAsErrors: 'clang-diagnostic-*,bugprone-*,performance-*,modernize-*'
HeaderFilterRegex: '^(src|include)/'
AnalyzeTemporaryDtors: true
FormatStyle: none
```

```
// .clang-format (아직 작성 중)
BasedOnStyle: LLVM
IndentWidth: 4
ColumnLimit: 120
PointerAlignment: Left
SortIncludes: true
AllowShortFunctionsOnASingleLine: Empty
```
