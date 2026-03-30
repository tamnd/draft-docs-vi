---
footer: false
---

<script setup>
import { VTCodeGroup, VTCodeGroupTab } from '@vue/theme'
</script>

# Bắt đầu nhanh {#quick-start}

## Thử Vue trực tuyến {#try-vue-online}

- Để nhanh chóng trải nghiệm Vue, bạn có thể thử trực tiếp trên [Playground](https://play.vuejs.org/#eNo9jcEKwjAMhl/lt5fpQYfXUQfefAMvvRQbddC1pUuHUPrudg4HIcmXjyRZXEM4zYlEJ+T0iEPgXjn6BB8Zhp46WUZWDjCa9f6w9kAkTtH9CRinV4fmRtZ63H20Ztesqiylphqy3R5UYBqD1UyVAPk+9zkvV1CKbCv9poMLiTEfR2/IXpSoXomqZLtti/IFwVtA9A==).

- Nếu bạn thích một thiết lập HTML thuần không có bước build nào, bạn có thể dùng [JSFiddle](https://jsfiddle.net/yyx990803/2ke1ab0z/) này làm điểm bắt đầu.

- Nếu bạn đã quen với Node.js và build tool, bạn cũng có thể thử luôn một dự án hoàn chỉnh ngay trong trình duyệt qua [StackBlitz](https://vite.new/vue).

- Để xem hướng dẫn từng bước về thiết lập được khuyến nghị, hãy thử [hướng dẫn tương tác trên Scrimba](http://scrimba.com/links/vue-quickstart), nơi bạn sẽ được hướng dẫn cách chạy, chỉnh sửa, và triển khai ứng dụng Vue đầu tiên của mình.

## Tạo ứng dụng Vue {#creating-a-vue-application}

:::tip Điều kiện tiên quyết

- Quen thuộc với command line
- Cài đặt [Node.js](https://nodejs.org/) phiên bản `^20.19.0 || >=22.12.0`
  :::

Trong phần này, chúng ta sẽ tạo thử một [Single Page Application](/guide/extras/ways-of-using-vue#single-page-application-spa) Vue trên máy của bạn. Dự án được tạo ra sẽ dùng cấu hình build dựa trên [Vite](https://vite.dev/) và cho phép chúng ta sử dụng Vue [Single-File Components](/guide/scaling-up/sfc) (SFC).

Hãy đảm bảo bạn đã cài đặt phiên bản [Node.js](https://nodejs.org/) mới và thư mục làm việc hiện tại chính là nơi bạn muốn tạo dự án. Chạy lệnh sau trong command line của bạn, không cần gõ ký hiệu `$`:

::: code-group

```sh [npm]
$ npm create vue@latest
```

```sh [pnpm]
$ pnpm create vue@latest
```

```sh [yarn]
# Dành cho Yarn (v1+)
$ yarn create vue

# Dành cho Yarn Modern (v2+)
$ yarn create vue@latest
  
# Dành cho Yarn ^v4.11
$ yarn dlx create-vue@latest
```

```sh [bun]
$ bun create vue@latest
```
:::

Lệnh này sẽ cài và chạy [create-vue](https://github.com/vuejs/create-vue), công cụ tạo dự án chính thức của Vue. Sau đó bạn sẽ thấy một số câu hỏi về các tính năng tùy chọn như TypeScript hay testing:

<div class="language-sh"><pre><code><span style="color:var(--vt-c-green);">✔</span> <span style="color:#A6ACCD;">Project name: <span style="color:#888;">… <span style="color:#89DDFF;">&lt;</span><span style="color:#888;">your-project-name</span><span style="color:#89DDFF;">&gt;</span></span></span>
<span style="color:var(--vt-c-green);">✔</span> <span style="color:#A6ACCD;">Add TypeScript? <span style="color:#888;">… <span style="color:#89DDFF;text-decoration:underline">No</span> / Có</span></span>
<span style="color:var(--vt-c-green);">✔</span> <span style="color:#A6ACCD;">Add JSX Support? <span style="color:#888;">… <span style="color:#89DDFF;text-decoration:underline">No</span> / Có</span></span>
<span style="color:var(--vt-c-green);">✔</span> <span style="color:#A6ACCD;">Add Vue Router for Single Page Application development? <span style="color:#888;">… <span style="color:#89DDFF;text-decoration:underline">No</span> / Có</span></span>
<span style="color:var(--vt-c-green);">✔</span> <span style="color:#A6ACCD;">Add Pinia for state management? <span style="color:#888;">… <span style="color:#89DDFF;text-decoration:underline">No</span> / Có</span></span>
<span style="color:var(--vt-c-green);">✔</span> <span style="color:#A6ACCD;">Add Vitest for Unit testing? <span style="color:#888;">… <span style="color:#89DDFF;text-decoration:underline">No</span> / Có</span></span>
<span style="color:var(--vt-c-green);">✔</span> <span style="color:#A6ACCD;">Add an End-to-End Testing Solution? <span style="color:#888;">… <span style="color:#89DDFF;text-decoration:underline">No</span> / Cypress / Nightwatch / Playwright</span></span>
<span style="color:var(--vt-c-green);">✔</span> <span style="color:#A6ACCD;">Add ESLint for code quality? <span style="color:#888;">… No / <span style="color:#89DDFF;text-decoration:underline">Có</span></span></span>
<span style="color:var(--vt-c-green);">✔</span> <span style="color:#A6ACCD;">Add Prettier for code formatting? <span style="color:#888;">… <span style="color:#89DDFF;text-decoration:underline">No</span> / Có</span></span>
<span style="color:var(--vt-c-green);">✔</span> <span style="color:#A6ACCD;">Add Vue DevTools 7 extension for debugging? (experimental) <span style="color:#888;">… <span style="color:#89DDFF;text-decoration:underline">No</span> / Có</span></span>
<span></span>
<span style="color:#A6ACCD;">Đang tạo dự án vào ./<span style="color:#89DDFF;">&lt;</span><span style="color:#888;">your-project-name</span><span style="color:#89DDFF;">&gt;</span>...</span>
<span style="color:#A6ACCD;">Hoàn tất.</span></code></pre></div>

Nếu bạn chưa chắc về một tùy chọn nào đó, cứ nhấn Enter để chọn `No` trước. Sau khi dự án được tạo xong, hãy làm theo hướng dẫn để cài đặt dependencies và khởi động dev server:

::: code-group

```sh-vue [npm]
$ cd {{'<your-project-name>'}}
$ npm install
$ npm run dev
```

```sh-vue [pnpm]
$ cd {{'<your-project-name>'}}
$ pnpm install
$ pnpm run dev
```

```sh-vue [yarn]
$ cd {{'<your-project-name>'}}
$ yarn
$ yarn dev
```

```sh-vue [bun]
$ cd {{'<your-project-name>'}}
$ bun install
$ bun run dev
```

:::


Giờ đây bạn đã có dự án Vue đầu tiên đang chạy. Lưu ý rằng các component ví dụ trong dự án được tạo ra sẽ dùng [Composition API](/guide/introduction#composition-api) và `<script setup>`, thay vì [Options API](/guide/introduction#options-api). Dưới đây là một vài gợi ý thêm:

- Thiết lập IDE được khuyến nghị là [Visual Studio Code](https://code.visualstudio.com/) + [Vue - Official extension](https://marketplace.visualstudio.com/items?itemName=Vue.volar). Nếu bạn dùng editor khác, hãy xem phần [hỗ trợ IDE](/guide/scaling-up/tooling#ide-support).
- Chi tiết hơn về công cụ phát triển, bao gồm cả tích hợp với các backend framework, được trình bày trong [Hướng dẫn công cụ](/guide/scaling-up/tooling).
- Để hiểu thêm về Vite, build tool nền tảng phía sau, hãy xem [tài liệu Vite](https://vite.dev/).
- Nếu bạn chọn sử dụng TypeScript, hãy xem [Hướng dẫn sử dụng TypeScript](/guide/typescript/overview).

Khi bạn sẵn sàng đưa ứng dụng lên production, hãy chạy lệnh sau:

::: code-group

```sh [npm]
$ npm run build
```

```sh [pnpm]
$ pnpm run build
```

```sh [yarn]
$ yarn build
```

```sh [bun]
$ bun run build
```

:::


Lệnh này sẽ tạo ra bản build sẵn sàng cho production của ứng dụng trong thư mục `./dist` của dự án. Hãy xem [Hướng dẫn triển khai production](/guide/best-practices/production-deployment) để tìm hiểu thêm về cách đưa ứng dụng của bạn lên production.

[Các bước tiếp theo >](#next-steps)

## Dùng Vue từ CDN {#using-vue-from-cdn}

Bạn có thể dùng Vue trực tiếp từ CDN thông qua một thẻ script:

```html
<script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
```

Ở đây chúng ta dùng [unpkg](https://unpkg.com/), nhưng bạn cũng có thể dùng bất kỳ CDN nào phân phối package npm, ví dụ như [jsdelivr](https://www.jsdelivr.com/package/npm/vue) hoặc [cdnjs](https://cdnjs.com/libraries/vue). Tất nhiên, bạn cũng có thể tải tệp này về và tự phục vụ nó.

Khi dùng Vue từ CDN, sẽ không có "bước build" nào cả. Điều này giúp thiết lập đơn giản hơn nhiều và phù hợp để tăng cường HTML tĩnh hoặc tích hợp với backend framework. Tuy nhiên, bạn sẽ không thể sử dụng cú pháp Single-File Component (SFC).

### Dùng bản build global {#using-the-global-build}

Liên kết phía trên tải _global build_ của Vue, nơi tất cả API cấp cao nhất được expose dưới dạng thuộc tính trên object toàn cục `Vue`. Dưới đây là ví dụ đầy đủ sử dụng global build:

<div class="options-api">

```html
<script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>

<div id="app">{{ message }}</div>

<script>
  const { createApp } = Vue

  createApp({
    data() {
      return {
        message: 'Xin chào Vue!'
      }
    }
  }).mount('#app')
</script>
```

[Demo trên CodePen >](https://codepen.io/vuejs-examples/pen/QWJwJLp)

</div>

<div class="composition-api">

```html
<script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>

<div id="app">{{ message }}</div>

<script>
  const { createApp, ref } = Vue

  createApp({
    setup() {
      const message = ref('Xin chào Vue!')
      return {
        message
      }
    }
  }).mount('#app')
</script>
```

[Demo trên CodePen >](https://codepen.io/vuejs-examples/pen/eYQpQEG)

:::tip
Rất nhiều ví dụ về Composition API trong phần hướng dẫn sẽ dùng cú pháp `<script setup>`, vốn yêu cầu build tool. Nếu bạn muốn dùng Composition API mà không có bước build, hãy tham khảo cách dùng [`setup()` option](/api/composition-api-setup).
:::

</div>

### Dùng bản build ES Module {#using-the-es-module-build}

Trong phần còn lại của tài liệu, chúng tôi sẽ chủ yếu sử dụng cú pháp [ES modules](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Modules). Hầu hết các trình duyệt hiện đại ngày nay đã hỗ trợ ES modules một cách tự nhiên, vì vậy chúng ta có thể dùng Vue từ CDN thông qua ES modules như sau:

<div class="options-api">

```html{3,4}
<div id="app">{{ message }}</div>

<script type="module">
  import { createApp } from 'https://unpkg.com/vue@3/dist/vue.esm-browser.js'

  createApp({
    data() {
      return {
        message: 'Xin chào Vue!'
      }
    }
  }).mount('#app')
</script>
```

</div>

<div class="composition-api">

```html{3,4}
<div id="app">{{ message }}</div>

<script type="module">
  import { createApp, ref } from 'https://unpkg.com/vue@3/dist/vue.esm-browser.js'

  createApp({
    setup() {
      const message = ref('Xin chào Vue!')
      return {
        message
      }
    }
  }).mount('#app')
</script>
```

</div>

Lưu ý rằng ở đây chúng ta đang dùng `<script type="module">`, và URL CDN được import vào đang trỏ tới **bản build ES modules** của Vue.

<div class="options-api">

[Demo trên CodePen >](https://codepen.io/vuejs-examples/pen/VwVYVZO)

</div>
<div class="composition-api">

[Demo trên CodePen >](https://codepen.io/vuejs-examples/pen/MWzazEv)

</div>

### Bật Import Maps {#enabling-import-maps}

Trong ví dụ ở trên, chúng ta import từ URL CDN đầy đủ, nhưng ở phần còn lại của tài liệu bạn sẽ thấy đoạn mã như thế này:

```js
import { createApp } from 'vue'
```

Chúng ta có thể chỉ cho trình duyệt biết vị trí của import `vue` bằng [Import Maps](https://caniuse.com/import-maps):

<div class="options-api">

```html{1-7,12}
<script type="importmap">
  {
    "imports": {
      "vue": "https://unpkg.com/vue@3/dist/vue.esm-browser.js"
    }
  }
</script>

<div id="app">{{ message }}</div>

<script type="module">
  import { createApp } from 'vue'

  createApp({
    data() {
      return {
        message: 'Xin chào Vue!'
      }
    }
  }).mount('#app')
</script>
```

[Demo trên CodePen >](https://codepen.io/vuejs-examples/pen/wvQKQyM)

</div>

<div class="composition-api">

```html{1-7,12}
<script type="importmap">
  {
    "imports": {
      "vue": "https://unpkg.com/vue@3/dist/vue.esm-browser.js"
    }
  }
</script>

<div id="app">{{ message }}</div>

<script type="module">
  import { createApp, ref } from 'vue'

  createApp({
    setup() {
      const message = ref('Xin chào Vue!')
      return {
        message
      }
    }
  }).mount('#app')
</script>
```

[Demo trên CodePen >](https://codepen.io/vuejs-examples/pen/YzRyRYM)

</div>

Bạn cũng có thể thêm các mục cho dependency khác vào import map, nhưng hãy đảm bảo chúng trỏ tới phiên bản ES modules của thư viện mà bạn muốn dùng.

:::tip Khả năng hỗ trợ Import Maps trên trình duyệt
Import Maps là một tính năng trình duyệt tương đối mới. Hãy đảm bảo bạn dùng trình duyệt nằm trong [phạm vi hỗ trợ](https://caniuse.com/import-maps) của nó. Đặc biệt, Safari chỉ hỗ trợ từ phiên bản 16.4 trở lên.
:::

:::warning Lưu ý khi dùng trong production
Các ví dụ đến đây vẫn đang sử dụng bản development build của Vue. Nếu bạn định dùng Vue từ CDN trong production, hãy nhớ xem [Hướng dẫn triển khai production](/guide/best-practices/production-deployment#without-build-tools).

Mặc dù hoàn toàn có thể dùng Vue mà không cần hệ thống build, một hướng tiếp cận thay thế đáng cân nhắc là [`vuejs/petite-vue`](https://github.com/vuejs/petite-vue), vốn có thể phù hợp hơn trong những bối cảnh mà trước đây người ta thường dùng [`jquery/jquery`](https://github.com/jquery/jquery), hoặc hiện nay là [`alpinejs/alpine`](https://github.com/alpinejs/alpine).
:::

### Tách các module {#splitting-up-the-modules}

Khi đi sâu hơn vào phần hướng dẫn, chúng ta có thể cần tách mã nguồn ra thành các tệp JavaScript riêng để dễ quản lý hơn. Ví dụ:

```html [index.html]
<div id="app"></div>

<script type="module">
  import { createApp } from 'vue'
  import MyComponent from './my-component.js'

  createApp(MyComponent).mount('#app')
</script>
```

<div class="options-api">

```js [my-component.js]
export default {
  data() {
    return { count: 0 }
  },
  template: `<div>Số đếm là: {{ count }}</div>`
}
```

</div>
<div class="composition-api">

```js [my-component.js]
import { ref } from 'vue'
export default {
  setup() {
    const count = ref(0)
    return { count }
  },
  template: `<div>Số đếm là: {{ count }}</div>`
}
```

</div>

Nếu bạn mở trực tiếp `index.html` ở trên trong trình duyệt, bạn sẽ thấy lỗi xuất hiện vì ES modules không thể hoạt động qua giao thức `file://`, vốn là giao thức mà trình duyệt dùng khi bạn mở một tệp cục bộ.

Vì lý do bảo mật, ES modules chỉ có thể hoạt động qua giao thức `http://`, cũng là giao thức mà trình duyệt dùng khi mở các trang trên web. Để ES modules hoạt động trên máy cục bộ, chúng ta cần phục vụ `index.html` thông qua giao thức `http://` bằng một HTTP server cục bộ.

Để khởi động một HTTP server cục bộ, trước tiên hãy chắc chắn rằng bạn đã cài [Node.js](https://nodejs.org/en/), sau đó chạy `npx serve` trong command line tại chính thư mục chứa tệp HTML của bạn. Bạn cũng có thể dùng bất kỳ HTTP server nào khác có khả năng phục vụ tệp tĩnh với MIME type chính xác.

Bạn có thể nhận ra rằng template của component được import ở đây đang được viết inline dưới dạng một chuỗi JavaScript. Nếu bạn đang dùng VS Code, bạn có thể cài extension [es6-string-html](https://marketplace.visualstudio.com/items?itemName=Tobermory.es6-string-html) và thêm comment `/*html*/` trước chuỗi để có syntax highlighting.

## Các bước tiếp theo {#next-steps}

Nếu bạn đã bỏ qua phần [Giới thiệu](/guide/introduction), chúng tôi đặc biệt khuyến nghị bạn đọc nó trước khi tiếp tục với phần còn lại của tài liệu.

<div class="vt-box-container next-steps">
  <a class="vt-box" href="/guide/essentials/application.html">
    <p class="next-steps-link">Tiếp tục với phần hướng dẫn</p>
    <p class="next-steps-caption">Phần hướng dẫn sẽ đưa bạn đi qua từng khía cạnh của framework một cách đầy đủ và chi tiết.</p>
  </a>
  <a class="vt-box" href="/tutorial/">
    <p class="next-steps-link">Thử phần hướng dẫn tương tác</p>
    <p class="next-steps-caption">Dành cho những ai thích học bằng cách trực tiếp thực hành.</p>
  </a>
  <a class="vt-box" href="/examples/">
    <p class="next-steps-link">Xem các ví dụ</p>
    <p class="next-steps-caption">Khám phá các ví dụ về những tính năng cốt lõi và các tác vụ UI phổ biến.</p>
  </a>
</div>
