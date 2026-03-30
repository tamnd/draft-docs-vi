# \<script setup> {#script-setup}

`<script setup>` là cú pháp đường tắt compile-time để dùng Composition API trong Single-File Component (SFC). Đây là cú pháp được khuyến nghị nếu bạn đang dùng cả SFC lẫn Composition API. Nó cung cấp một số ưu điểm so với cú pháp `<script>` thông thường:

- Code ngắn gọn hơn, ít boilerplate hơn
- Khả năng khai báo props và emitted event dùng TypeScript thuần túy
- Hiệu suất runtime tốt hơn (template được biên dịch thành render function trong cùng scope, không cần proxy trung gian)
- Hiệu suất suy luận kiểu IDE tốt hơn (ít công việc hơn cho language server để trích xuất kiểu từ code)

## Cú pháp Cơ bản {#basic-syntax}

Để bật cú pháp này, thêm thuộc tính `setup` vào khối `<script>`:

```vue
<script setup>
console.log('hello script setup')
</script>
```

Code bên trong được biên dịch như nội dung của hàm `setup()` của component. Điều này có nghĩa là khác với `<script>` thông thường chỉ thực thi một lần khi component được import lần đầu, code bên trong `<script setup>` sẽ **thực thi mỗi khi một instance của component được tạo**.

### Các binding cấp cao nhất được expose cho template {#top-level-bindings-are-exposed-to-template}

Khi dùng `<script setup>`, bất kỳ binding cấp cao nhất nào (bao gồm biến, khai báo hàm, và import) được khai báo bên trong `<script setup>` đều có thể dùng trực tiếp trong template:

```vue
<script setup>
// biến
const msg = 'Hello!'

// hàm
function log() {
  console.log(msg)
}
</script>

<template>
  <button @click="log">{{ msg }}</button>
</template>
```

Import cũng được expose theo cách tương tự. Điều này có nghĩa là bạn có thể dùng trực tiếp một helper function đã import trong template expression mà không cần expose nó qua tùy chọn `methods`:

```vue
<script setup>
import { capitalize } from './helpers'
</script>

<template>
  <div>{{ capitalize('hello') }}</div>
</template>
```

## Tính Phản ứng {#reactivity}

State phản ứng cần được tạo rõ ràng bằng cách dùng [Reactivity API](./reactivity-core). Tương tự các giá trị được trả về từ hàm `setup()`, ref được tự động unwrap khi được tham chiếu trong template:

```vue
<script setup>
import { ref } from 'vue'

const count = ref(0)
</script>

<template>
  <button @click="count++">{{ count }}</button>
</template>
```

## Dùng Component {#using-components}

Các giá trị trong scope của `<script setup>` cũng có thể dùng trực tiếp như tên tag custom component:

```vue
<script setup>
import MyComponent from './MyComponent.vue'
</script>

<template>
  <MyComponent />
</template>
```

Hãy coi `MyComponent` như được tham chiếu như một biến. Nếu bạn đã dùng JSX, mô hình tư duy ở đây tương tự. Tên kebab-case tương đương `<my-component>` cũng hoạt động trong template - tuy nhiên tag component PascalCase được khuyến nghị mạnh mẽ để nhất quán. Nó cũng giúp phân biệt với custom element native.

### Component Động {#dynamic-components}

Vì component được tham chiếu như biến thay vì đăng ký theo string key, chúng ta nên dùng binding `:is` động khi dùng dynamic component bên trong `<script setup>`:

```vue
<script setup>
import Foo from './Foo.vue'
import Bar from './Bar.vue'
</script>

<template>
  <component :is="Foo" />
  <component :is="someCondition ? Foo : Bar" />
</template>
```

Lưu ý cách component có thể dùng như biến trong biểu thức ternary.

### Component Đệ quy {#recursive-components}

Một SFC có thể ngầm tự tham chiếu qua tên file của nó. Ví dụ: file có tên `FooBar.vue` có thể tự tham chiếu như `<FooBar/>` trong template của nó.

