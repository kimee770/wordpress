# WordPress 커스텀 작업 노트

## 사이트 정보
- URL: kimee77.mycafe24.com (→ kimee.net 이전 예정)
- 테마: Salient (Nectar 프레임워크)
- 호스팅: cafe24 (FTP: kimee77.mycafe24.com / 포트 21 / ID: kimee77)

---

## 파일 구조 (업데이트 안전한 위치)

| 파일 | 경로 | 용도 |
|------|------|------|
| PHP 커스텀 | `wp-content/mu-plugins/kimee77-blog-custom.php` | WordPress 업데이트 무관 |
| CSS 커스텀 | `wp-content/uploads/kimee77/custom-fonts.css` | 테마 업데이트 무관 |
| functions.php | **건드리지 않음** | 테마 업데이트 시 초기화됨 |

> ⚠️ 테마 디렉토리(`wp-content/themes/salient/`) 내 파일은 테마 업데이트 시 덮어씌워짐

---

## 커스텀 작업 내역

---

### 1. Pretendard 폰트 적용 (카테고리 + 싱글 포스트)

**적용 범위:** `/category/ai/` 및 모든 single post
**방법:** mu-plugin에서 조건부 CSS 로드

```php
// wp-content/mu-plugins/kimee77-blog-custom.php
function kimee77_enqueue_custom_fonts() {
    if ( is_category() || is_single() ) {
        $upload_dir = wp_upload_dir();
        $css_url = set_url_scheme( $upload_dir['baseurl'] . '/kimee77/custom-fonts.css', 'https' );
        wp_enqueue_style( 'kimee77-custom-fonts', $css_url, array(), '1.0.3' );
    }
}
add_action( 'wp_enqueue_scripts', 'kimee77_enqueue_custom_fonts' );
```

```css
/* CDN */
@import url('https://cdn.jsdelivr.net/gh/orioncactus/pretendard@v1.3.9/dist/web/static/pretendard.min.css');

/* 카테고리 페이지 */
body.category { font-family: 'Pretendard', 'Open Sans', sans-serif !important; }

/* 싱글 포스트 전체 */
body.single-post { font-family: 'Pretendard', 'Open Sans', sans-serif !important; }
body.single-post h1,h2,h3,h4,h5,h6,p,a,span,li,td,blockquote {
    font-family: 'Pretendard', 'Open Sans', sans-serif !important;
}
```

---

### 2. 싱글 포스트 타이포그래피 조정

```css
body.single-post h3, body.single-post h4 { font-weight: 600 !important; }
body.single-post p { padding-bottom: 16px !important; }
body.single-post p:empty { display: block !important; height: 16px !important; padding-bottom: 0 !important; }
body.single-post li + li { margin-top: 4px !important; }
```

---

### 3. Blockquote 스타일

```css
body.single-post .wp-block-quote { margin-bottom: 1em !important; padding-bottom: 0 !important; }
body.single-post .wp-block-quote p { padding-bottom: 0 !important; margin-bottom: 0 !important; }
body.single-post .wp-block-quote p:empty { height: 0 !important; display: none !important; }
body.single-post .wp-block-quote > :last-child { margin-bottom: 0 !important; padding-bottom: 0 !important; }
```

---

### 4. 직선 따옴표 → 타이포그래피 따옴표 변환

**적용 범위:** 카테고리 + 싱글 포스트 (`the_content`, `the_title`, `widget_text`)

```php
function kimee77_curly_quotes( $content ) {
    // HTML 태그와 단축코드 내부는 건드리지 않고 텍스트만 변환
    return preg_replace_callback(
        '/(?:<[^>]*>|\[[^\]]*\])|"([^"]+)"/',
        function( $m ) {
            if ( empty( $m[1] ) ) return $m[0];
            return "\u{201C}" . $m[1] . "\u{201D}";
        },
        $content
    );
}
// 카테고리/싱글에서만 적용
if ( is_category() || is_single() ) {
    add_filter( 'the_content', 'kimee77_curly_quotes' );
    add_filter( 'the_title',   'kimee77_curly_quotes' );
    add_filter( 'widget_text', 'kimee77_curly_quotes' );
}
```

---

### 5. entry-title (포스트 제목) 스타일

```css
/* 제목 너비 = featured-media 컨테이너와 동일하게 */
body.single-post .featured-media-under-header__content {
    max-width: none !important;
}

body.single-post .entry-title {
    font-size: 64px !important;
    line-height: 84px !important;
    font-weight: 800 !important;
    width: 100% !important;
    align-self: stretch !important;
    padding-left: 40px !important;
    padding-right: 40px !important;
    box-sizing: border-box !important;
    word-break: keep-all !important;
}

/* 반응형 */
@media (max-width: 999px) {
    body.single-post .entry-title { font-size: 48px !important; line-height: 64px !important; }
}
@media (max-width: 600px) {
    body.single-post .entry-title { font-size: 36px !important; line-height: 48px !important; }
}
```

---

### 6. 카테고리 페이지 포스트 제목 단어 단위 줄바꿈

```css
body.category .post-header h3.title a {
    word-break: keep-all !important;
}
```

---

### 7. Featured Image 전체 높이 표시

기본 Salient 테마의 `padding-bottom: 40%` 고정 높이 제거 → 업로드한 이미지 원본 높이 표시

```css
body.single-post .featured-media-under-header__featured-media {
    padding-bottom: 0 !important;
    height: auto !important;
}
body.single-post .featured-media-under-header__featured-media .post-featured-img {
    position: static !important;
    height: auto !important;
}
body.single-post .featured-media-under-header__featured-media .post-featured-img img {
    position: static !important;
    height: auto !important;
    width: 100% !important;
    top: auto !important;
    left: auto !important;
}
```

---

## 주의사항

- 도메인 이전 예정 (kimee77.mycafe24.com → kimee.net): URL 하드코딩 금지, WordPress 함수 사용
- WP Super Cache: 개발 중 비활성화 권장 (캐시로 인한 변경사항 미반영 방지)
- CSS/PHP 변경 후 반드시 캐시 삭제
