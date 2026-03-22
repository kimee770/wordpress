# WordPress 커스텀 작업 노트

## 사이트 정보
- URL: kimee77.mycafe24.com

---

## 작업 내역

---

### AI 카테고리 블로그 섹션 폰트 → Pretendard 변경

**적용 방법:** WordPress 관리자 → 외모 → 사용자 정의하기 → 추가 CSS

```css
/* Pretendard 폰트 로드 */
@import url('https://cdn.jsdelivr.net/gh/orioncactus/pretendard/dist/web/static/pretendard.css');

/* AI 카테고리 블로그 섹션 전체 텍스트 */
.category-ai .nectar-post-grid,
.category-ai .nectar-post-grid-item,
.category-ai .blog-archive-header,
.category-ai h1,
.category-ai h2,
.category-ai h3,
.category-ai h4,
.category-ai h5,
.category-ai h6,
.category-ai p,
.category-ai a,
.category-ai span,
.category-ai li {
  font-family: 'Pretendard', -apple-system, BlinkMacSystemFont, sans-serif !important;
}
```

