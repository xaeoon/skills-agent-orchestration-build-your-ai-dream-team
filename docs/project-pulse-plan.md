# Project Pulse 구현 계획

## 목표

Mona의 팀이 활성 프로젝트, 담당자, 현재 상태, 최근 활동, 우선순위 또는
위험 수준을 한눈에 확인할 수 있는 가벼운 정적 **Project Pulse** 대시보드를
구축한다. 결과물은 프로젝트 카드, 상태 배지, 우선순위 표현, 읽기 쉬운
간격과 반응형 레이아웃을 갖춘다.

## 에이전트 책임

- **Planner**: 저장소와 요구사항을 조사하고 구현 단계, dependencies,
  파일 소유권, 위험 요소, 병렬 처리 여부와 validation 기준을 정의한다.
- **Designer**: 정보 구조, 접근성, 반응형 동작, 시각적 계층을 설계한다.
  프로젝트 카드, 상태 배지, 우선순위 표현, 간격, 색상 대비와 타이포그래피를
  안내하며 `.dashboard`와 `.project-card` 같은 일관된 CSS 훅을 요구한다.
- **Coder**: Designer의 방향과 이 계획에 따라 정적 대시보드를 구현한다.
  HTML 구조와 데이터 렌더링, 스타일, 프로젝트 데이터를 작성하고 실행 가능한
  VS Code launch configuration을 추가한 뒤 동작을 검증한다.
- **Orchestrator**: Planner의 계획을 기준으로 작업을 배정하고, 파일 범위를
  분리하며, 통합 결과와 validation을 확인한다.

## 단계 및 파일 할당

### 1. 요구사항과 UI 방향 확정

- **담당**: Planner, Designer
- **파일 할당**: 계획 문서와 디자인 방향만 다룬다. 구현 파일은 이 단계에서
  수정하지 않는다.
- Project Pulse의 핵심 정보와 사용자 흐름을 확인한다.
- 카드에 표시할 `name`, `owner`, `status`, `recentActivity`, `priority`
  필드를 확정한다.
- 접근성 요구사항(시맨틱 구조, 명확한 레이블, 충분한 대비, 키보드 탐색)과
  반응형 레이아웃 기준을 정한다.

### 2. 데이터와 화면 구조 구현

- **담당**: Coder
- **파일 할당**:
  - `app/project-data.json`: 최상위 `projects` 배열과 각 프로젝트의
    `name`, `owner`, `status`, `recentActivity`, `priority` 데이터를 작성한다.
  - `app/index.html`: Project Pulse 제목, 대시보드 컨테이너, 프로젝트 카드
    마크업과 데이터 파일 로딩 및 필드 렌더링을 구현한다.
- HTML은 `styles.css`와 `project-data.json`을 참조해야 하며, 첫 화면이
  디렉터리 목록이 아니라 대시보드가 되도록 구성한다.

### 3. 시각 디자인과 실행 구성 구현

- **담당**: Designer가 디자인 기준을 제공하고 Coder가 구현한다.
- **파일 할당**:
  - `app/styles.css`: `.dashboard`, `.project-card`, 상태 및 우선순위
    표현, `border-radius`, `box-shadow`, 대비, 간격, 반응형 규칙을 작성한다.
  - `.vscode/launch.json`: `Run Project Pulse Dashboard` 구성으로 `app/`
    디렉터리를 작업 디렉터리로 사용하고 `index.html`을 열도록 작성한다.
- Coder는 `.vscode/launch.json`을 주석 없는 유효한 JSON으로 유지한다.

### 4. 통합 및 검증

- **담당**: Orchestrator와 Coder
- 네 파일의 참조 관계와 데이터 필드가 일치하는지 확인한다.
- 누락된 파일, 잘못된 경로, 깨진 JSON, 카드 렌더링 문제를 수정한다.

## Dependencies와 작업 순서

1. Planner의 요구사항 조사와 Designer의 기본 정보 구조 결정이 먼저
   완료되어야 Coder가 안정적으로 구현을 시작할 수 있다.
2. `app/project-data.json`의 스키마와 샘플 데이터는 `app/index.html`의
   렌더링 필드보다 먼저 확정되어야 한다.
3. `app/index.html`의 구조가 정해진 뒤 `app/styles.css`가 카드와 대시보드
   훅을 정확히 스타일링할 수 있다.
4. `app/index.html`과 `app/` 경로가 정해진 뒤 `.vscode/launch.json`의
   `cwd`와 `index.html` 대상 경로를 확정한다.
5. 모든 구현 파일이 준비된 뒤에만 통합 validation을 수행한다.

## Parallel work decisions

- Planner의 저장소 조사와 Designer의 독립적인 UX 검토는 구현 파일을
  수정하지 않으므로 **parallel**로 진행할 수 있다.
- Designer는 데이터 필드와 화면 구조가 정해진 뒤에도 Coder와 병렬로
  접근성 및 시각 기준을 검토할 수 있다. 단, 같은 파일을 동시에 수정하지
  않도록 Designer는 기준을 전달하고 Coder만 구현 파일을 편집한다.
- `app/project-data.json` 작성과 초기 `app/styles.css` 디자인 토큰 작성은
  서로 다른 파일이므로 병렬화할 수 있지만, HTML 필드와 최종 스타일 훅은
  스키마와 화면 구조 합의 후에 연결한다.
- `app/index.html` 구현과 `.vscode/launch.json` 작성은 파일이 겹치지 않아
  병렬 작업이 가능하다. 다만 launch 설정은 `app/`과 `index.html`의
  확정된 경로에 의존하므로 경로 결정 이후에 시작한다.
- 데이터 스키마 결정, HTML 렌더링 연결, 최종 통합 validation은 dependencies가
  있으므로 **sequential**하게 수행한다.

## Validation expectations

- `docs/project-pulse-plan.md`에 Project Pulse, Designer, Coder, 네 개의
  파일 할당, dependencies, parallel 작업 결정과 validation 기준이 명시되어
  있는지 확인한다.
- `app/index.html`이 `styles.css`와 `project-data.json`을 참조하고
  Project Pulse 제목, `.project-card`, `status`, `recentActivity`,
  `priority` 표시를 포함하는지 확인한다.
- `app/styles.css`에 `.dashboard`, `.project-card`, `border-radius`,
  `box-shadow`와 반응형 규칙이 있는지 확인한다.
- `app/project-data.json`이 유효한 JSON이며 최상위 `projects` 배열을
  포함하고 각 항목에 `name`, `owner`, `status`, `recentActivity`,
  `priority`가 있는지 확인한다.
- `.vscode/launch.json`이 유효한 JSON이며 `Run Project Pulse Dashboard`,
  `index.html`, `${workspaceFolder}/app`을 사용하고 디렉터리 목록 대신
  대시보드 파일을 열도록 설정되어 있는지 확인한다.
- 브라우저 또는 VS Code 실행 구성으로 대시보드를 열어 카드, 상태 배지,
  최근 활동, 우선순위가 실제로 표시되는지 확인한다.
- 저장소의 Step 2 계획 검증과 Step 3 산출물 검증이 요구하는 파일 존재,
  키워드, JSON 파싱 및 launch 설정 검사를 통과해야 한다.
