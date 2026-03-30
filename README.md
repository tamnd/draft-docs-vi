# Bản dịch tiếng Việt cho vuejs.org

Đây là repository (kho mã nguồn) chứa bản dịch tiếng Việt của tài liệu Vue.js trên [vuejs.org](https://vuejs.org).

Tham khảo thêm:

* Tài liệu tiếng Anh gốc tại [vuejs.org](https://vuejs.org)
* Repository gốc tại [vuejs/docs](https://github.com/vuejs/docs)
* Hướng dẫn dành cho cộng đồng dịch thuật tại [vuejs-translations/guidelines](https://github.com/vuejs-translations/guidelines)

## Tham gia đóng góp

Repository này được duy trì đồng bộ với [vuejs/docs](https://github.com/vuejs/docs). Mục tiêu là bám sát nội dung gốc và chuyển ngữ sang tiếng Việt, không tách riêng hay phát triển nội dung độc lập.

Nếu bạn có ý kiến về nội dung tiếng Anh, hãy tạo issue hoặc PR trực tiếp tại [vuejs/docs](https://github.com/vuejs/docs).

Tại repository này, bạn có thể đóng góp bằng cách:

* Dịch các trang chưa có bản tiếng Việt
* Rà soát và cải thiện câu chữ, thuật ngữ, chính tả, và định dạng
* Tạo issue để thảo luận về cách dịch, thuật ngữ, hoặc quy trình cộng tác
* Cập nhật nội dung theo các thay đổi mới từ bản gốc

## Chạy local

Trang web được xây dựng bằng [VitePress](https://github.com/vuejs/vitepress) và sử dụng theme từ [@vue/theme](https://github.com/vuejs/vue-theme). Toàn bộ nội dung nằm trong thư mục `src`, được viết với định dạng Markdown.

Yêu cầu Node.js phiên bản `v20` trở lên. Khuyến nghị bật corepack:

```bash
corepack enable
```

Sử dụng [pnpm](https://pnpm.io/) để quản lý dependencies:

```bash
pnpm i
pnpm run dev
```
