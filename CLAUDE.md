# LEAP Lab 연구실 홈페이지

Korea University Sejong Campus, LEAP Lab의 정적 웹사이트.
빌드 도구·의존성 없이 순수 HTML/CSS(+Bootstrap 5 CDN)로 구성되며,
GitHub Pages가 `main` 브랜치를 그대로 서빙한다.

- 배포 URL: https://codingchild2424.github.io/lab-website/
- 저장소: https://github.com/codingchild2424/lab-website
- `main`에 푸시하면 약 40~60초 후 자동 배포된다. (별도 CI/워크플로 없음)

## 파일 구조

| 경로 | 내용 |
|---|---|
| `index.html` | 홈. News 상위 3건 + 연구 주제 요약 |
| `news.html` | News 전체 (연도별 내림차순) |
| `publication.html` | 논문 목록 (연도별 내림차순) |
| `project.html` | 수행 과제 |
| `research.html` | 연구 주제별 소개 + 대표 논문 |
| `patent.html` / `course.html` / `apply.html` | 특허 / 강의 / 지원 안내 |
| `about/` | `introduction` `professor` `members` `photos` |
| `materials/portfolio/index.html` | 이웅기 교수 개인 CV 페이지 (별도 레이아웃) |
| `assets/` | CSS·이미지 |

## 논문/뉴스 추가 시 함께 고쳐야 하는 파일

논문 1건이 추가되면 **아래를 모두** 갱신해야 일관성이 유지된다.
과거에 `materials/portfolio/index.html`이 누락되어 여러 건이 밀린 적이 있으니 주의.

1. `publication.html` - 해당 연도 섹션 **맨 위**에 추가
2. `news.html` - 해당 연도 `news-list` **맨 위**에 추가
3. `index.html` - 아래 "홈 News 동기화" 참고
4. `materials/portfolio/index.html` - Publications의 해당 연도 블록 맨 위에 추가
5. (선택) `research.html` - 연구 주제의 대표 논문을 교체할 가치가 있으면

`project.html`의 섹션은 **Ongoing Projects / Completed Projects** 두 가지다.
아직 시작하지 않은 과제만 별도 표기하며, 수행 중인 과제는 Ongoing에 둔다.

### 홈 News 동기화

`index.html`의 `#home-news`는 페이지 로드 시 JS가 `news.html`을 fetch해
`.news-item` 상위 **3개**로 교체한다. HTML에 적힌 마크업은 fetch 실패 시의 fallback이다.
따라서 news 추가 시 fallback도 같이 손봐서 **항상 상위 3건**을 유지할 것
(1건 추가했으면 맨 아래 1건 제거).

## 표기 규칙

- 저자는 이니셜 축약: `Unggi Lee` → `U Lee`. 이웅기 교수는 `<b>U Lee</b>`로 굵게.
- 교신저자 표시: `publication.html`은 `<sup>&dagger;</sup>`, 포트폴리오는 `<b>U Lee†</b>`.
- 저자가 많으면 9명 내외에서 `...`로 생략 (Google Scholar 표기 관례).
- Venue 예시
  - `arXiv preprint arXiv:2602.10597`
  - `ACM International Conference on Information and Knowledge Management (CIKM)`
  - `International Conference on Intelligent Tutoring Systems, 259-273`
- 링크(arXiv/DOI)가 있으면 제목을 `<a ... target="_blank">`로 감싸고, 없으면 제목 텍스트만 둔다.
- News 문구: 프리프린트는 `New preprint: "제목"`, 학회 억셉은 `Paper accepted at CIKM 2026: "제목"`.
- 날짜 형식은 `2026.08` (연.월).
- 들여쓰기는 **탭**. 기존 파일의 탭 깊이를 그대로 맞출 것.

### 대시 표기 (중요)

- **em dash(`—`, `&mdash;`)와 en dash(`–`, `&ndash;`)를 쓰지 않는다.**
  구분자가 필요하면 일반 하이픈 `-` 하나만 쓴다.