Lưu ý điều này có độ ưu tiên thấp hơn component đã import. Nếu bạn có named import trùng với tên được suy luận của component, bạn có thể alias import:

```js
import { FooBar as FooBarChild } from './components'
```

### Component Namespace {#namespaced-components}

Bạn có thể dùng tag component với dấu chấm như `<Foo.Bar>` để tham chiếu các component lồng bên dưới các thuộc tính object. Điều này hữu ích khi bạn import nhiều component từ một file duy nhất:

```vue
<script setup>
import * as Form from './form-components'
</script>

<template>
  <Form.Input>
    <Form.Label>label</Form.Label>
  </Form.Input>
</template>
```

## Dùng Custom Directive {#using-custom-directives}

Custom directive đã đăng ký toàn cục hoạt động bình thường. Custom directive cục bộ không cần đăng ký rõ ràng với `<script setup>`, nhưng chúng phải tuân theo quy tắc đặt tên `vTênDirective`:

```vue
<script setup>
const vMyDirective = {
  beforeMount: (el) => {
    // làm gì đó với element
  }
}
</script>
<template>
  <h1 v-my-directive>Đây là Tiêu đề</h1>
</template>
```

Nếu bạn import directive từ chỗ khác, nó có thể được đổi tên để phù hợp với quy tắc đặt tên yêu cầu:

```vue
<script setup>
import { myDirective as vMyDirective } from './MyDirective.js'
</script>
```

## defineProps() & defineEmits() {#defineprops-defineemits}

Để khai báo các tùy chọn như `props` và `emits` với hỗ trợ suy luận kiểu đầy đủ, chúng ta có thể dùng các API `defineProps` và `defineEmits`, tự động có sẵn bên trong `<script setup>`:

```vue
<script setup>
const props = defineProps({
  foo: String
})

const emit = defineEmits(['change', 'delete'])
// code setup
</script>
```

- `defineProps` và `defineEmits` là **compiler macro** chỉ có thể dùng bên trong `<script setup>`. Chúng không cần được import, và được biên dịch bỏ đi khi `<script setup>` được xử lý.

- `defineProps` chấp nhận cùng giá trị với tùy chọn `props`, trong khi `defineEmits` chấp nhận cùng giá trị với tùy chọn `emits`.

- `defineProps` và `defineEmits` cung cấp suy luận kiểu phù hợp dựa trên các tùy chọn được truyền vào.

- Các tùy chọn được truyền vào `defineProps` và `defineEmits` sẽ được hoist ra ngoài setup vào module scope. Do đó, các tùy chọn không thể tham chiếu các biến cục bộ được khai báo trong setup scope. Làm vậy sẽ dẫn đến compile error. Tuy nhiên, nó _có thể_ tham chiếu các binding đã import vì chúng cũng trong module scope.

### Khai báo props/emit chỉ kiểu<sup class="vt-badge ts" /> {#type-only-props-emit-declarations}

Props và emit cũng có thể được khai báo dùng cú pháp pure-type bằng cách truyền một literal type argument vào `defineProps` hoặc `defineEmits`:

```ts
const props = defineProps<{
  foo: string
  bar?: number
}>()

const emit = defineEmits<{
  (e: 'change', id: number): void
  (e: 'update', value: string): void
}>()

// 3.3+: cú pháp thay thế, ngắn gọn hơn
const emit = defineEmits<{
  change: [id: number] // cú pháp named tuple
  update: [value: string]
}>()
```

- `defineProps` hoặc `defineEmits` chỉ có thể dùng khai báo runtime HOẶC khai báo type. Dùng cả hai cùng lúc sẽ dẫn đến compile error.

