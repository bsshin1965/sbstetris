name: sbstetris
description: >
  HTML/CSS/JavaScript로 완성도 높은 테트리스 웹 게임을 제작하는 스킬입니다.
  PC(Windows)와 Android 스마트폰 양쪽에서 동작하는 반응형 웹으로 만들며,
  Web Audio API 사운드, 레벨/속도 시스템, T-스핀, 홀드, 콤보, 고스트 피스,
  스와이프 조작, 배경음악, 하이스코어 저장 등 풍부한 기능을 포함합니다.
  사용자가 "테트리스", "tetris", "블록 게임", "낙하 퍼즐 게임" 등을 요청하거나
  웹 게임 제작, HTML5 게임 제작을 요청할 때 이 스킬을 사용하세요.
  단일 HTML 파일로 완성되며 별도 라이브러리나 서버 없이 바로 실행 가능합니다.
---

# 테트리스 게임 제작 스킬

## 개요

이 스킬은 **순수 HTML + CSS + JavaScript** 로 작성된 완성형 테트리스 게임을 생성합니다.
별도 프레임워크나 서버 없이 브라우저에서 즉시 실행되며, PC와 모바일 모두 지원합니다.

---

## 아키텍처

tetris.html (단일 파일)
├── <style>          — 반응형 CSS, CSS 변수, 애니메이션
├── Canvas (board)   — 10×20 게임 보드 렌더링
├── Canvas (next)    — 다음 피스 미리보기
├── Canvas (hold)    — 홀드 피스 미리보기
└── <script>
    ├── Audio Engine     — Web Audio API 사운드 & BGM
    ├── Piece Bag        — 7-bag 랜덤화
    ├── Board Operations — 충돌 감지, 라인 클리어
    ├── SRS Rotation     — 슈퍼 로테이션 시스템 + 월킥
    ├── Lock Delay       — 잠금 지연 (최대 15회 이동)
    ├── Gravity Loop     — rAF 기반 게임 루프
    ├── Renderer         — 셀/고스트/미리보기 렌더링
    ├── Input Handler    — 키보드 + 터치/스와이프
    └── UI Manager       — 점수/레벨/콤보 표시

---

## 핵심 기능 목록

### 게임플레이 기능
| 기능 | 설명 |
|------|------|
| 7-bag 랜덤화 | 7개 피스를 균등하게 섞어 배분 |
| 고스트 피스 | 착지 위치 반투명 미리보기 |
| 홀드 시스템 | C키 또는 📦 버튼으로 피스 보관 |
| 하드 드롭 | Space/스와이프 다운으로 즉시 착지 |
| 소프트 드롭 | ↓키/버튼 누르는 동안 빠른 낙하 |
| SRS 로테이션 | 슈퍼 로테이션 시스템 + 월킥 완전 구현 |
| T-스핀 감지 | T-스핀 보너스 점수 및 카운터 |
| 콤보 시스템 | 연속 라인 클리어 보너스 |
| 락 딜레이 | 바닥 착지 후 이동/회전 가능 (최대 15회) |

### 레벨 & 속도 시스템
- 매 10라인마다 레벨업
- 레벨 1: 800ms/drop → 레벨 20: 33ms/drop (최고속)
- 소프트 드롭 시 속도 10배 가속
- 레벨업 시 사운드 + 화면 플래시 이펙트

### 점수 시스템
Single : 100 × level
Double : 300 × level
Triple : 500 × level
Tetris : 800 × level
T-Spin : 기본 점수 × 2
콤보   : 50 × combo × level (누적)
하드드롭: +2점/셀, 소프트드롭: +1점/셀

### 사운드 (Web Audio API — 외부 파일 없음)
| 이벤트 | 사운드 |
|--------|--------|
| 이동 | 짧은 square wave |
| 회전 | 중간 square wave |
| 하드 드롭 | sine wave 충격음 |
| 피스 잠금 | sawtooth 쿵 |
| 홀드 | triangle wave |
| 라인 클리어 | 라인 수에 따라 화음 |
| 테트리스 | 4음 상승 멜로디 |
| T-스핀 | 3음 triangle |
| 레벨업 | 5음 팡파레 |
| 게임오버 | 4음 하강 |
| 배경음악 | 200ms 간격 triangle arpeggio |

### 반응형 & 모바일
- PC (>900px): CELL=30px, 좌우 패널 레이아웃
- 태블릿 (601~900px): CELL=26px
- 스마트폰 (≤600px): CELL=22px, 패널 수평 배치 + 터치 컨트롤
- 터치 제스처: 탭=회전, 더블탭=하드드롭, 좌우스와이프=이동, 아래스와이프=하드드롭
- 6버튼 모바일 패드 (홀드/회전/하드드롭/←/▼/→)

---

## 제작 절차

