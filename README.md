# vuejs.org Bản Dịch Tiếng Việt

Đây là kho mã cho bản dịch tiếng Việt của tài liệu Vue.js tại [vuejs.org](https://vuejs.org).

Xem thêm:

- tài liệu tiếng Anh gốc tại [vuejs.org](https://vuejs.org)
- kho mã gốc tại [vuejs/docs](https://github.com/vuejs/docs)
- hướng dẫn cộng đồng dịch thuật tại [vuejs-translations/guidelines](https://github.com/vuejs-translations/guidelines)

## Cách tham gia đóng góp

Kho mã này bám sát [vuejs/docs](https://github.com/vuejs/docs). Mục tiêu là đồng bộ nội dung gốc và dịch sang tiếng Việt, không phát triển tài liệu tách nhánh với nội dung riêng.

Nếu bạn có góp ý về nội dung gốc tiếng Anh, hãy mở issue hoặc pull request trực tiếp tại [vuejs/docs](https://github.com/vuejs/docs).

Trong repo này, bạn có thể đóng góp bằng cách:

- dịch các trang còn thiếu
- rà soát và chỉnh sửa câu chữ, thuật ngữ, chính tả, và định dạng
- mở issue để thảo luận về cách dịch, thuật ngữ, hoặc workflow cộng tác
- đồng bộ thay đổi mới từ bản tiếng Anh

Danh sách công việc dịch được theo dõi trong [ROADMAP.md](./ROADMAP.md).

## Phát triển cục bộ

Trang này được xây dựng với [VitePress](https://github.com/vuejs/vitepress) và phụ thuộc vào [@vue/theme](https://github.com/vuejs/vue-theme). Nội dung được viết bằng Markdown trong thư mục `src`.

Yêu cầu Node.js `v20` trở lên. Nên bật corepack:

```bash
corepack enable
```

Dùng [pnpm](https://pnpm.io/) làm trình quản lý gói:

```bash
pnpm i
pnpm run dev
```