- Khi dùng khai báo type, khai báo runtime tương đương được tự động tạo ra từ phân tích static để bỏ nhu cầu khai báo đôi và vẫn đảm bảo hành vi runtime đúng.

  - Trong chế độ dev, compiler sẽ cố suy luận runtime validation tương ứng từ các kiểu. Ví dụ ở đây `foo: String` được suy luận từ kiểu `foo: string`. Các kiểu đã import cũng được resolve, miễn là TypeScript được cài đặt như một peer dependency.

  - Trong chế độ prod, compiler sẽ tạo khai báo dạng mảng để giảm bundle size (các props ở đây sẽ được biên dịch thành `['foo', 'bar']`)

- Trong phiên bản 3.2 trở xuống, generic type parameter cho `defineProps()` bị giới hạn ở type literal hoặc tham chiếu đến local interface.

  Giới hạn này được giải quyết trong 3.3. Phiên bản Vue mới nhất hỗ trợ tham chiếu các kiểu đã import và một tập hợp giới hạn các kiểu phức tạp trong vị trí type parameter. Tuy nhiên, vì chuyển đổi type sang runtime vẫn dựa trên AST, một số kiểu phức tạp yêu cầu phân tích kiểu thực sự, ví dụ: conditional type, không được hỗ trợ. Bạn có thể dùng conditional type cho kiểu của một prop đơn lẻ, nhưng không phải toàn bộ object props.

### Reactive Props Destructure <sup class="vt-badge" data-text="3.5+" /> {#reactive-props-destructure}

Trong Vue 3.5 trở lên, các biến được destructure từ giá trị trả về của `defineProps` là reactive. Compiler của Vue tự động thêm tiền tố `props.` khi code trong cùng khối `<script setup>` truy cập các biến được destructure từ `defineProps`:

```ts
const { foo } = defineProps(['foo'])

watchEffect(() => {
  // chạy chỉ một lần trước 3.5
  // chạy lại khi prop "foo" thay đổi trong 3.5+
  console.log(foo)
})
```

Code trên được biên dịch thành tương đương sau:

```js {5}
const props = defineProps(['foo'])

watchEffect(() => {
  // `foo` được compiler chuyển đổi thành `props.foo`
  console.log(props.foo)
})
```

Ngoài ra, bạn có thể dùng cú pháp giá trị mặc định native của JavaScript để khai báo giá trị mặc định cho các props. Điều này đặc biệt hữu ích khi dùng khai báo props dựa trên type:

```ts
interface Props {
  msg?: string
  labels?: string[]
}

const { msg = 'hello', labels = ['one', 'two'] } = defineProps<Props>()
```

### Giá trị mặc định props khi dùng khai báo type <sup class="vt-badge ts" /> {#default-props-values-when-using-type-declaration}

Trong 3.5 trở lên, giá trị mặc định có thể được khai báo tự nhiên khi dùng Reactive Props Destructure. Nhưng trong 3.4 trở xuống, Reactive Props Destructure không được bật mặc định. Để khai báo giá trị mặc định props với khai báo dựa trên type, cần dùng compiler macro `withDefaults`:

```ts
interface Props {
  msg?: string
  labels?: string[]
}

const props = withDefaults(defineProps<Props>(), {
  msg: 'hello',
  labels: () => ['one', 'two']
})
```

Điều này sẽ được biên dịch thành tùy chọn `default` props runtime tương đương. Ngoài ra, helper `withDefaults` cung cấp kiểm tra kiểu cho các giá trị mặc định, và đảm bảo kiểu `props` được trả về có các flag tùy chọn được loại bỏ cho các thuộc tính đã có khai báo giá trị mặc định.

:::info
Lưu ý rằng các giá trị mặc định cho kiểu tham chiếu có thể thay đổi (như mảng hoặc object) nên được bọc trong hàm khi dùng `withDefaults` để tránh thay đổi ngoài ý muốn và side effect bên ngoài. Điều này đảm bảo mỗi instance component nhận bản sao riêng của giá trị mặc định. Điều này **không** cần thiết khi dùng giá trị mặc định với destructure.
:::

## defineModel() {#definemodel}

- Chỉ có sẵn trong 3.4+