- 이 규칙은 HTML 본문, 커밋 메시지, 문서 전부에 적용된다.
- 사용자와의 대화에서도 마찬가지로 `-`만 쓴다.
- 확인: `grep -rn '&mdash;\|&ndash;\|—\|–' . --include='*.html' --include='*.md'` 결과가 비어야 한다.
- 가운뎃점 `&middot;`는 항목 구분자로 계속 써도 된다 (대시가 아님).

## Project 페이지 규칙

- 제목 형식: `(기간) 과제명 (금액)` - 기간과 금액을 모두 제목 괄호에 넣는다.
  - 예: `(2026.07 - 2029.06) Basic Research Laboratory: ... (KRW 375,000,000 of KRW 1,500,000,000)`
  - 기간을 연도만 아는 경우 `(2026)`.
- 정렬은 **시작일 내림차순**. 섹션은 Ongoing / Completed.
- 회색 태그 박스(`research-tag`)를 쓰지 않는다. 부가 정보는 `<ul class="proj-meta">` 불렛으로.
- 불렛에는 **우측 로고와 중복되는 지원기관명을 넣지 않는다.** 파트너 기관, 지분, 총액 같은 추가 정보만.
- 카드 구조는 `<div class="research-card proj">` > `.proj-body` + `.proj-mark`.
- 페이지 상단에 `Funding in <올해>` 와 `Cumulative` 두 금액을 함께 적는다.

### 공동과제 금액 처리

여러 PI가 함께 수행하는 과제는 **총액과 본 연구실 지분을 모두 표기**하고,
상단 누적/올해 합계에는 **지분만** 더한다.
예) NRF 기초연구실: 총 15억(연 5억), 4인 공동이므로 지분 3.75억(연 1.25억) -> 누적에는 3.75억 반영.

## 로고 (assets/images/logos/)

- 각 과제 우측 `.proj-mark`에는 **텍스트가 아니라 이미지**를 넣는다.
- 자금 출처뿐 아니라 **협력 기관 로고도 함께** 넣는다 (여러 장이면 세로로 쌓임).
- 해당 기관 로고가 없으면 고려대 마크로 대체한다.
- **자유 라이선스(Public domain / CC)나 기관 공식 배포 파일만** 쓴다. Wikimedia Commons를 먼저 확인.
- 흰 배경에서 보이는 버전을 쓸 것. 흰색 단색 로고(fill 전부 #FFFFFF)는 흰 바탕에서 안 보인다.
- 가로로 긴 로고는 `max-width: 130px`에서 읽을 수 없게 되니 마크/심볼 버전을 우선한다.
- 봇 차단(Cloudflare 등)이 걸린 사이트는 우회하지 않는다. 사용자에게 파일을 요청할 것.

## 사진 (assets/images/photos/)

- EXIF로 촬영일시를 확인해 캡션 날짜를 채운다 (파일 수정일 말고).
- 긴 변 1200px로 리사이즈, JPEG quality 85로 저장.
- 마크업은 `<figure class="photo-item">` + `<img>` + `<figcaption>`.
- 캡션 형식: `설명, YYYY.MM`.
- **게시 전 반드시 사진을 직접 확인한다.** 여러 장이면 PIL로 컨택트시트를 만들어 한 번에 본다.
- 사진 속 인물을 추측해 이름을 붙이지 않는다.

## 인증 정보

`gh` CLI용 GitHub 토큰은 `.env`에 있으며 `.gitignore`로 추적 제외된다.
템플릿은 `.env.example` 참고.

```bash
set -a; . ./.env; set +a
gh api repos/codingchild2424/lab-website/pages/builds/latest --jq '.status + " " + .commit'
```

## 배포 확인

푸시 후 Pages 빌드 상태와 실제 배포본을 함께 확인한다.

```bash
# 빌드 상태 (status가 built, commit이 HEAD와 일치해야 함)
gh api repos/codingchild2424/lab-website/pages/builds/latest --jq '{status, commit}'

# 라이브 HTML과 로컬 파일 비교
curl -sSL https://codingchild2424.github.io/lab-website/news.html -o /tmp/live.html
diff news.html /tmp/live.html && echo "동일"
```
