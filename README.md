# Vuejs.org Bản Dịch Tiếng Việt

Đây là kho mã cho bản dịch tiếng Việt của tài liệu mới tại `vuejs.org`.

Bạn cũng có thể xem:

- tài liệu tiếng Anh gốc tại [https://vuejs.org](https://vuejs.org)
- kho mã gốc tại [vuejs/docs](https://github.com/vuejs/docs)
- hướng dẫn cộng đồng dịch thuật tại [vuejs-translations/guidelines](https://github.com/vuejs-translations/guidelines)

## Cách tham gia đóng góp

Kho mã này là repo dịch bám sát theo [vuejs/docs](https://github.com/vuejs/docs). Mục tiêu của chúng ta là đồng bộ nội dung gốc và dịch sang tiếng Việt, không phát triển một bản tài liệu tách nhánh với nội dung riêng.

Nếu bạn có góp ý về nội dung gốc tiếng Anh, hãy mở issue hoặc pull request trực tiếp tại [vuejs/docs](https://github.com/vuejs/docs).

Trong repo này, bạn có thể đóng góp bằng cách:

- dịch thủ công các trang còn thiếu
- rà soát và chỉnh sửa câu chữ, thuật ngữ, chính tả, và định dạng
- mở issue để thảo luận về cách dịch, thuật ngữ, hoặc workflow cộng tác
- đồng bộ thay đổi mới từ bản tiếng Anh

Danh sách công việc dịch hiện được theo dõi trong [TRANSLATES.md](./TRANSLATES.md).

## Quy ước làm việc

- Mỗi trang nên đi qua issue hoặc pull request riêng để dễ review.
- Giữ nguyên anchor tiêu đề gốc khi dịch, ví dụ `## Tiêu đề {#original-anchor}`.
- Giữ nguyên tên API, package, component, directive, và mã nguồn ví dụ.
- Các đoạn giao diện không nằm trong markdown sẽ được xử lý trong `.vitepress/config.ts`.
- Khi gặp đoạn chưa dịch xong, có thể đánh dấu tạm bằng `<!-- TODO: translation -->`.

## Cách chạy và xem thử tại máy cục bộ

Khuyến nghị dùng [pnpm](https://pnpm.io/) làm package manager:

```bash
corepack enable
pnpm install
pnpm run dev
```

Dự án này yêu cầu Node.js `v20` hoặc cao hơn.

## Người đóng góp

Khi repo được public và bắt đầu có lịch sử đóng góp, có thể theo dõi danh sách contributor tại trang GitHub Contributors của repo này.

## Giấy phép

Nội dung và mã nguồn của repo này kế thừa giấy phép từ dự án gốc. Xem chi tiết trong [LICENSE](./LICENSE).
