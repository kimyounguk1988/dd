# dd — 섬 산책 놀이터 (villager-viewer)

동물의 숲 느낌의 3D 섬에서 캐릭터를 걷게 하고, 키보드 악기로 리듬게임·자유 연주를 하는 단일 HTML 웹앱.
작업자: 김영욱 (SKYTJ). 답변은 한국어 반말, 결론부터.

## 구조
- `villager-viewer/index.html` — 앱 전체 (HTML+CSS+JS 한 파일, 빌드 없음)
  - Three.js 0.160 (jsdelivr importmap), JSZip 3.10.1 (cdnjs)
  - 3D: 섬/나무/샘플 주민(프리미티브), 모델 로더(.dae/.fbx/.glb/.gltf/.zip)
  - 리그: 동숲 계열 뼈 이름(Leg_1_L, Arm_1_R, TailRoot…)을 찾아 코드로 걷기·점프·손흔들기·연주 포즈. T-pose 기준이라 팔을 ARM_DOWN(1.25rad)만큼 내려서 씀
  - 연주 모드: 12키(1옥타브) 리듬게임. 채보 노트 = `{t, lane, midi}`, lane = 음이름(midi % 12), 소리는 midi로 재생
  - 곡: 캐논(내장, 쉬움/보통/어려움) + MIDI 파일 임포트(멜로디 트랙 자동 선택, 난이도별 노트 간격으로 솎고 나머지는 반주)
  - 연습 모드: 채보 없이 자유 연주, Z/X 옥타브, Space 페달
  - 오디오: 샘플러(단3도 간격 샘플을 피치시프트), 손가락/키마다 voice → 화음, 컴프레서+리버브
- `villager-viewer/samples/salamander/` — 그랜드 피아노 C2–C7 (CC BY 3.0, LICENSE.txt)
- `villager-viewer/samples/fluidr3/<악기>/` — 오르골·첼레스타·비브라폰·마림바·칼림바·나일론기타·바이올린·플루트 (CC BY 3.0)
- `villager-viewer/model/` — (git 제외) 시작 시 자동으로 띄울 모델. `manifest.json` 형식:
  `{"files": ["NpcNmlSqu19.dae", "mBody_Alb.png", ...]}` 또는 확장자 바꾼 항목은 `{"path": "x.xml", "name": "x.dae"}`

## 실행
file://로 열면 샘플·모델 fetch가 막혀서 합성음만 남. 로컬 서버로 열 것:
```
cd villager-viewer && python3 -m http.server 8000
# 브라우저: http://localhost:8000
```

## 주의
- 동숲 원본 모델(닌텐도 저작권)은 레포에 커밋하지 않음. `model/`은 .gitignore 처리. 개인용으로만.
- claude.ai 아티팩트 배포본(https://claude.ai/artifact/X7nyRQxDfyj5PWieuWE8sr)은 CSP 때문에 `fetch(blob:)`가 막힘 → 모델은 `loader.parse()`로 메모리에서 파싱함. 이 방식 유지.
- 아티팩트는 .dae를 못 올려서 `.xml`로 올리고 manifest의 name으로 원래 이름 복원.
- 테스트는 Playwright + 헤드리스 Chromium(swiftshader)으로 스크린샷/판정 확인해왔음. 소리는 귀로 검증 못 했음.

## 남은 아이디어 / TODO
- 연주 중 손이 큰 머리에 가려 잘 안 보임 (카메라 각도·키보드 위치 조정 여지)
- 옷 변형은 같은 텍스처라 모양(소매·하의)만 다름. 다른 옷 텍스처 받으면 갈아입히기
- 실제 복잡한 MIDI(예: 게임 OST)로 채보 품질 검증 필요
