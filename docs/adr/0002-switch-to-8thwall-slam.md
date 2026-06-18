# MindAR → 8th Wall OSS(SLAM)로 추적 엔진 전환 (ADR 0001 대체)

실기기 테스트에서 MindAR(프레임별 이미지추적)은 카드를 움직이면 오버레이가 드리프트해 "따로 떠 있는" 느낌이었다. 레퍼런스(헬로우봇 `play.pookie.fan`)는 카드를 흔들어도 콘텐츠가 용접된 듯 붙어 추적됨 — 이는 **SLAM 보조 추적**의 특징이다. 이를 맞추기 위해 2026.2 MIT로 공개된 **8th Wall 오픈소스 엔진(SLAM + 이미지타깃)**으로 전환한다.

## 트레이드오프 / 메모
- 엔진: `@8thwall/engine-binary@1`(`dist/xr.js`)를 CDN로 로드. **전역 `window.THREE` 요구** → three 0.150대 주입(8th Wall 호환).
- 이미지타깃: `npx @8thwall/image-target-cli`(대화형)로 PNG → `.json`(+luminance PNG) 컴파일 → `image-targets/`에 배치, 런타임 `XR8.XrController.configure({imageTargetData:[json]})`.
- API: `XR8` 클래식 파이프라인(`GlTextureRenderer`/`Threejs`/`XrController`) + `reality.imagefound/updated/lost` 이벤트로 앵커 위치 갱신.
- `XRExtras`는 바이너리에 거의 없음 → 풀윈도 캔버스·카메라 권한·로딩 UI는 수동 처리.
- 제약: 직접 AR 테스트 불가(헤드리스) → 정렬/스케일/회전은 `index.html` 상단 상수(`PLANE_ASPECT`, `EXTRA_ROT_X`, `SCALE_MULT`)와 `↔/↕` 토글로 폰에서 보정.

## 상태
- **ADR 0001 (WebAR + MindAR) 대체(superseded).** WebAR·문양 마스크·글로우 셰이더 설계는 유지하되 추적 엔진만 교체.
