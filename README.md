# TIRES88 — Landing Page

Static landing page cho **TIRES88** (Canada's tire distribution network). Đây là bản đã được **tái cấu trúc từ file bundle 14MB** (export từ Claude Design) thành một project sạch, responsive, deploy thẳng lên Vercel.

## Cấu trúc file

```
tires88/
├── index.html                      # Trang chính (markup + design tokens + component logic)
├── vercel.json                     # Cấu hình static deploy + cache headers
├── README.md
└── assets/
    ├── css/
    │   └── styles.css              # Responsive enhancements + mobile navigation
    ├── js/
    │   └── dc-runtime.js           # Runtime render (đã trỏ React về local, bỏ unpkg)
    ├── vendor/
    │   ├── react.production.min.js     # React 18.3.1 UMD (self-hosted)
    │   └── react-dom.production.min.js  # ReactDOM 18.3.1 UMD (self-hosted)
    └── img/                        # 21 ảnh (PNG nhỏ + WebP cho ảnh lớn)
```

## Deploy lên Vercel

Không cần build step — đây là **static site** thuần.

**Cách 1 — CLI:**
```bash
npm i -g vercel
cd tires88
vercel            # preview
vercel --prod     # production
```

**Cách 2 — Dashboard:** kéo-thả thư mục `tires88/` vào Vercel, hoặc connect Git repo. Framework Preset chọn **Other** (static), để trống Build Command & Output Directory.

Vì là static, deploy được luôn lên Netlify Drop / Cloudflare Pages / Surge với cùng thư mục.

**Preview local** (nên chạy qua HTTP server, không mở trực tiếp `file://`):
```bash
cd tires88
npx serve            # hoặc: python3 -m http.server 8080
```

## Những gì đã thay đổi so với bản gốc

- **Bỏ blob 14MB:** toàn bộ ảnh/font base64 nhúng trong 1 file HTML đã được tách ra file thật. Tổng dung lượng **14MB → ~1.4MB**.
- **Bỏ phụ thuộc CDN ngoài:** React/ReactDOM trước đây fetch từ `unpkg.com` → nay self-host trong `assets/vendor/`. Site chạy được kể cả khi unpkg lỗi.
- **Tối ưu ảnh:** các PNG lớn (2–3MB) convert sang **WebP** (quality 82, cap 1600px) → giảm ~90% dung lượng ảnh.
- **Fonts qua Google Fonts:** Instrument Sans, Inter, Space Mono, Bebas Neue (thay vì 33 file woff2 nhúng).
- **Responsive đầy đủ:** thêm **mobile menu** (hamburger, pure-CSS checkbox-hack, không cần JS), kèm các breakpoint tại 980px / 640px / 380px. Layout grid tự stack, padding co lại, hero/section vừa mọi thiết bị.
- **SEO/meta:** thêm `title`, `description`, Open Graph, `theme-color`, favicon.

## Kiến trúc render (đọc nếu cần sửa sâu)

Trang dùng **dc-runtime** (runtime của Claude Design, React-based) để render. Cơ chế:

1. `index.html` chứa template trong `<x-dc>...</x-dc>` + một component (`<script type="text/x-dc">`) giữ state tương tác (hero carousel 3D, FAQ accordion, product category selector, scroll rail, quote form).
2. `dc-runtime.js` parse template, resolve design tokens `{{bg}}`, `{{accent}}`… (tính từ props: accent = **Crimson** `#c0181e`, font pair = **Instrument Sans / Inter**), chuyển `style-hover` thành CSS thật, rồi mount bằng React vào `#dc-root`.

### Sửa nội dung / màu / font

- **Đổi accent color hoặc font pair:** sửa hàm `renderVals()` trong block `<script type="text/x-dc">` ở cuối `index.html` (bảng `accents` và `fonts`). Mặc định `accent = 'Crimson'`.
- **Sửa text, ảnh, section:** chỉnh trực tiếp markup trong `<x-dc>`. Ảnh dùng đường dẫn `./assets/img/...`.
- **Sửa responsive / mobile menu:** chỉnh `assets/css/styles.css`. Các rule layout gốc (stack grid, padding) nằm trong `<style>` ngay trong `index.html` (block media-query 980/640).

> Lưu ý: `dc-runtime.js` là code generated — đừng sửa tay. Nếu cần đổi React version, thay file trong `assets/vendor/` và (nếu cần) cập nhật URL trong `dc-runtime.js`.
# tires88-structure
