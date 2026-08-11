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

1. `publication.html` — 해당 연도 섹션 **맨 위**에 추가
2. `news.html` — 해당 연도 `news-list` **맨 위**에 추가
3. `index.html` — 아래 "홈 News 동기화" 참고
4. `materials/portfolio/index.html` — Publications의 해당 연도 블록 맨 위에 추가
5. (선택) `research.html` — 연구 주제의 대표 논문을 교체할 가치가 있으면

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
