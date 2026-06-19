# MindAR → 8th Wall OSS(SLAM)로 추적 엔진 전환 (ADR 0001 대체)

실기기 테스트에서 MindAR(프레임별 이미지추적)은 카드를 움직이면 오버레이가 드리프트해 "따로 떠 있는" 느낌이었다. 레퍼런스(헬로우봇 `play.pookie.fan`)는 카드를 흔들어도 콘텐츠가 용접된 듯 붙어 추적됨 — 이는 **SLAM 보조 추적**의 특징이다. 이를 맞추기 위해 2026.2 MIT로 공개된 **8th Wall 오픈소스 엔진(SLAM + 이미지타깃)**으로 전환한다.

## 트레이드오프 / 메모
- 엔진: `@8thwall/engine-binary@1`(`dist/xr.js`)를 CDN로 로드. **전역 `window.THREE` 요구** → three 0.150대 주입(8th Wall 호환).
- 이미지타깃: `npx @8thwall/image-target-cli`(대화형)로 PNG → `.json`(+luminance PNG) 컴파일 → `image-targets/`에 배치, 런타임 `XR8.XrController.configure({imageTargetData:[json]})`.
- API: `XR8` 클래식 파이프라인(`GlTextureRenderer`/`Threejs`/`XrController`) + `reality.imagefound/updated/lost` 이벤트로 앵커 위치 갱신.
- `XRExtras`는 바이너리에 거의 없음 → 풀윈도 캔버스·카메라 권한·로딩 UI는 수동 처리.
- 제약: 직접 AR 테스트 불가(헤드리스) → 정렬/스케일/회전은 `index.html` 상단 상수(`PLANE_ASPECT`, `EXTRA_ROT_X`, `SCALE_MULT`)와 `↔/↕` 토글로 폰에서 보정.

## 고려한 대안 (2026.6 웹리서치 결론)
"특정 카드 문양에 빛을 용접"이라는 목표 기준 비교:
- **MindAR(2.7k★)**: 이미지타깃 O, SLAM X → 프레임별 추적이라 흔들면 드리프트. (떠나온 엔진)
- **AR.js(5.9k★)**: 이미지타깃(NFT) O, SLAM X → MindAR과 유사한 지터 한계.
- **AlvaAR(492★)**: 100% OSS SLAM이나 **마커리스 월드추적 전용 — 특정 이미지 인식 불가.** 카드를 못 집어내 별도 검출 레이어를 직접 얹어야 함.
- **8th Wall engine-binary(채택)**: 이미지타깃 + (바이너리에 컴파일된)독점 SLAM을 한 번에 제공, 무료·앱키 불필요. 목표에 유일하게 직접 부합.

주의: MIT 오픈소스 *소스*(`8thwall/8thwall`)엔 SLAM이 빠져 있으나, 우리가 쓰는 *배포 바이너리*(`@8thwall/engine-binary`)는 `xr-slam.js` 청크에 SLAM을 포함 → `<script ... data-preload-chunks="slam">`로 로드해야 `XR8.XrController`가 채워짐.

## 상태
- **ADR 0001 (WebAR + MindAR) 대체(superseded).** WebAR·문양 마스크·글로우 셰이더 설계는 유지하되 추적 엔진만 교체.