### 1단계 — 사용자 요구사항 파악
스킬 발동 시 아래를 확인하세요:
- 추가 피스 타입 요청 여부 (기본: I, O, T, S, Z, J, L)
- 특별 게임 모드 (타임 어택, 마라톤, 스프린트)
- 커스텀 색상 테마
- 멀티플레이어 여부 (이 스킬은 싱글만 지원)

### 2단계 — 에셋 파일 복사 또는 새 작성
# 기존 에셋 사용 (이 SKILL 폴더에 포함)
cp /mnt/skills/user/tetris-game/assets/tetris.html /mnt/user-data/outputs/tetris.html

요구사항에 커스터마이징이 필요하면 아래 섹션을 수정하세요.

### 3단계 — 커스터마이징 포인트

색상 테마 변경 (--accent, --accent2 CSS 변수):
:root {
  --accent: #00f5ff;  /* 포인트 색상 */
  --accent2: #ff006e; /* 보조 색상 */
  --bg: #0a0a0f;      /* 배경 */
}

속도 조정 (LEVEL_SPEEDS 배열, ms 단위):
const LEVEL_SPEEDS = [800,717,633,...,33]; // 레벨 1~20

보드 크기 변경:
const COLS = 10, ROWS = 20; // 표준 테트리스

BGM 멜로디 변경 (bgNotes 배열, MIDI 주파수):
const bgNotes = [262,330,392,...]; // Hz 단위

### 4단계 — 추가 기능 구현 가이드

#### 게임 모드 추가
const MODES = {
  marathon: { maxLines: Infinity, goal: 'score' },
  sprint:   { maxLines: 40,       goal: 'time' },
  ultra:    { maxLines: Infinity,  timeLimit: 120 }
};

#### 통계 화면 (엔드게임)
게임오버 오버레이에서 이미 표시:
- 최종 점수 / 하이스코어
- 라인 수 / 레벨
- T-스핀 횟수 / 테트리스 횟수

#### 로컬 랭킹
// localStorage로 상위 10개 기록 저장
function saveRanking(score, lines, level) {
  const ranks = JSON.parse(localStorage.getItem('tetris_ranks')||'[]');
  ranks.push({score, lines, level, date: new Date().toLocaleDateString()});
  ranks.sort((a,b) => b.score - a.score);
  localStorage.setItem('tetris_ranks', JSON.stringify(ranks.slice(0,10)));
}

---

## 제공 추가 기능 (기본 포함)

1. 하이스코어 자동 저장 — localStorage 영구 보존
2. 새 기록 알림 — 깜빡이는 황금색 BEST 텍스트
3. 레벨 진행 바 — 다음 레벨까지 남은 라인 시각화
4. T-스핀 / 테트리스 카운터 — 우측 패널
5. 콤보 팝업 애니메이션 — 보드 중앙 플로팅 텍스트
6. 레벨업 플래시 — 보드 테두리 황금 글로우
7. 스캔라인 오버레이 — 레트로 CRT 효과
8. 애니메이션 그리드 배경 — 사이버펑크 분위기
9. 속도 별점 표시 — ★☆☆☆☆ ~ ★★★★★

---

## 제안 가능한 추가 기능

사용자가 요청할 경우 아래 기능을 추가 구현하세요:

| 기능 | 구현 난이도 | 설명 |
|------|------------|------|
| 로컬 랭킹 보드 | 쉬움 | localStorage 상위 10개 기록 |
| 타임 어택 모드 | 보통 | 제한 시간 카운트다운 |
| 스프린트 모드 | 보통 | 40라인 최단시간 클리어 |
| 다음 피스 3개 표시 | 쉬움 | nextBag[0~2] 미리보기 |
| 퍼펙트 클리어 보너스 | 보통 | 빈 보드 클리어 시 초고점수 |
| 배경 테마 선택 | 쉬움 | CSS 변수 세트 교체 |
| 키 리매핑 UI | 어려움 | 설정 화면에서 키 재설정 |
| 멀티플레이어 (대결) | 매우 어려움 | WebSocket 필요 |
| AI 데모 모드 | 어려움 | 자동 플레이 봇 |
| 피스 통계 차트 | 보통 | 각 피스 사용 횟수 바 차트 |

---

## 파일 구조

tetris-game/
├── SKILL.md           ← 이 파일
└── assets/
    └── tetris.html    ← 완성된 게임 (단일 파일)

---

## 출력

- 완성된 tetris.html 을 /mnt/user-data/outputs/tetris.html 에 복사
- present_files 도구로 사용자에게 전달
- 브라우저에서 직접 열면 즉시 실행

---

## 주의사항

- Web Audio API는 사용자 인터랙션(클릭/터치) 이후에만 활성화됨 (브라우저 정책)
- iOS Safari는 touchstart 이벤트에 passive:false 필수
- localStorage가 없는 환경(프라이빗 브라우징)에서는 하이스코어 저장 불가 — try/catch로 처리됨
- Canvas 크기는 resize 이벤트마다 재계산됨