Macro này có thể dùng để khai báo một prop binding hai chiều có thể sử dụng qua `v-model` từ component cha. Ví dụ sử dụng cũng được thảo luận trong hướng dẫn [Component `v-model`](/guide/components/v-model).

Bên dưới, macro này khai báo một model prop và một event cập nhật giá trị tương ứng. Nếu đối số đầu tiên là chuỗi literal, nó sẽ được dùng làm tên prop; Ngược lại tên prop sẽ mặc định là `"modelValue"`. Trong cả hai trường hợp, bạn cũng có thể truyền thêm một object có thể bao gồm các tùy chọn của prop và các tùy chọn transform giá trị của model ref.

```js
// khai báo prop "modelValue", được cha sử dụng qua v-model
const model = defineModel()
// HOẶC: khai báo prop "modelValue" với tùy chọn
const model = defineModel({ type: String })

// emit "update:modelValue" khi thay đổi
model.value = 'hello'

// khai báo prop "count", được cha sử dụng qua v-model:count
const count = defineModel('count')
// HOẶC: khai báo prop "count" với tùy chọn
const count = defineModel('count', { type: Number, default: 0 })

function inc() {
  // emit "update:count" khi thay đổi
  count.value++
}
```

:::warning
Nếu bạn có giá trị `default` cho prop `defineModel` và bạn không cung cấp bất kỳ giá trị nào cho prop này từ component cha, nó có thể gây mất đồng bộ giữa component cha và con. Trong ví dụ dưới đây, `myRef` của cha là undefined, nhưng `model` của con là 1:

```vue [Child.vue]
<script setup>
const model = defineModel({ default: 1 })
</script>
```

```vue [Parent.vue]
<script setup>
const myRef = ref()
</script>

<template>
  <Child v-model="myRef"></Child>
</template>
```

:::

### Modifier và Transformer {#modifiers-and-transformers}

Để truy cập các modifier được dùng với directive `v-model`, chúng ta có thể destructure giá trị trả về của `defineModel()` như sau:

```js
const [modelValue, modelModifiers] = defineModel()

// tương ứng với v-model.trim
if (modelModifiers.trim) {
  // ...
}
```

Khi có modifier, chúng ta có thể cần transform giá trị khi đọc hoặc đồng bộ lại cho cha. Chúng ta có thể đạt điều này bằng cách dùng các tùy chọn transformer `get` và `set`:

```js
const [modelValue, modelModifiers] = defineModel({
  // get() bỏ qua vì không cần ở đây
  set(value) {
    // nếu modifier .trim được dùng, trả về giá trị đã trim
    if (modelModifiers.trim) {
      return value.trim()
    }
    // ngược lại, trả về giá trị nguyên vẹn
    return value
  }
})
```

### Dùng với TypeScript <sup class="vt-badge ts" /> {#usage-with-typescript}

Giống như `defineProps` và `defineEmits`, `defineModel` cũng có thể nhận type argument để chỉ định kiểu của model value và modifier:

```ts
const modelValue = defineModel<string>()
//    ^? Ref<string | undefined>

// model mặc định với tùy chọn, required loại bỏ các giá trị undefined có thể
const modelValue = defineModel<string>({ required: true })
//    ^? Ref<string>

const [modelValue, modifiers] = defineModel<string, 'trim' | 'uppercase'>()
//                 ^? Record<'trim' | 'uppercase', true | undefined>
```

## defineExpose() {#defineexpose}

Các component dùng `<script setup>` là **đóng theo mặc định** - tức là public instance của component, được lấy qua template ref hoặc chuỗi `$parent`, sẽ **không** expose bất kỳ binding nào được khai báo bên trong `<script setup>`.

Để expose rõ ràng các thuộc tính trong component `<script setup>`, dùng compiler macro `defineExpose`:

```vue
<script setup>
import { ref } from 'vue'

const a = 1
const b = ref(2)

defineExpose({
  a,
  b
})
</script>
```

Khi cha nhận instance của component này qua template ref, instance được lấy sẽ có dạng `{ a: number, b: number }` (ref được tự động unwrap giống như trên các instance thông thường).

