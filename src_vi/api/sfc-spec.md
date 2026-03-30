# Đặc tả Cú pháp SFC {#sfc-syntax-specification}

## Tổng quan {#overview}

Một Vue Single-File Component (SFC), theo quy ước dùng phần mở rộng file `*.vue`, là một định dạng file tùy chỉnh sử dụng cú pháp giống HTML để mô tả một Vue component. Một Vue SFC về mặt cú pháp tương thích với HTML.

Mỗi file `*.vue` bao gồm ba loại khối ngôn ngữ cấp cao nhất: `<template>`, `<script>`, và `<style>`, và tùy chọn các khối tùy chỉnh bổ sung:

```vue
<template>
  <div class="example">{{ msg }}</div>
</template>

<script>
export default {
  data() {
    return {
      msg: 'Hello world!'
    }
  }
}
</script>

<style>
.example {
  color: red;
}
</style>

<custom1>
  Đây có thể là tài liệu cho component chẳng hạn.
</custom1>
```

## Các Khối Ngôn ngữ {#language-blocks}

### `<template>` {#template}

- Mỗi file `*.vue` có thể chứa tối đa một khối `<template>` cấp cao nhất.

- Nội dung sẽ được trích xuất và truyền vào `@vue/compiler-dom`, được biên dịch trước thành các hàm render JavaScript, và được đính kèm vào component được export là tùy chọn `render` của nó.

### `<script>` {#script}

- Mỗi file `*.vue` có thể chứa tối đa một khối `<script>` (ngoại trừ [`<script setup>`](/api/sfc-script-setup)).

- Script được thực thi như một ES Module.

- **Default export** nên là một options object Vue component, có thể là plain object hoặc là giá trị trả về của [defineComponent](/api/general#definecomponent).

### `<script setup>` {#script-setup}

- Mỗi file `*.vue` có thể chứa tối đa một khối `<script setup>` (ngoại trừ `<script>` thông thường).

- Script được xử lý trước và dùng như hàm `setup()` của component, nghĩa là nó sẽ được thực thi **cho mỗi instance của component**. Các binding cấp cao nhất trong `<script setup>` tự động được expose cho template. Để biết thêm chi tiết, xem [tài liệu dành riêng cho `<script setup>`](/api/sfc-script-setup).

### `<style>` {#style}

- Một file `*.vue` có thể chứa nhiều tag `<style>`.

- Một tag `<style>` có thể có các thuộc tính `scoped` hoặc `module` (xem [Tính năng Style SFC](/api/sfc-css-features) để biết thêm chi tiết) để giúp đóng gói style vào component hiện tại. Nhiều tag `<style>` với các chế độ đóng gói khác nhau có thể được pha trộn trong cùng một component.

### Khối Tùy chỉnh {#custom-blocks}

Các khối tùy chỉnh bổ sung có thể được đưa vào file `*.vue` cho bất kỳ nhu cầu cụ thể của dự án nào, ví dụ: khối `<docs>`. Một số ví dụ thực tế về các khối tùy chỉnh bao gồm:

- [Gridsome: `<page-query>`](https://gridsome.org/docs/querying-data/)
- [vite-plugin-vue-gql: `<gql>`](https://github.com/wheatjs/vite-plugin-vue-gql)
- [vue-i18n: `<i18n>`](https://github.com/intlify/bundle-tools/tree/main/packages/unplugin-vue-i18n#i18n-custom-block)

Việc xử lý Khối Tùy chỉnh sẽ phụ thuộc vào tooling - nếu bạn muốn xây dựng tích hợp khối tùy chỉnh của riêng mình, xem [phần tooling tích hợp khối tùy chỉnh SFC](/guide/scaling-up/tooling#sfc-custom-block-integrations) để biết thêm chi tiết.

## Suy luận Tên Tự động {#automatic-name-inference}

Một SFC tự động suy luận tên của component từ **tên file** trong các trường hợp sau:

- Định dạng cảnh báo dev
- Kiểm tra DevTools
- Tự tham chiếu đệ quy, ví dụ: file có tên `FooBar.vue` có thể tự tham chiếu như `<FooBar/>` trong template của nó. Điều này có độ ưu tiên thấp hơn các component đã đăng ký/import rõ ràng.

## Pre-Processor {#pre-processors}

Các khối có thể khai báo ngôn ngữ pre-processor bằng thuộc tính `lang`. Trường hợp phổ biến nhất là dùng TypeScript cho khối `<script>`:

```vue-html
<script lang="ts">
  // dùng TypeScript
</script>
```

`lang` có thể áp dụng cho bất kỳ khối nào - ví dụ chúng ta có thể dùng `<style>` với [Sass](https://sass-lang.com/) và `<template>` với [Pug](https://pugjs.org/api/getting-started.html):

```vue-html
<template lang="pug">
p {{ msg }}
</template>

<style lang="scss">
  $primary-color: #333;
  body {
    color: $primary-color;
  }
</style>
```

Lưu ý rằng việc tích hợp với các pre-processor khác nhau có thể khác nhau tùy theo toolchain. Kiểm tra tài liệu tương ứng để xem ví dụ:

- [Vite](https://vite.dev/guide/features.html#css-pre-processors)
- [Vue CLI](https://cli.vuejs.org/guide/css.html#pre-processors)
- [webpack + vue-loader](https://vue-loader.vuejs.org/guide/pre-processors.html#using-pre-processors)

## Import `src` {#src-imports}

Nếu bạn muốn tách các component `*.vue` thành nhiều file, bạn có thể dùng thuộc tính `src` để import một file bên ngoài cho một khối ngôn ngữ:

```vue
<template src="./template.html"></template>
<style src="./style.css"></style>
<script src="./script.js"></script>
```

Lưu ý các import `src` tuân theo các quy tắc phân giải đường dẫn giống như webpack module request, nghĩa là:

- Đường dẫn tương đối cần bắt đầu bằng `./`
- Bạn có thể import tài nguyên từ npm dependencies:

```vue
<!-- import file từ package npm "todomvc-app-css" đã cài đặt -->
<style src="todomvc-app-css/index.css" />
```

Import `src` cũng hoạt động với các khối tùy chỉnh, ví dụ:

```vue
<unit-test src="./unit-test.js">
</unit-test>
```

:::warning Lưu ý
Khi dùng alias trong `src`, đừng bắt đầu bằng `~`, tất cả phần sau đó được hiểu là module request. Điều này có nghĩa là bạn có thể tham chiếu tài nguyên bên trong node_modules:
```vue
<img src="~some-npm-package/foo.png">
```
:::

## Chú thích {#comments}

Bên trong mỗi khối bạn phải dùng cú pháp chú thích của ngôn ngữ đang được dùng (HTML, CSS, JavaScript, Pug, v.v.). Cho các chú thích cấp cao nhất, dùng cú pháp chú thích HTML: `<!-- nội dung chú thích ở đây -->`
