# Production Deployment {#production-deployment}

## Development và Production {#development-vs-production}

Trong quá trình phát triển, Vue cung cấp khá nhiều tính năng để cải thiện trải nghiệm lập trình:

- Cảnh báo cho các lỗi và bẫy thường gặp
- Kiểm tra props / events
- [Hook debug tính phản ứng](/guide/extras/reactivity-in-depth#reactivity-debugging)
- Tích hợp Devtools

Tuy vậy, các tính năng này lại không còn hữu ích trong production. Một số bước kiểm tra cảnh báo còn tạo thêm một chút overhead về hiệu năng. Khi triển khai lên production, ta nên loại bỏ toàn bộ những nhánh code chỉ phục vụ development nhưng không dùng tới, để giảm kích thước payload và cải thiện hiệu năng.

## Không dùng build tool {#without-build-tools}

Nếu bạn dùng Vue mà không qua build tool, ví dụ tải từ CDN hoặc script tự host, hãy nhớ dùng bản build production, tức các file dist có hậu tố `.prod.js`, khi triển khai lên production. Các bản build production đã được minify sẵn và đã loại bỏ toàn bộ nhánh code chỉ dành cho development.

- Nếu dùng global build, tức truy cập qua biến toàn cục `Vue`, hãy dùng `vue.global.prod.js`.
- Nếu dùng ESM build, tức import bằng ESM gốc của trình duyệt, hãy dùng `vue.esm-browser.prod.js`.

Xem thêm [hướng dẫn về các file dist](https://github.com/vuejs/core/tree/main/packages/vue#which-dist-file-to-use).

## Có dùng build tool {#with-build-tools}

Những project được tạo bằng `create-vue`, dựa trên Vite, hoặc Vue CLI, dựa trên webpack, đều đã được cấu hình sẵn cho bản build production.

Nếu bạn dùng thiết lập tùy chỉnh, hãy bảo đảm rằng:

1. `vue` được resolve tới `vue.runtime.esm-bundler.js`.
2. [Compile time feature flags](/api/compile-time-flags) được cấu hình đúng.
3. <code>process.env<wbr>.NODE_ENV</code> được thay bằng `"production"` trong quá trình build.

Tài liệu tham khảo thêm:

- [Hướng dẫn build production của Vite](https://vite.dev/guide/build.html)
- [Hướng dẫn deploy của Vite](https://vite.dev/guide/static-deploy.html)
- [Hướng dẫn deploy của Vue CLI](https://cli.vuejs.org/guide/deployment.html)

## Theo dõi lỗi lúc chạy {#tracking-runtime-errors}

[App-level error handler](/api/application#app-config-errorhandler) có thể được dùng để báo lỗi tới các dịch vụ theo dõi lỗi:

```js
import { createApp } from 'vue'

const app = createApp(...)

app.config.errorHandler = (err, instance, info) => {
  // báo lỗi tới các dịch vụ theo dõi
}
```

Các dịch vụ như [Sentry](https://docs.sentry.io/platforms/javascript/guides/vue/) và [Bugsnag](https://docs.bugsnag.com/platforms/javascript/vue/) cũng có tích hợp chính thức cho Vue.