## defineOptions() {#defineoptions}

- Chỉ hỗ trợ trong 3.3+

Macro này có thể dùng để khai báo component option trực tiếp bên trong `<script setup>` mà không cần dùng một khối `<script>` riêng biệt:

```vue
<script setup>
defineOptions({
  inheritAttrs: false,
  customOptions: {
    /* ... */
  }
})
</script>
```

- Đây là macro. Các tùy chọn sẽ được hoist lên module scope và không thể truy cập các biến cục bộ trong `<script setup>` mà không phải là literal constant.

## defineSlots()<sup class="vt-badge ts"/> {#defineslots}

- Chỉ hỗ trợ trong 3.3+

Macro này có thể dùng để cung cấp type hint cho IDE để kiểm tra tên slot và kiểu props.

`defineSlots()` chỉ chấp nhận type parameter và không có runtime argument. Type parameter nên là type literal trong đó property key là tên slot, và value type là hàm slot. Đối số đầu tiên của hàm là props mà slot nhận, và kiểu của nó sẽ được dùng cho slot props trong template. Kiểu trả về hiện tại bị bỏ qua và có thể là `any`, nhưng chúng tôi có thể tận dụng nó để kiểm tra nội dung slot trong tương lai.

Nó cũng trả về object `slots`, tương đương với object `slots` được expose trên setup context hoặc được trả về bởi `useSlots()`.

```vue
<script setup lang="ts">
const slots = defineSlots<{
  default(props: { msg: string }): any
}>()
</script>
```

## `useSlots()` & `useAttrs()` {#useslots-useattrs}

Việc dùng `slots` và `attrs` bên trong `<script setup>` thường hiếm gặp, vì bạn có thể truy cập chúng trực tiếp như `$slots` và `$attrs` trong template. Trong trường hợp hiếm gặp khi bạn thực sự cần chúng, dùng helper `useSlots` và `useAttrs` tương ứng:

```vue
<script setup>
import { useSlots, useAttrs } from 'vue'

const slots = useSlots()
const attrs = useAttrs()
</script>
```

`useSlots` và `useAttrs` là các hàm runtime thực sự trả về tương đương của `setupContext.slots` và `setupContext.attrs`. Chúng cũng có thể dùng trong các hàm composition API thông thường.

## Dùng cùng với `<script>` thông thường {#usage-alongside-normal-script}

`<script setup>` có thể dùng cùng với `<script>` thông thường. Một `<script>` thông thường có thể cần trong các trường hợp chúng ta cần:

