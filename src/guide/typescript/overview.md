---
outline: deep
---

# Dùng Vue với TypeScript {#using-vue-with-typescript}

Hệ thống kiểu như TypeScript có thể phát hiện nhiều lỗi phổ biến qua phân tích tĩnh tại thời điểm build. Điều này giảm thiểu lỗi runtime trong production, đồng thời cho phép tái cấu trúc code tự tin hơn trong các ứng dụng lớn. TypeScript cũng cải thiện trải nghiệm lập trình nhờ tự động hoàn thành dựa trên kiểu trong IDE.

Vue được viết bằng TypeScript và cung cấp hỗ trợ TypeScript hạng nhất. Tất cả package Vue chính thức đều đi kèm khai báo kiểu sẵn có, hoạt động ngay mà không cần cấu hình thêm.

## Cài đặt Dự án {#project-setup}

[`create-vue`](https://github.com/vuejs/create-vue), công cụ khởi tạo dự án chính thức, cung cấp tùy chọn để tạo dự án Vue dùng [Vite](https://vite.dev/) và sẵn sàng TypeScript.

### Tổng quan {#overview}

Với cài đặt dựa trên Vite, dev server và bundler chỉ thực hiện transpile, không kiểm tra kiểu. Điều này đảm bảo Vite dev server luôn chạy nhanh ngay cả khi dùng TypeScript.

- Trong quá trình phát triển, nên dựa vào [cài đặt IDE](#ide-support) tốt để nhận phản hồi tức thì về lỗi kiểu.

- Nếu dùng SFC, hãy dùng tiện ích [`vue-tsc`](https://github.com/vuejs/language-tools/tree/master/packages/tsc) để kiểm tra kiểu và sinh khai báo kiểu qua command line. `vue-tsc` là wrapper của `tsc`, giao diện command line của TypeScript. Nó hoạt động tương tự `tsc` nhưng hỗ trợ thêm Vue SFC ngoài file TypeScript. Bạn có thể chạy `vue-tsc` ở chế độ watch song song với Vite dev server, hoặc dùng Vite plugin như [vite-plugin-checker](https://vite-plugin-checker.netlify.app/) chạy kiểm tra trên một worker thread riêng.

- Vue CLI cũng hỗ trợ TypeScript, nhưng không còn được khuyến nghị nữa. Xem [ghi chú bên dưới](#note-on-vue-cli-and-ts-loader).

### Hỗ trợ IDE {#ide-support}

- [Visual Studio Code](https://code.visualstudio.com/) (VS Code) được khuyến nghị mạnh mẽ nhờ hỗ trợ TypeScript tốt sẵn có.

  - [Vue - Official](https://marketplace.visualstudio.com/items?itemName=Vue.volar) (trước đây là Volar) là extension VS Code chính thức cung cấp hỗ trợ TypeScript bên trong Vue SFC, cùng nhiều tính năng hữu ích khác.

    :::tip
    Extension Vue - Official thay thế [Vetur](https://marketplace.visualstudio.com/items?itemName=octref.vetur), extension VS Code chính thức trước đây cho Vue 2. Nếu bạn đang cài Vetur, hãy tắt nó trong các dự án Vue 3.
    :::

- [WebStorm](https://www.jetbrains.com/webstorm/) cũng hỗ trợ sẵn cả TypeScript lẫn Vue. Các IDE JetBrains khác cũng hỗ trợ, hoặc sẵn có hoặc qua [plugin miễn phí](https://plugins.jetbrains.com/plugin/9442-vue-js). Từ phiên bản 2023.2, WebStorm và Vue Plugin tích hợp sẵn Vue Language Server. Bạn có thể cấu hình dịch vụ Vue dùng tích hợp Volar cho mọi phiên bản TypeScript, trong Settings > Languages & Frameworks > TypeScript > Vue. Mặc định, Volar được dùng cho TypeScript phiên bản 5.0 trở lên.

### Cấu hình `tsconfig.json` {#configuring-tsconfig-json}

Các dự án tạo bằng `create-vue` đã có `tsconfig.json` được cấu hình sẵn. Cấu hình cơ sở được trừu tượng hóa trong package [`@vue/tsconfig`](https://github.com/vuejs/tsconfig). Bên trong dự án, ta dùng [Project References](https://www.typescriptlang.org/docs/handbook/project-references.html) để đảm bảo kiểu đúng cho code chạy trong các môi trường khác nhau (ví dụ: code ứng dụng và code test nên có global variables khác nhau).

Khi cấu hình `tsconfig.json` thủ công, một số option đáng chú ý:

- [`compilerOptions.isolatedModules`](https://www.typescriptlang.org/tsconfig#isolatedModules) được đặt thành `true` vì Vite dùng [esbuild](https://esbuild.github.io/) để transpile TypeScript và bị giới hạn bởi việc transpile từng file riêng lẻ. [`compilerOptions.verbatimModuleSyntax`](https://www.typescriptlang.org/tsconfig#verbatimModuleSyntax) là [superset của `isolatedModules`](https://github.com/microsoft/TypeScript/issues/53601) và cũng là lựa chọn tốt - đây là cái mà [`@vue/tsconfig`](https://github.com/vuejs/tsconfig) sử dụng.

- Nếu dùng Options API, bạn cần đặt [`compilerOptions.strict`](https://www.typescriptlang.org/tsconfig#strict) thành `true` (hoặc ít nhất bật [`compilerOptions.noImplicitThis`](https://www.typescriptlang.org/tsconfig#noImplicitThis), là một phần của flag `strict`) để tận dụng kiểm tra kiểu cho `this` trong component options. Nếu không, `this` sẽ bị coi là `any`.

- Nếu bạn đã cấu hình resolver alias trong build tool, ví dụ alias `@/*` được cấu hình mặc định trong dự án `create-vue`, bạn cần cấu hình thêm cho TypeScript qua [`compilerOptions.paths`](https://www.typescriptlang.org/tsconfig#paths).

- Nếu bạn dự định dùng TSX với Vue, đặt [`compilerOptions.jsx`](https://www.typescriptlang.org/tsconfig#jsx) thành `"preserve"` và đặt [`compilerOptions.jsxImportSource`](https://www.typescriptlang.org/tsconfig#jsxImportSource) thành `"vue"`.

Xem thêm:

- [Tài liệu chính thức về compiler options TypeScript](https://www.typescriptlang.org/docs/handbook/compiler-options.html)
- [Lưu ý về biên dịch TypeScript của esbuild](https://esbuild.github.io/content-types/#typescript-caveats)

### Ghi chú về Vue CLI và `ts-loader` {#note-on-vue-cli-and-ts-loader}

Trong các cài đặt dựa trên webpack như Vue CLI, thông thường người ta kiểm tra kiểu như một phần của pipeline transform module, ví dụ dùng `ts-loader`. Tuy nhiên, đây không phải giải pháp gọn gàng vì hệ thống kiểu cần biết toàn bộ module graph để thực hiện kiểm tra kiểu. Bước transform của từng module đơn lẻ đơn giản không phải nơi phù hợp cho tác vụ này. Điều này dẫn đến các vấn đề sau:

- `ts-loader` chỉ có thể kiểm tra kiểu code sau khi transform. Điều này không khớp với lỗi ta thấy trong IDE hay từ `vue-tsc`, vốn ánh xạ trực tiếp về source code gốc.

- Kiểm tra kiểu có thể chậm. Khi thực hiện trong cùng thread/process với biến đổi code, nó ảnh hưởng đáng kể đến tốc độ build của toàn ứng dụng.

- Ta đã có kiểm tra kiểu chạy ngay trong IDE trong một process riêng, nên chi phí làm chậm trải nghiệm dev đơn giản không đáng đổi.

Nếu bạn đang dùng Vue 3 + TypeScript qua Vue CLI, chúng tôi khuyến nghị mạnh mẽ chuyển sang Vite. Chúng tôi cũng đang làm việc trên các tùy chọn CLI để bật hỗ trợ TypeScript chỉ transpile, để bạn có thể chuyển sang `vue-tsc` cho kiểm tra kiểu.

## Ghi chú Sử dụng Chung {#general-usage-notes}

### `defineComponent()` {#definecomponent}

Để TypeScript suy luận đúng kiểu bên trong component options, ta cần định nghĩa component với [`defineComponent()`](/api/general#definecomponent):

```ts
import { defineComponent } from 'vue'

export default defineComponent({
  // bật suy luận kiểu
  props: {
    name: String,
    msg: { type: String, required: true }
  },
  data() {
    return {
      count: 1
    }
  },
  mounted() {
    this.name // kiểu: string | undefined
    this.msg // kiểu: string
    this.count // kiểu: number
  }
})
```

`defineComponent()` cũng hỗ trợ suy luận các props truyền vào `setup()` khi dùng Composition API mà không có `<script setup>`:

```ts
import { defineComponent } from 'vue'

export default defineComponent({
  // bật suy luận kiểu
  props: {
    message: String
  },
  setup(props) {
    props.message // kiểu: string | undefined
  }
})
```

Xem thêm:

- [Ghi chú về Treeshaking trong webpack](/api/general#note-on-webpack-treeshaking)
- [Type tests cho `defineComponent`](https://github.com/vuejs/core/blob/main/packages-private/dts-test/defineComponent.test-d.tsx)

:::tip
`defineComponent()` cũng cho phép suy luận kiểu cho các component được định nghĩa bằng JavaScript thuần.
:::

### Dùng trong Single-File Component {#usage-in-single-file-components}

Để dùng TypeScript trong SFC, thêm thuộc tính `lang="ts"` vào thẻ `<script>`. Khi có `lang="ts"`, tất cả biểu thức trong template cũng được kiểm tra kiểu chặt chẽ hơn.

```vue
<script lang="ts">
import { defineComponent } from 'vue'

export default defineComponent({
  data() {
    return {
      count: 1
    }
  }
})
</script>

<template>
  <!-- bật kiểm tra kiểu và tự động hoàn thành -->
  {{ count.toFixed(2) }}
</template>
```

`lang="ts"` cũng có thể dùng với `<script setup>`:

```vue
<script setup lang="ts">
// TypeScript được bật
import { ref } from 'vue'

const count = ref(1)
</script>

<template>
  <!-- bật kiểm tra kiểu và tự động hoàn thành -->
  {{ count.toFixed(2) }}
</template>
```

### TypeScript trong Template {#typescript-in-templates}

`<template>` cũng hỗ trợ TypeScript trong các biểu thức binding khi dùng `<script lang="ts">` hoặc `<script setup lang="ts">`. Tính năng này hữu ích khi bạn cần thực hiện ép kiểu trong biểu thức template.

Đây là một ví dụ minh họa:

```vue
<script setup lang="ts">
let x: string | number = 1
</script>

<template>
  <!-- lỗi vì x có thể là string -->
  {{ x.toFixed(2) }}
</template>
```

Có thể khắc phục bằng cách ép kiểu inline:

```vue{6}
<script setup lang="ts">
let x: string | number = 1
</script>

<template>
  {{ (x as number).toFixed(2) }}
</template>
```

:::tip
Nếu dùng Vue CLI hoặc cài đặt dựa trên webpack, TypeScript trong biểu thức template yêu cầu `vue-loader@^16.8.0`.
:::

### Dùng với TSX {#usage-with-tsx}

Vue cũng hỗ trợ viết component với JSX / TSX. Chi tiết được trình bày trong hướng dẫn [Render Function & JSX](/guide/extras/render-function.html#jsx-tsx).

## Generic Component {#generic-components}

Generic component được hỗ trợ trong hai trường hợp:

- Trong SFC: [`<script setup>` với thuộc tính `generic`](/api/sfc-script-setup.html#generics)
- Component dùng render function / JSX: [chữ ký hàm của `defineComponent()`](/api/general.html#function-signature)

## Hướng dẫn Riêng theo API {#api-specific-recipes}

- [TypeScript với Composition API](./composition-api)
- [TypeScript với Options API](./options-api)
