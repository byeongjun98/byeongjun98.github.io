# 게시글 작성 가이드

이 문서는 Jekyll 블로그에 새로운 게시글을 작성하는 방법을 안내합니다.

## 1. 파일 위치

모든 게시글은 프로젝트의 `_posts` 폴더 안에 저장되어야 합니다.

## 2. 파일 이름 규칙

게시글 파일의 이름은 반드시 `YYYY-MM-DD-원하는-제목.md` 형식을 따라야 합니다.

- `YYYY`: 4자리 연도
- `MM`: 2자리 월
- `DD`: 2자리 일
- `원하는-제목`: 영어 소문자와 하이픈(`-`)을 사용하여 작성합니다. (예: `my-first-post`)
- 확장자는 `.md` (마크다운)를 사용합니다.

**예시:** `2025-08-21-new-post-example.md`

## 3. 파일 내용 (Front Matter)

파일의 가장 위에는 'Front Matter'라고 불리는 설정 영역이 있어야 합니다. 이 부분은 `---` 사이에 작성합니다.

```yaml
---
layout: post
title: "여기에 게시글 제목을 입력하세요"
date: YYYY-MM-DD HH:MM:SS +0900
categories: [카테고리1, 카테고리2] # 대문자로 작성하는 것을 추천합니다
---
```

- `layout`: 항상 `post`로 유지하세요.
- `title`: 따옴표 안에 원하는 게시글의 제목을 작성합니다.
- `date`: 게시글 작성 날짜와 시간을 정확히 기입합니다. `+0900`은 한국 시간대를 의미합니다.
- `categories`: 게시글이 속할 카테고리를 입력합니다. 하나일 경우 `CATEGORIES`처럼, 여러 개일 경우 `[JAVA, SPRING]`처럼 대괄호로 묶어 쉼표로 구분합니다.

## 4. 본문 작성

Front Matter 영역 아래부터는 마크다운 문법을 사용하여 자유롭게 글의 내용을 작성하면 됩니다.

---

### 전체 예시 (`_posts/2025-08-21-new-post-example.md`)

```markdown
---
layout: post
title: "새로운 게시글 예시"
date: 2025-08-21 11:30:00 +0900
categories: [JAVA, ALGORITHM]
---

여기는 게시글의 첫 번째 문단입니다.

## 소제목

여기는 소제목 아래의 내용입니다.

- 리스트 1
- 리스트 2

```java
// 자바 코드 블록 예시
public class Main {
    public static void main(String[] args) {
        System.out.println("Hello World!");
    }
}
```
```
