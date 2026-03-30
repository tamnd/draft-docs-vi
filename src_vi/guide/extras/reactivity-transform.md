# Reactivity Transform {#reactivity-transform}

:::danger Tính năng thử nghiệm đã bị gỡ bỏ
Reactivity Transform là một tính năng thử nghiệm và đã bị gỡ trong bản phát hành 3.4 mới nhất. Hãy đọc [lý do tại đây](https://github.com/vuejs/rfcs/discussions/369#discussioncomment-5059028).

Nếu bạn vẫn muốn dùng nó, hiện tại nó khả dụng thông qua plugin [Vue Macros](https://vue-macros.sxzz.moe/features/reactivity-transform.html).
:::

:::tip Chỉ dành cho Composition API
Reactivity Transform là một tính năng riêng của Composition API và yêu cầu có build step.
:::

## Ref So Với Biến Reactive {#refs-vs-reactive-variables}

Kể từ khi Composition API ra đời, một trong những câu hỏi lớn chưa được giải quyết triệt để là nên dùng ref hay object reactive. Ta rất dễ làm mất tính phản ứng khi destructure object reactive, trong khi việc phải dùng `.value` khắp nơi khi dùng ref lại khá phiền. Ngoài ra, nếu không dùng hệ kiểu thì rất dễ bỏ sót `.value`.

[Vue Reactivity Transform](https://github.com/vuejs/core/tree/main/packages/reactivity-transform) là một phép biến đổi ở thời điểm biên dịch cho phép ta viết mã như sau:

```vue
<script setup>
let count = $ref(0)

console.log(count)

function increment() {
  count++
}
</script>

<template>
  <button @click="increment">{{ count }}</button>
</template>
```

Phương thức `$ref()` ở đây là một **macro ở thời điểm biên dịch**: nó không phải là phương thức thật sẽ được gọi lúc chạy. Thay vào đó, trình biên dịch Vue dùng nó như một gợi ý để coi biến `count` tạo ra là một **biến reactive**.

Biến reactive có thể được truy cập và gán lại giống hệt biến thường, nhưng các thao tác đó sẽ được biên dịch thành ref với `.value`. Ví dụ, phần `<script>` của component trên sẽ được biên dịch thành:

```js{5,8}
import { ref } from 'vue'

let count = ref(0)

console.log(count.value)

function increment() {
  count.value++
}
```

Mọi API phản ứng trả về ref đều sẽ có phiên bản macro tương đương với tiền tố `$`. Các API này bao gồm:

- [`ref`](/api/reactivity-core#ref) -> `$ref`
- [`computed`](/api/reactivity-core#computed) -> `$computed`
- [`shallowRef`](/api/reactivity-advanced#shallowref) -> `$shallowRef`
- [`customRef`](/api/reactivity-advanced#customref) -> `$customRef`
- [`toRef`](/api/reactivity-utilities#toref) -> `$toRef`

Những macro này có sẵn toàn cục và không cần import khi Reactivity Transform được bật, nhưng nếu muốn tường minh hơn, bạn vẫn có thể import chúng từ `vue/macros`:

```js
import { $ref } from 'vue/macros'

let count = $ref(0)
```

## Destructure Với `$()` {#destructuring-with}

Rất thường gặp trường hợp một composable trả về object gồm các ref, sau đó ta dùng destructuring để lấy các ref đó ra. Cho mục đích này, reactivity transform cung cấp macro **`$()`**:

```js
import { useMouse } from '@vueuse/core'

const { x, y } = $(useMouse())

console.log(x, y)
```

Kết quả biên dịch:

```js
import { toRef } from 'vue'
import { useMouse } from '@vueuse/core'

const __temp = useMouse(),
  x = toRef(__temp, 'x'),
  y = toRef(__temp, 'y')

console.log(x.value, y.value)
```

Lưu ý rằng nếu `x` vốn đã là ref, `toRef(__temp, 'x')` sẽ trả về chính nó và không tạo thêm ref mới. Nếu giá trị được destructure không phải ref (ví dụ một hàm), nó vẫn hoạt động: giá trị đó sẽ được bọc lại trong ref để phần còn lại của mã tiếp tục hoạt động như mong đợi.

Việc destructure bằng `$()` hoạt động trên cả object reactive **lẫn** object thường chứa ref.

## Chuyển Ref Có Sẵn Thành Biến Reactive Bằng `$()` {#convert-existing-refs-to-reactive-variables-with}

Trong một số trường hợp, ta có thể có các hàm bao bọc cũng trả về ref. Tuy nhiên, trình biên dịch Vue sẽ không thể biết trước rằng một hàm nào đó sẽ trả về ref. Trong trường hợp như vậy, macro `$()` cũng có thể được dùng để chuyển bất kỳ ref hiện có nào thành biến reactive:

```js
function myCreateRef() {
  return ref(0)
}

let count = $(myCreateRef())
```

## Destructure Props Reactive {#reactive-props-destructure}

Hiện tại có hai điểm bất tiện khi dùng `defineProps()` trong `<script setup>`:

1. Tương tự `.value`, bạn phải luôn truy cập props dưới dạng `props.x` để giữ tính phản ứng. Điều đó có nghĩa là bạn không thể destructure kết quả của `defineProps`, vì những biến tạo ra sau khi destructure không reactive và sẽ không cập nhật.

2. Khi dùng [khai báo props chỉ bằng kiểu](/api/sfc-script-setup#type-only-props-emit-declarations), không có cách dễ dàng để khai báo giá trị mặc định cho props. Ta đã giới thiệu API `withDefaults()` chính vì mục đích này, nhưng nó vẫn khá cồng kềnh.

Ta có thể giải quyết những vấn đề đó bằng cách áp dụng một phép biến đổi ở thời điểm biên dịch khi `defineProps` được dùng cùng destructuring, tương tự như điều ta vừa thấy với `$()`:

```html
<script setup lang="ts">
  interface Props {
    msg: string
    count?: number
    foo?: string
  }

  const {
    msg,
    // giá trị mặc định hoạt động trực tiếp
    count = 1,
    // đặt bí danh cục bộ cũng hoạt động trực tiếp
    // ở đây ta đổi `props.foo` thành `bar`
    foo: bar
  } = defineProps<Props>()

  watchEffect(() => {
    // sẽ log ra mỗi khi props thay đổi
    console.log(msg, count, bar)
  })
</script>
```

Đoạn trên sẽ được biên dịch thành khai báo runtime tương đương như sau:

```js
export default {
  props: {
    msg: { type: String, required: true },
    count: { type: Number, default: 1 },
    foo: String
  },
  setup(props) {
    watchEffect(() => {
      console.log(props.msg, props.count, props.foo)
    })
  }
}
```

## Giữ Tính Phản Ứng Qua Ranh Giới Hàm {#retaining-reactivity-across-function-boundaries}

Dù biến reactive giúp ta khỏi phải viết `.value` ở mọi nơi, nó lại tạo ra vấn đề "mất tính phản ứng" khi ta truyền biến reactive qua ranh giới hàm. Điều này có thể xảy ra trong hai trường hợp:

### Truyền Vào Hàm Dưới Dạng Đối Số {#passing-into-function-as-argument}

Giả sử có một hàm chờ ref làm đối số, ví dụ:

```ts
function trackChange(x: Ref<number>) {
  watch(x, (x) => {
    console.log('x changed!')
  })
}

let count = $ref(0)
trackChange(count) // không hoạt động!
```

Trường hợp trên sẽ không hoạt động như mong đợi vì nó được biên dịch thành:

```ts
let count = ref(0)
trackChange(count.value)
```

Ở đây `count.value` được truyền đi như một con số, trong khi `trackChange` lại chờ một ref thật sự. Có thể sửa bằng cách bọc `count` trong `$$()` trước khi truyền vào:

```diff
let count = $ref(0)
- trackChange(count)
+ trackChange($$(count))
```

Đoạn trên được biên dịch thành:

```js
import { ref } from 'vue'

let count = ref(0)
trackChange(count)
```

Như ta thấy, `$$()` là một macro đóng vai trò **gợi ý thoát**: các biến reactive nằm bên trong `$$()` sẽ không bị thêm `.value`.

### Trả Về Bên Trong Phạm Vi Hàm {#returning-inside-function-scope}

Tính phản ứng cũng có thể bị mất nếu biến reactive được dùng trực tiếp trong biểu thức trả về:

```ts
function useMouse() {
  let x = $ref(0)
  let y = $ref(0)

  // lắng nghe mousemove...

  // không hoạt động!
  return {
    x,
    y
  }
}
```

Lệnh return ở trên sẽ được biên dịch thành:

```ts
return {
  x: x.value,
  y: y.value
}
```

Để giữ tính phản ứng, ta cần trả về ref thật sự, chứ không phải giá trị hiện tại tại thời điểm return.

Một lần nữa, ta có thể dùng `$$()` để sửa điều này. Trong trường hợp này, `$$()` có thể được dùng trực tiếp trên object được trả về. Mọi tham chiếu tới biến reactive bên trong lời gọi `$$()` sẽ giữ lại tham chiếu đến ref gốc của chúng:

```ts
function useMouse() {
  let x = $ref(0)
  let y = $ref(0)

  // lắng nghe mousemove...

  // đã sửa
  return $$({
    x,
    y
  })
}
```

### Dùng `$$()` Với Props Đã Destructure {#using-on-destructured-props}

`$$()` cũng hoạt động với props đã destructure vì chúng cũng là biến reactive. Trình biên dịch sẽ chuyển nó thành `toRef` để đạt hiệu quả tốt hơn:

```ts
const { count } = defineProps<{ count: number }>()

passAsRef($$(count))
```

Sẽ được biên dịch thành:

```js
setup(props) {
  const __props_count = toRef(props, 'count')
  passAsRef(__props_count)
}
```

## Tích Hợp TypeScript <sup class="vt-badge ts" /> {#typescript-integration}

Vue cung cấp kiểu cho các macro này (khả dụng toàn cục) và mọi kiểu sẽ hoạt động như mong đợi. Không có sự không tương thích nào với ngữ nghĩa TypeScript tiêu chuẩn, nên cú pháp này sẽ hoạt động với toàn bộ hệ công cụ hiện có.

Điều này cũng có nghĩa là các macro này có thể hoạt động trong mọi file cho phép JS / TS hợp lệ, chứ không chỉ trong Vue SFC.

Vì các macro này khả dụng toàn cục, kiểu của chúng cần được tham chiếu một cách tường minh (ví dụ trong file `env.d.ts`):

```ts
/// <reference types="vue/macros-global" />
```

Khi import tường minh macro từ `vue/macros`, kiểu sẽ hoạt động mà không cần khai báo các global đó.

## Opt-in Tường Minh {#explicit-opt-in}

:::danger Không còn được hỗ trợ trong core
Phần dưới đây chỉ áp dụng tới Vue 3.3 trở xuống. Hỗ trợ đã bị gỡ khỏi Vue core từ 3.4 trở lên, cũng như khỏi `@vitejs/plugin-vue` 5.0 trở lên. Nếu bạn muốn tiếp tục dùng phép biến đổi này, hãy chuyển sang [Vue Macros](https://vue-macros.sxzz.moe/features/reactivity-transform.html).
:::

### Vite {#vite}

- Yêu cầu `@vitejs/plugin-vue@>=2.0.0`
- Áp dụng cho SFC và file js(x)/ts(x). Một bước kiểm tra sử dụng nhanh sẽ được chạy trên file trước khi áp dụng phép biến đổi nên sẽ không có chi phí hiệu năng cho những file không dùng macro.
- Lưu ý rằng `reactivityTransform` giờ là một option ở cấp root của plugin thay vì lồng trong `script.refSugar`, vì nó không chỉ ảnh hưởng tới SFC.

```js [vite.config.js]
export default {
  plugins: [
    vue({
      reactivityTransform: true
    })
  ]
}
```

### `vue-cli` {#vue-cli}

- Hiện tại chỉ ảnh hưởng tới SFC
- Yêu cầu `vue-loader@>=17.0.0`

```js [vue.config.js]
module.exports = {
  chainWebpack: (config) => {
    config.module
      .rule('vue')
      .use('vue-loader')
      .tap((options) => {
        return {
          ...options,
          reactivityTransform: true
        }
      })
  }
}
```

### `webpack` Thuần + `vue-loader` {#plain-webpack-vue-loader}

- Hiện tại chỉ ảnh hưởng tới SFC
- Yêu cầu `vue-loader@>=17.0.0`

```js [webpack.config.js]
module.exports = {
  module: {
    rules: [
      {
        test: /\.vue$/,
        loader: 'vue-loader',
        options: {
          reactivityTransform: true
        }
      }
    ]
  }
}
```
