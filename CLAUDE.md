# 한글 쓰기 (hangul)

유아·초등 1~2학년용. 손글씨·음성으로 한글을 입력하면 큰 글자와 자모 풀이를 보여 주고 따라 쓰게 한다.

## 구조
- `index.html` 하나(약 33KB). 3패널: 왼쪽 쓰기판(`#pad`, `#cv` 캔버스, 후보 `#cands`, 모드 `🧒 유아/🎒 1·2학년`), 가운데 큰 글자(`#big`·`#pic`·`#reading`·별 `#stars`, ⚙️ 설정), 오른쪽 풀이(`#info`).
- 필기 인식: 구글 입력도구 **비공식** 엔드포인트(`inputtools.google.com`, `itc=ko-t-i0-handwrit`). 갑자기 인식이 안 되면 이 엔드포인트부터 의심. 엔진의 첫 결과를 그대로 쓴다(가→가족 변환 문제로 수정됨). 인식 실패 안내가 끝나면 쓴 글씨를 자동으로 지운다(안내 중 새로 쓰면 유지).
- 음성 입력은 Web Speech API(크롬/엣지/삼성 인터넷/사파리).
- 쓰기판은 `touch-action:none` — 레이아웃을 바꾼 뒤 실제로 필기가 되는지(좌표 어긋남) 태블릿 크기에서 확인.
- 참고: 이 앱은 자체 Vercel 프로젝트(`hangul-xi-jet.vercel.app`)도 따로 있다(허브는 쓰지 않음).

## 공통 규칙 (AIEDU 허브)
- 이 앱은 **AIEDU 허브**(https://aiedu-pearl.vercel.app)의 서비스 중 하나다. 허브는 `arenarun23/aiedu` 저장소이고, 빌드 때 이 저장소를 가져와 `/hangul/` 하위 경로로 서비스한다. 허브 전체 구조·점검법은 그 저장소의 `.claude/skills/aiedu-hub`, 서비스별 설명은 `.claude/skills/aiedu-services` 에 있다(필요하면 `arenarun23/aiedu` 를 add_repo 로 붙여 읽는다).
- **정적 파일 + 상대경로만** 사용한다(`/foo.js` 같은 절대경로 금지). 서버·API 키 없음. 빌드가 필요한 구조로 바꾸지 않는다.
- **화면이 깨지지 않고 한 화면에 맞는 것**이 중요하다(주 사용 기기: 삼성 태블릿 가로·폰). 수정 후 1280×560, 1366×650, 800×1280, 390×780, 360×640, 800×360 크기에서 확인한다(허브 저장소 `scripts/fit-check.mjs` 참고). 아이용 큰 터치 버튼은 낮은 화면 미디어쿼리 안에서만 줄인다.
- `main` 에 push 하면 `.github/workflows/notify-hub.yml` 이 허브를 즉시 다시 배포한다(저장소 Secret `HUB_DEPLOY_HOOK` 사용, 20~60초). **Secret 값·훅 주소는 파일/커밋/로그에 쓰지 않는다.**
- push 전 `git fetch origin main` 으로 확인하고, 같은 앱을 다른 세션이 고치고 있었다면 rebase 해서 양쪽 기능을 모두 살린다.
- 반영이 안 보이면 코드보다 먼저 배포본을 비교한다: `curl -s https://aiedu-pearl.vercel.app/apps.json` 의 `hangul` 항목 `sha` 가 이 저장소 최신 커밋과 같은지.
- **AIEDU 복귀 아이콘**(`a.aiedu-home`, 웃는 별 44px): 모든 서비스가 같은 모양·같은 자리(페이지 왼쪽 위 8px, `<body>` 첫 요소, 링크 `../`)에 둔다. 지우거나 옮기지 않는다. 레이아웃을 바꿀 때 왼쪽 위 52×52px 를 비워 둔다 — 이 앱의 자리 확보: `.left h2{padding-left:52px}`, 좁은/낮은 화면에서는 `.left{padding-top:52px}`. 원본 코드: 허브 저장소 `templates/aiedu-home.html`.

- **외부 호스트 호출 금지(보안, CSP 가 브라우저에서 차단)**: 허용되는 외부 주소는 필기 인식 `inputtools.google.com` 하나뿐이다. 글꼴은 허브의 자체 호스팅 CSS 를 쓴다 — `<link rel="stylesheet" href="../fonts/jua.css">`(`jua`·`gowun-dodum`·`noto-sans-kr`·`noto-serif-kr`·`noto-serif-tc`). **Google Fonts 링크, CDN 이미지·스크립트를 넣지 않는다.** 이미지·데이터는 이 저장소 안에 둔다. 사용자·인식 결과를 HTML 에 넣을 땐 `esc()`/`textContent`, 속성은 큰따옴표. 자세한 규칙: 허브 저장소 `.claude/skills/aiedu-hub/references/security.md`.
