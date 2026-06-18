# 문양 리빌 — NFC × WebAR 프로토타입

NFC 태그로 여는 WebAR 데모. 카드(타깃)를 카메라로 비추면 **문양 위에 빛이 퍼지는 이펙트**가 정렬돼 재생됩니다. NFC는 "이 URL을 여는 실행 트리거"일 뿐, 마법은 카메라 + 이미지추적(MindAR) + 글로우 셰이더(three.js)에서 납니다.

## 스택
- **WebAR** (앱 설치 0, 모바일 브라우저)
- **MindAR 1.2.5** (이미지추적) + **three.js 0.160** (글로우 셰이더)
- 기본 타깃: 번들된 샘플 문양 `assets/munyang.png` (이미 `assets/targets.mind`로 컴파일 완료) — *추후 실제 브랜드 문양으로 교체*
- 결정 근거 → `docs/adr/0001-webar-mindar-stack.md` · 용어 → `CONTEXT.md`

## 30초 테스트
> 카메라는 **HTTPS(보안 컨텍스트)** 에서만 열립니다. 폰 테스트엔 공개 HTTPS URL이 필요합니다.

**방법 A — 드래그 배포(가장 쉬움)**
1. https://app.netlify.com/drop 접속
2. 이 폴더(`nfc-test`)를 드롭 → 즉시 `https://...` URL 발급

**방법 B — Vercel**
- 이 폴더에서 `npx vercel` → https URL

**방법 C — 로컬 + 터널**
- `npx serve .` (또는 `python3 -m http.server 8000`)
- 다른 터미널: `npx cloudflared tunnel --url http://localhost:3000` (또는 `ngrok http 3000`) → https URL

**그다음**
1. 인트로의 **① 타깃 이미지 열기** 링크를 노트북/모니터에 띄우거나 인쇄
2. 폰 브라우저로 발급된 URL 접속 → **② 카메라 시작** → 권한 허용
3. 카드(타깃)를 비추면 문양 위로 빛이 퍼짐. 하단에서 이펙트(방사·스윕·파티클·펄스)·색 전환, `↻ 다시`로 재생.

## NFC 태그로 실행하기(선택)
1. **NTAG213** 스티커/카드 준비
2. 폰에 **NFC Tools** 앱 설치 → Write → Add record → URL/URI → 위 https URL 입력 → Write
3. 폰을 태그에 대면 브라우저가 URL을 열어 데모 실행

**iOS 주의**
- 카메라가 켜져 있으면 NFC 백그라운드 읽기가 막힘 → 반드시 **"태그 먼저 → 그다음 카메라 시작"** 순서. AR 도중 재태그 불가.
- URL이 유니버설 링크가 아니면 태그 시 `"Website NFC Tag"` 배너를 한 번 탭해야 함(프로토타입 정상 동작).

## 효과 방식 (현재)
빛은 **타깃의 인쇄 문양(잉크/컬러 마크)을 마스크로 삼아 그 위에서만** 퍼집니다 — 밝은 배경엔 안 들어갑니다. 하단 **↔ 좌우 / ↕ 상하** 토글로 마스크 정렬(거울/뒤집힘)을 재배포 없이 즉석 보정합니다.

## 다음 단계: 우리 문양으로 교체
1. 추적 잘되는 문양 디자인 — **특징점 풍부·고대비·무광·비대칭**(`CONTEXT.md` 제약)
2. MindAR 온라인 컴파일러로 `.mind` 생성 → https://hiukim.github.io/mind-ar-js-doc/tools/compile
3. `index.html`에서 `TARGET_SRC`(.mind), `TEX_SRC`(문양 PNG), `TARGET_ASPECT`(height/width) 교체
4. `↔/↕`로 정렬 맞추고, 필요시 마스크 임계값(`darkInk`/`sat`)을 문양에 맞게 튜닝
