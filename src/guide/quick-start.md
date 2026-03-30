---
footer: false
---

<script setup>
import { VTCodeGroup, VTCodeGroupTab } from '@vue/theme'
</script>

# Bắt đầu nhanh {#quick-start}

## Thử Vue trực tuyến {#try-vue-online}

- Bạn có thể thử nhanh Vue trên [Playground](https://play.vuejs.org/#eNo9jcEKwjAMhl/lt5fpQYfXUQfefAMvvRQbddC1pUuHUPrudg4HIcmXjyRZXEM4zYlEJ+T0iEPgXjn6BB8Zhp46WUZWDjCa9f6w9kAkTtH9CRinV4fmRtZ63H20Ztesqiylphqy3R5UYBqD1UyVAPk+9zkvV1CKbCv9poMLiTEfR2/IXpSoXomqZLtti/IFwVtA9A==).

- Nếu muốn dùng HTML thuần, không cần build, có thể dùng [JSFiddle](https://jsfiddle.net/yyx990803/2ke1ab0z/) làm điểm bắt đầu.

- Nếu đã quen với Node.js và build tool, có thể chạy dự án Vue ngay trong trình duyệt qua [StackBlitz](https://vite.new/vue).

- Ngoài ra có thể học theo [hướng dẫn tương tác trên Scrimba](http://scrimba.com/links/vue-quickstart) để biết cách chạy, chỉnh sửa và deploy ứng dụng Vue đầu tiên.

## Tạo ứng dụng Vue {#creating-a-vue-application}

:::tip Điều kiện tiên quyết

- Biết dùng command line
- Đã cài [Node.js](https://nodejs.org/) phiên bản `^20.19.0 || >=22.12.0`
  :::

Ở phần này, bạn sẽ tạo một ứng dụng Vue dạng [SPA](/guide/extras/ways-of-using-vue#single-page-application-spa) chạy trên máy. Dự án dùng [Vite](https://vite.dev/) làm build tool và hỗ trợ [Single-File Component](/guide/scaling-up/sfc) (SFC).

Chạy lệnh sau (không cần gõ ký hiệu `$`):

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

Lệnh này sẽ chạy công cụ [create-vue](https://github.com/vuejs/create-vue) để tạo project. Sau đó bạn sẽ chọn các tùy chọn như TypeScript, router, testing...

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

Nếu chưa chắc, cứ chọn `No`. Sau khi tạo xong:

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


Giờ bạn đã có project Vue chạy local. Một số lưu ý:

- Project mặc định dùng [Composition API](/guide/introduction#composition-api) + `<script setup>`
- IDE khuyến nghị: [Visual Studio Code](https://code.visualstudio.com/) + [Vue - Official extension](https://marketplace.visualstudio.com/items?itemName=Vue.volar). Nếu dùng editor khác, xem phần [hỗ trợ IDE](/guide/scaling-up/tooling#ide-support).
- Có thể xem thêm [tài liệu Vite](https://vite.dev/) nếu muốn hiểu build tool
- Nếu dùng TypeScript, nên đọc thêm [guide riêng](/guide/typescript/overview)

Build production:

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


Kết quả sẽ nằm trong thư mục `./dist`. Xem thêm [Hướng dẫn triển khai production](/guide/best-practices/production-deployment).

[Các bước tiếp theo >](#next-steps)

## Dùng Vue từ CDN {#using-vue-from-cdn}

Bạn có thể dùng Vue trực tiếp từ CDN thông qua một thẻ script:

```html
<script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
```

Không cần build, phù hợp cho demo nhanh hoặc thêm Vue vào trang có sẵn. Nhưng không dùng được SFC.

Bạn cũng có thể dùng [jsdelivr](https://www.jsdelivr.com/package/npm/vue) hoặc [cdnjs](https://cdnjs.com/libraries/vue) thay cho [unpkg](https://unpkg.com/).

### Dùng bản build global {#using-the-global-build}

Bản global expose tất cả API trên object `Vue`:

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

### Dùng bản build ES modules {#using-the-es-module-build}

Hầu hết trình duyệt hiện đại đã hỗ trợ [ES modules](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Modules), nên có thể dùng Vue qua ES modules:

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

Lưu ý dùng `<script type="module">` và URL trỏ tới **bản build ES modules** của Vue.

<div class="options-api">

[Demo trên CodePen >](https://codepen.io/vuejs-examples/pen/VwVYVZO)

</div>
<div class="composition-api">

[Demo trên CodePen >](https://codepen.io/vuejs-examples/pen/MWzazEv)

</div>

### Bật Import Maps {#enabling-import-maps}

Ví dụ trên import từ URL đầy đủ. Để viết ngắn gọn hơn:

```js
import { createApp } from 'vue'
```

Bạn có thể dùng [Import Maps](https://caniuse.com/import-maps) để map import `vue`:

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

Bạn cũng có thể thêm các dependency khác vào import map, miễn là trỏ tới bản ES modules.

:::tip Lưu ý
- Import maps chỉ hỗ trợ trên [trình duyệt mới](https://caniuse.com/import-maps) (Safari 16.4+)
- Production cần dùng bản build tối ưu, xem [Hướng dẫn triển khai production](/guide/best-practices/production-deployment#without-build-tools)
- Có thể cân nhắc [`vuejs/petite-vue`](https://github.com/vuejs/petite-vue) cho case nhẹ
:::

### Tách module {#splitting-up-the-modules}

Có thể tách component ra file riêng. Ví dụ:

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

Lưu ý: ES modules không chạy được qua `file://`. Cần chạy qua HTTP server, ví dụ:

```sh
npx serve
```

## Các bước tiếp theo {#next-steps}

Nếu chưa đọc phần [Giới thiệu](/guide/introduction), nên đọc trước khi tiếp tục.

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
