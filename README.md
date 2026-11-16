# 마플 숏츠 구좌 프로토타입

Figma 최종 시안(「[대표님 피드백 반영] 디자인 3차」)을 동작하는 HTML 프로토타입으로 옮긴 것입니다. 단일 `index.html` + `assets/`로 구성되며 빌드 과정이 없습니다.

## 실행

```bash
node server.js          # http://localhost:5173
```

유튜브 임베드는 `file://`로 열면 재생이 막힐 수 있어 로컬 서버나 호스팅(GitHub Pages 등)에서 확인합니다.

## 화면

| 해시 | 화면 | PC | MO (768px 미만) |
|---|---|---|---|
| `#main` | 메인 페이지 | 정적 캡처 + 「숏츠로 만나는 마플 굿즈」 구좌만 동작 | 가로 스크롤 카드 |
| `#list` | 숏츠 목록 | 태그 필터, 5열 그리드, **썸네일 호버 시 영상 중간 4초 무음 루프** | 2열 그리드 |
| `#viewer/<youtubeId>` | 숏츠 뷰어 | 목록 위 모달. 휠·방향키·우측 버튼으로 **스크롤 스냅 이동**, 우측 상품 패널 | 전체 화면. 위아래 스와이프, 코치마크(세션당 1회), 하단 상품 스트립 → 바텀시트 |

- 영상 속 상품은 **0개 또는 1개**입니다(`VIDEOS[].product`가 `null`이면 "영상 속 상품" 블록이 빠지고 인기 상품 목록만 노출).
- 영상은 마플 공식 유튜브 쇼츠 채널의 영상 ID를 사용하며, YouTube IFrame Player API로 재생합니다.
- 상품 이미지·아이콘·로고는 Figma 시안에서 추출했습니다. 폰트는 Pretendard(CDN).

## 구현 메모 (개발 참고)

- 호버 미리보기: `mouseenter` 후 300ms 뒤 `YT.Player`를 생성해 `mute` → `seekTo(duration/2 - 2)` → 200ms 간격으로 `currentTime > mid + 4`이면 다시 `seekTo`. 카드를 벗어나면 플레이어를 파괴합니다. 썸네일 위에는 클릭을 받는 투명 레이어를 두어 유튜브 UI가 클릭을 가로채지 않게 합니다.
- 뷰어 스크롤: 세로 컨테이너에 `scroll-snap-type: y mandatory`, 각 슬라이드 `scroll-snap-stop: always`. `IntersectionObserver`(threshold 0.6)로 활성 슬라이드를 판정해 그 슬라이드에만 플레이어를 마운트하고, 패널과 URL 해시를 갱신합니다.
- 뷰어 재생: `autoplay: 1, controls: 1, playsinline: 1`. 카드 클릭이라는 사용자 제스처가 있어 소리 있는 자동재생이 허용됩니다.
