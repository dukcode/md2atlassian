# 최소 테스트 스펙

아직 테스트 구현은 하지 않는다. 이 문서는 나중에 `pytest` 등으로 옮길 핵심 회귀 테스트 범위만 기록한다.

## 1. URL 파싱

- Confluence 도메인 분리형 URL에서 base URL과 page ID를 추출한다.
  - 입력: `https://wiki.xxx.com/spaces/ABC/pages/123456/Page`
  - 기대: `("https://wiki.xxx.com", "123456")`
- Confluence context path URL에서 `/wiki`를 보존한다.
  - 입력: `https://xxx.atlassian.net/wiki/spaces/ABC/pages/123456/Page`
  - 기대: `("https://xxx.atlassian.net/wiki", "123456")`
- Jira 도메인 분리형 URL에서 base URL과 issue key를 추출한다.
  - 입력: `https://jira.xxx.com/browse/PROJ-123`
  - 기대: `("https://jira.xxx.com", "PROJ-123")`
- Jira context path URL에서 `/jira`를 보존한다.
  - 입력: `https://xxx.com/jira/browse/PROJ-123`
  - 기대: `("https://xxx.com/jira", "PROJ-123")`

## 2. Markdown 전처리

- 일반 Markdown heading의 섹션 번호를 제거한다.
  - 입력: `## 2.1 제목`
  - 기대: `## 제목`
- fenced code block 내부 heading은 변경하지 않는다.
  - 입력:

    ````markdown
    ```md
    ## 2.1 예시 제목
    ```
    ````

  - 기대: 코드블록 내부의 `## 2.1 예시 제목` 유지
- 일반 문장 바로 다음에 리스트가 오면 빈 줄을 삽입한다.
- fenced code block 내부 리스트는 변경하지 않는다.
- `mermaid` fenced code block은 일반 code block으로 낮춘다.
  - 입력: ```` ```mermaid ````
  - 기대: ```` ``` ````

## 3. Jira 변환 보정

- Jira가 지원하지 않는 코드 블록 언어는 `{code:lang}`에서 `{code}`로 낮춘다.
  - 예: `kotlin`, `typescript` 등
- Jira가 지원하는 코드 블록 언어는 유지한다.
  - 예: `bash`, `python`, `json`, `yaml`
- 한글이나 단어 문자에 붙은 인라인 monospace는 Jira에서 리터럴로 깨지지 않도록 보정한다.

## 4. Confluence 변환 보정

- Markdown 코드블록은 Confluence code macro로 변환한다.
- 첫 번째 `h1`은 Confluence 페이지 제목과 중복되지 않도록 제거한다.
- blockquote는 Confluence info macro로 변환한다.
- 로컬 이미지는 attachment reference로 변환하고 첨부 예정 목록에 포함한다.
- 원격 이미지는 URL reference로 변환한다.

## 5. CLI smoke

- `python3 -m py_compile md2atlassian.py`가 성공한다.
- `python3 md2atlassian.py README.md --dry-run --format confluence`가 성공한다.
- `python3 md2atlassian.py README.md --dry-run --format jira`가 성공한다.