- Khai báo các tùy chọn không thể biểu đạt trong `<script setup>`, ví dụ `inheritAttrs` hoặc các tùy chọn tùy chỉnh được bật qua plugin (Có thể được thay thế bằng [`defineOptions`](/api/sfc-script-setup#defineoptions) trong 3.3+).
- Khai báo named export.
- Chạy side effect hoặc tạo object chỉ nên thực thi một lần.

```vue
<script>
// <script> thông thường, thực thi trong module scope (chỉ một lần)
runSideEffectOnce()

// khai báo thêm tùy chọn
export default {
  inheritAttrs: false,
  customOptions: {}
}
</script>

<script setup>
// thực thi trong setup() scope (cho mỗi instance)
</script>
```

Hỗ trợ kết hợp `<script setup>` và `<script>` trong cùng một component bị giới hạn ở các trường hợp được mô tả ở trên. Cụ thể:

- **KHÔNG** dùng phần `<script>` riêng biệt cho các tùy chọn đã có thể định nghĩa bằng `<script setup>`, như `props` và `emits`.
- Các biến được tạo bên trong `<script setup>` không được thêm làm thuộc tính vào component instance, khiến chúng không thể truy cập từ Options API. Pha trộn API theo cách này bị khuyến cáo không nên làm.

Nếu bạn thấy mình trong một trong các trường hợp không được hỗ trợ thì bạn nên cân nhắc chuyển sang hàm [`setup()`](/api/composition-api-setup) rõ ràng, thay vì dùng `<script setup>`.

## `await` cấp cao nhất {#top-level-await}

`await` cấp cao nhất có thể dùng bên trong `<script setup>`. Code kết quả sẽ được biên dịch thành `async setup()`:

```vue
<script setup>
const post = await fetch(`/api/post/1`).then((r) => r.json())
</script>
```

Ngoài ra, biểu thức được await sẽ được tự động biên dịch theo định dạng giữ nguyên context của component instance hiện tại sau `await`.

:::warning Lưu ý
`async setup()` phải được dùng kết hợp với [`Suspense`](/guide/built-ins/suspense.html), hiện vẫn là tính năng thử nghiệm. Chúng tôi dự định hoàn thiện và tài liệu hóa nó trong một bản release tương lai - nhưng nếu bạn tò mò ngay bây giờ, bạn có thể tham khảo [các test](https://github.com/vuejs/core/blob/main/packages/runtime-core/__tests__/components/Suspense.spec.ts) của nó để xem cách hoạt động.
:::

## Câu lệnh Import {#imports-statements}

Các câu lệnh import trong vue tuân theo [đặc tả ES module](https://nodejs.org/api/esm.html).
Ngoài ra, bạn có thể dùng các alias được định nghĩa trong cấu hình build tool của bạn:

```vue
<script setup>
import { ref } from 'vue'
import { componentA } from './Components'
import { componentB } from '@/Components'
import { componentC } from '~/Components'
</script>
```

## Generic <sup class="vt-badge ts" /> {#generics}

Generic type parameter có thể được khai báo bằng thuộc tính `generic` trên tag `<script>`:

```vue
<script setup lang="ts" generic="T">
defineProps<{
  items: T[]
  selected: T
}>()
</script>
```

Giá trị của `generic` hoạt động giống hệt danh sách parameter giữa `<...>` trong TypeScript. Ví dụ, bạn có thể dùng nhiều parameter, ràng buộc `extends`, kiểu mặc định, và tham chiếu các kiểu đã import:

```vue
<script
  setup
  lang="ts"
  generic="T extends string | number, U extends Item"
>
import type { Item } from './types'
defineProps<{
  id: T
  list: U[]
}>()
</script>
```

Bạn có thể dùng directive `@vue-generic` để truyền vào các kiểu rõ ràng, cho khi kiểu không thể được suy luận:

```vue
<template>
  <!-- @vue-generic {import('@/api').Actor} -->
  <ApiSelect v-model="peopleIds" endpoint="/api/actors" id-prop="actorId" />

  <!-- @vue-generic {import('@/api').Genre} -->
  <ApiSelect v-model="genreIds" endpoint="/api/genres" id-prop="genreId" />
</template>
```

Để dùng tham chiếu đến generic component trong `ref`, bạn cần dùng thư viện [`vue-component-type-helpers`](https://www.npmjs.com/package/vue-component-type-helpers) vì `InstanceType` sẽ không hoạt động.

```vue
<script
  setup
  lang="ts"
>
import componentWithoutGenerics from '../component-without-generics.vue';
import genericComponent from '../generic-component.vue';

import type { ComponentExposed } from 'vue-component-type-helpers';

// Hoạt động cho component không có generic
ref<InstanceType<typeof componentWithoutGenerics>>();

ref<ComponentExposed<typeof genericComponent>>();
```

## Hạn chế {#restrictions}

- Do sự khác biệt trong ngữ nghĩa thực thi module, code bên trong `<script setup>` phụ thuộc vào context của một SFC. Khi chuyển vào các file `.js` hoặc `.ts` bên ngoài, nó có thể gây nhầm lẫn cho cả developer lẫn công cụ. Do đó, **`<script setup>`** không thể dùng với thuộc tính `src`.
- `<script setup>` không hỗ trợ In-DOM Root Component Template. ([Thảo luận liên quan](https://github.com/vuejs/core/issues/8391))
