---
name: lab-site
description: LEAP Lab 연구실 홈페이지(lab-website)에 논문·뉴스·과제·멤버·사진을 추가하거나 수정할 때 사용한다. "논문 추가", "publication 업데이트", "news 추가", "과제 추가", "연구비 반영", "멤버 추가/제외", "연구실 사진 올려" 같은 요청에 해당한다. 여러 파일을 동시에 갱신해야 일관성이 유지되므로 반드시 이 스킬의 체크리스트를 따른다.
---

# LEAP Lab 홈페이지 갱신

빌드 도구 없는 정적 사이트. `main`에 푸시하면 GitHub Pages가 40~60초 후 자동 배포한다.
상세 배경은 저장소 루트의 `CLAUDE.md`를 함께 읽을 것.

## 공통 규칙

- **대시 금지**: `—`(em dash), `–`(en dash)를 쓰지 않는다. 필요하면 하이픈 `-` 하나만.
  검증: `grep -rn '&mdash;\|&ndash;\|—\|–' . --include='*.html' --include='*.md'` 가 비어야 한다.
- 들여쓰기는 **탭**. 기존 파일의 탭 깊이를 그대로 맞춘다.
- 편집 후 항상 태그 밸런스를 검사한다 (아래 스니펫).
- 저자는 이니셜 축약, 이웅기 교수는 `<b>U Lee</b>`. 교신저자는 `<sup>&dagger;</sup>`.

## 논문 1건 추가 시 (4개 파일 전부)

1. `publication.html` - 해당 연도 섹션 맨 위
2. `news.html` - 해당 연도 `news-list` 맨 위
3. `index.html` - 정적 fallback 맨 위에 추가하고 **맨 아래 1건 제거**(항상 3건 유지).
   홈은 JS가 `news.html`에서 상위 3건을 가져오고, HTML 마크업은 fetch 실패 시 fallback이다.
4. `materials/portfolio/index.html` - Publications의 해당 연도 블록 맨 위

논문 정보는 추측하지 말고 arXiv abs 페이지의 `citation_*` 메타태그에서 확인한다.

```bash
curl -sSL -A "Mozilla/5.0" "https://arxiv.org/abs/XXXX.XXXXX" -o /tmp/a.html
python3 -c "
import re,html
s=open('/tmp/a.html',encoding='utf-8',errors='replace').read()
m=lambda n:[html.unescape(x) for x in re.findall(rf'<meta name=\"{n}\" content=\"([^\"]*)\"',s)]
print('제목:',m('citation_title')[0]); print('날짜:',m('citation_date')[0]); print('저자:',' | '.join(m('citation_author')))"
```

## 과제(Project) 추가/수정 시

`CLAUDE.md`의 "Project 페이지 규칙"을 따른다. 요약:

- 제목은 `(기간) 과제명 (금액)`, 시작일 내림차순 정렬
- 회색 태그 대신 `<ul class="proj-meta">` 불렛, 우측 `.proj-mark`에는 **로고 이미지**
- 불렛에 우측 로고와 중복되는 지원기관명을 넣지 않는다
- 공동과제는 총액과 지분을 모두 적고, 상단 합계에는 지분만 더한다
- 상단에 `Funding in <올해>` / `Cumulative` 두 금액을 적고, 편집할 때마다 재계산한다

## 사진 추가 시

`CLAUDE.md`의 "사진" 절을 따른다. 핵심: EXIF 촬영일 사용, 1200px 리사이즈,
`figure.photo-item` 마크업, **게시 전 직접 확인**, 인물 이름 추측 금지.

## 편집 후 검증

```bash
# 태그 밸런스 (0이어야 정상)
python3 -c "
import html.parser,sys
class P(html.parser.HTMLParser):
    def __init__(s): super().__init__(); s.d=0
    def handle_starttag(s,t,a):
        if t not in ('br','hr','img','link','meta','input'): s.d+=1
    def handle_endtag(s,t): s.d-=1
for f in sys.argv[1:]:
    p=P(); p.feed(open(f,encoding='utf-8').read()); print(f,p.d)
" index.html news.html publication.html project.html

# 참조하는 이미지가 실제로 있는지
grep -roh 'assets/images/[a-z0-9./-]*' --include='*.html' . | sort -u | while read f; do [ -f "$f" ] || echo "MISSING $f"; done
```

## 로컬 미리보기

```bash
python3 -m http.server 8899   # 저장소 루트에서
```
브라우저 도구는 `file://`를 못 열므로 반드시 로컬 서버를 쓴다.

## 배포 확인

토큰은 `.env`에 있다 (`.gitignore`로 추적 제외).

```bash
set -a; . ./.env; set +a
gh api repos/codingchild2424/lab-website/pages/builds/latest --jq '.status + " " + .commit'
curl -sSL https://codingchild2424.github.io/lab-website/news.html -o /tmp/live.html && diff news.html /tmp/live.html && echo 동일
```

Pages 빌드 API는 갱신이 지연될 수 있으니, 실제 배포본을 직접 받아 비교하는 쪽을 신뢰한다.
