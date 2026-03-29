# TypeScript với Composition API {#typescript-with-composition-api}

<ScrimbaLink href="https://scrimba.com/links/vue-ts-composition-api" title="Free Vue.js TypeScript with Composition API Lesson" type="scrimba">
  Xem bài học video tương tác trên Scrimba
</ScrimbaLink>

> Trang này giả định rằng bạn đã đọc phần tổng quan về [Dùng Vue với TypeScript](./overview).

## Khai Báo Kiểu Cho Props Của Component {#typing-component-props}

### Dùng `<script setup>` {#using-script-setup}

Khi dùng `<script setup>`, macro `defineProps()` hỗ trợ suy luận kiểu của props dựa trên đối số được truyền vào:

```vue
<script setup lang="ts">
const props = defineProps({
  foo: { type: String, required: true },
  bar: Number
})

props.foo // string
props.bar // number | undefined
</script>
```

Cách này được gọi là "khai báo lúc chạy", vì đối số truyền cho `defineProps()` sẽ được dùng làm option `props` ở runtime.

Tuy nhiên, trong đa số trường hợp, việc khai báo props bằng kiểu thuần qua đối số kiểu generic sẽ trực tiếp hơn:

```vue
<script setup lang="ts">
const props = defineProps<{
  foo: string
  bar?: number
}>()
</script>
```

Cách này được gọi là "khai báo dựa trên kiểu". Trình biên dịch sẽ cố gắng suy luận option runtime tương đương từ đối số kiểu. Trong trường hợp này, ví dụ thứ hai sẽ được biên dịch thành đúng cùng option runtime như ví dụ đầu tiên.

Bạn có thể dùng hoặc khai báo dựa trên kiểu, hoặc khai báo lúc chạy, nhưng không thể dùng cả hai cùng lúc.

Ta cũng có thể đưa kiểu của props vào một interface riêng:

```vue
<script setup lang="ts">
interface Props {
  foo: string
  bar?: number
}

const props = defineProps<Props>()
</script>
```

Cách này cũng hoạt động nếu `Props` được import từ file khác, chẳng hạn import tương đối, path alias (ví dụ `@/types`) hoặc dependency bên ngoài (ví dụ `node_modules`). Tính năng này yêu cầu TypeScript phải là peer dependency của Vue.

```vue
<script setup lang="ts">
import type { Props } from './foo'

const props = defineProps<Props>()
</script>
```

#### Giới Hạn Cú Pháp {#syntax-limitations}

Trong phiên bản 3.2 trở xuống, tham số kiểu generic của `defineProps()` bị giới hạn ở kiểu literal hoặc tham chiếu tới interface cục bộ.

Giới hạn này đã được gỡ trong 3.3. Phiên bản Vue mới nhất hỗ trợ tham chiếu tới kiểu được import và một tập giới hạn các kiểu phức tạp trong vị trí tham số kiểu. Tuy nhiên, vì việc chuyển từ kiểu sang runtime vẫn dựa trên AST, một số kiểu phức tạp cần phân tích kiểu thực sự, ví dụ conditional type, vẫn chưa được hỗ trợ. Bạn có thể dùng conditional type cho kiểu của một prop đơn lẻ, nhưng không thể dùng cho toàn bộ object props.

### Giá Trị Mặc Định Của Props {#props-default-values}

Khi dùng khai báo dựa trên kiểu, ta sẽ mất khả năng khai báo giá trị mặc định cho props. Điều này có thể được giải quyết bằng [Reactive Props Destructure](/guide/components/props#reactive-props-destructure) <sup class="vt-badge" data-text="3.5+" />:

```ts
interface Props {
  msg?: string
  labels?: string[]
}

const { msg = 'hello', labels = ['one', 'two'] } = defineProps<Props>()
```

Trong 3.4 trở xuống, Reactive Props Destructure không được bật mặc định. Một lựa chọn khác là dùng macro biên dịch `withDefaults`:

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

Đoạn này sẽ được biên dịch thành option `default` của props ở runtime tương đương. Ngoài ra, helper `withDefaults` còn kiểm tra kiểu của các giá trị mặc định và bảo đảm kiểu `props` trả về đã bỏ cờ optional đối với những thuộc tính có khai báo giá trị mặc định.

:::info
Lưu ý rằng giá trị mặc định cho các kiểu tham chiếu có thể thay đổi được (như mảng hoặc object) nên được bọc trong hàm khi dùng `withDefaults` để tránh việc bị sửa đổi ngoài ý muốn và side effect từ bên ngoài. Cách này bảo đảm mỗi instance component nhận được bản sao giá trị mặc định của riêng nó. Điều này **không** cần thiết khi dùng giá trị mặc định với destructure.
:::

### Không Dùng `<script setup>` {#without-script-setup}

Nếu không dùng `<script setup>`, bạn cần dùng `defineComponent()` để bật suy luận kiểu cho props. Kiểu của object props truyền cho `setup()` sẽ được suy luận từ option `props`.

```ts
import { defineComponent } from 'vue'

export default defineComponent({
  props: {
    message: String
  },
  setup(props) {
    props.message // <-- kiểu: string
  }
})
```

### Kiểu Prop Phức Tạp {#complex-prop-types}

Với khai báo dựa trên kiểu, một prop có thể dùng kiểu phức tạp giống như bất kỳ kiểu nào khác:

```vue
<script setup lang="ts">
interface Book {
  title: string
  author: string
  year: number
}

const props = defineProps<{
  book: Book
}>()
</script>
```

Với khai báo lúc chạy, ta có thể dùng kiểu tiện ích `PropType`:

```ts
import type { PropType } from 'vue'

const props = defineProps({
  book: Object as PropType<Book>
})
```

Điều này cũng hoạt động tương tự nếu ta khai báo trực tiếp option `props`:

```ts
import { defineComponent } from 'vue'
import type { PropType } from 'vue'

export default defineComponent({
  props: {
    book: Object as PropType<Book>
  }
})
```

Option `props` thường được dùng nhiều hơn với Options API, nên bạn sẽ thấy ví dụ chi tiết hơn trong bài hướng dẫn [TypeScript với Options API](/guide/typescript/options-api#typing-component-props). Các kỹ thuật trong những ví dụ đó cũng áp dụng cho khai báo lúc chạy với `defineProps()`.

## Khai Báo Kiểu Cho Emits Của Component {#typing-component-emits}

Trong `<script setup>`, hàm `emit` cũng có thể được khai báo kiểu bằng khai báo lúc chạy hoặc khai báo kiểu:

```vue
<script setup lang="ts">
// runtime
const emit = defineEmits(['change', 'update'])

// dựa trên options
const emit = defineEmits({
  change: (id: number) => {
    // trả về `true` hoặc `false` để biểu thị
    // việc xác thực thành công / thất bại
  },
  update: (value: string) => {
    // trả về `true` hoặc `false` để biểu thị
    // việc xác thực thành công / thất bại
  }
})

// dựa trên kiểu
const emit = defineEmits<{
  (e: 'change', id: number): void
  (e: 'update', value: string): void
}>()

// 3.3+: cú pháp thay thế, gọn hơn
const emit = defineEmits<{
  change: [id: number]
  update: [value: string]
}>()
</script>
```

Đối số kiểu có thể là một trong các dạng sau:

1. Kiểu hàm callable, nhưng được viết dưới dạng kiểu literal với [Call Signatures](https://www.typescriptlang.org/docs/handbook/2/functions.html#call-signatures). Nó sẽ được dùng làm kiểu của hàm `emit` trả về.
2. Kiểu literal trong đó key là tên sự kiện, còn value là kiểu mảng / tuple biểu diễn các tham số bổ sung được chấp nhận cho sự kiện đó. Ví dụ trên dùng named tuple để mỗi đối số có thể có tên rõ ràng.

Như bạn thấy, khai báo kiểu cho ta khả năng kiểm soát chi tiết hơn nhiều đối với ràng buộc kiểu của các sự kiện được emit.

Khi không dùng `<script setup>`, `defineComponent()` có thể suy luận các sự kiện hợp lệ cho hàm `emit` được lộ ra trên setup context:

```ts
import { defineComponent } from 'vue'

export default defineComponent({
  emits: ['change'],
  setup(props, { emit }) {
    emit('change') // <-- kiểm tra kiểu / tự động hoàn thành
  }
})
```

## Khai Báo Kiểu Cho `ref()` {#typing-ref}

Ref sẽ suy luận kiểu từ giá trị khởi tạo:

```ts
import { ref } from 'vue'

// kiểu được suy luận: Ref<number>
const year = ref(2020)

// => Lỗi TS: Type 'string' is not assignable to type 'number'.
year.value = '2020'
```

Đôi khi ta cần chỉ định kiểu phức tạp cho giá trị bên trong ref. Ta có thể làm điều đó bằng kiểu `Ref`:

```ts
import { ref } from 'vue'
import type { Ref } from 'vue'

const year: Ref<string | number> = ref('2020')

year.value = 2020 // ok!
```

Hoặc truyền đối số generic khi gọi `ref()` để ghi đè suy luận mặc định:

```ts
// kiểu kết quả: Ref<string | number>
const year = ref<string | number>('2020')

year.value = 2020 // ok!
```

Nếu bạn chỉ định đối số kiểu generic nhưng bỏ qua giá trị khởi tạo, kiểu kết quả sẽ là union có bao gồm `undefined`:

```ts
// kiểu được suy luận: Ref<number | undefined>
const n = ref<number>()
```

## Khai Báo Kiểu Cho `reactive()` {#typing-reactive}

`reactive()` cũng sẽ ngầm suy luận kiểu từ đối số của nó:

```ts
import { reactive } from 'vue'

// kiểu được suy luận: { title: string }
const book = reactive({ title: 'Vue 3 Guide' })
```

Để khai báo kiểu tường minh cho thuộc tính `reactive`, ta có thể dùng interface:

```ts
import { reactive } from 'vue'

interface Book {
  title: string
  year?: number
}

const book: Book = reactive({ title: 'Vue 3 Guide' })
```

:::tip
Không nên dùng đối số generic của `reactive()` vì kiểu trả về, vốn xử lý việc mở bọc ref lồng nhau, khác với kiểu của đối số generic.
:::

## Khai Báo Kiểu Cho `computed()` {#typing-computed}

`computed()` suy luận kiểu dựa trên giá trị trả về của getter:

```ts
import { ref, computed } from 'vue'

const count = ref(0)

// kiểu được suy luận: ComputedRef<number>
const double = computed(() => count.value * 2)

// => Lỗi TS: Property 'split' does not exist on type 'number'
const result = double.value.split('')
```

Bạn cũng có thể chỉ định kiểu tường minh thông qua đối số generic:

```ts
const double = computed<number>(() => {
  // lỗi kiểu nếu hàm này không trả về number
})
```

## Khai Báo Kiểu Cho Event Handler {#typing-event-handlers}

Khi xử lý sự kiện DOM gốc, việc khai báo đúng kiểu cho đối số truyền vào handler có thể rất hữu ích. Hãy xem ví dụ sau:

```vue
<script setup lang="ts">
function handleChange(event) {
  // `event` ngầm có kiểu `any`
  console.log(event.target.value)
}
</script>

<template>
  <input type="text" @change="handleChange" />
</template>
```

Nếu không có chú thích kiểu, đối số `event` sẽ ngầm có kiểu `any`. Điều này cũng gây ra lỗi TS nếu `tsconfig.json` dùng `"strict": true` hoặc `"noImplicitAny": true`. Vì vậy, nên chú thích kiểu tường minh cho đối số của event handler. Ngoài ra, bạn có thể cần dùng ép kiểu khi truy cập thuộc tính của `event`:

```ts
function handleChange(event: Event) {
  console.log((event.target as HTMLInputElement).value)
}
```

## Khai Báo Kiểu Cho Provide / Inject {#typing-provide-inject}

Provide và inject thường được thực hiện ở các component tách biệt nhau. Để khai báo đúng kiểu cho giá trị được inject, Vue cung cấp interface `InjectionKey`, đây là một kiểu generic mở rộng từ `Symbol`. Nó có thể được dùng để đồng bộ kiểu của giá trị được inject giữa bên cung cấp và bên tiêu thụ:

```ts
import { provide, inject } from 'vue'
import type { InjectionKey } from 'vue'

const key = Symbol() as InjectionKey<string>

provide(key, 'foo') // cung cấp giá trị không phải string sẽ gây lỗi

const foo = inject(key) // kiểu của foo: string | undefined
```

Bạn nên đặt injection key trong một file riêng để có thể import vào nhiều component.

Khi dùng injection key dạng chuỗi, kiểu của giá trị được inject sẽ là `unknown`, và cần được khai báo tường minh qua đối số generic:

```ts
const foo = inject<string>('foo') // kiểu: string | undefined
```

Lưu ý rằng giá trị được inject vẫn có thể là `undefined`, vì không có gì bảo đảm sẽ luôn có provider cung cấp giá trị này ở runtime.

Kiểu `undefined` có thể được loại bỏ bằng cách cung cấp giá trị mặc định:

```ts
const foo = inject<string>('foo', 'bar') // kiểu: string
```

Nếu bạn chắc chắn giá trị đó luôn được cung cấp, bạn cũng có thể ép kiểu cưỡng bức:

```ts
const foo = inject('foo') as string
```

## Khai Báo Kiểu Cho Template Ref {#typing-template-refs}

Với Vue 3.5 và `@vue/language-tools` 2.1 (được dùng cho cả language service trong IDE lẫn `vue-tsc`), kiểu của ref được tạo bởi `useTemplateRef()` trong SFC có thể được **tự động suy luận** đối với ref tĩnh dựa trên phần tử mà thuộc tính `ref` tương ứng được dùng lên.

Trong những trường hợp không thể tự động suy luận, bạn vẫn có thể ép template ref sang kiểu tường minh thông qua đối số generic:

```ts
const el = useTemplateRef<HTMLInputElement>('el')
```

<details>
<summary>Cách dùng trước phiên bản 3.5</summary>

Template ref nên được tạo với đối số kiểu generic tường minh và giá trị khởi tạo là `null`:

```vue
<script setup lang="ts">
import { ref, onMounted } from 'vue'

const el = ref<HTMLInputElement | null>(null)

onMounted(() => {
  el.value?.focus()
})
</script>

<template>
  <input ref="el" />
</template>
```

</details>

Để lấy đúng interface DOM, bạn có thể xem các trang như [MDN](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#technical_summary).

Lưu ý rằng để bảo đảm an toàn kiểu chặt chẽ, bạn cần dùng optional chaining hoặc type guard khi truy cập `el.value`. Lý do là giá trị ban đầu của ref là `null` cho đến khi component được mount, và nó cũng có thể bị đặt lại thành `null` nếu phần tử được tham chiếu bị unmount bởi `v-if`.

## Khai Báo Kiểu Cho Template Ref Của Component {#typing-component-template-refs}

Với Vue 3.5 và `@vue/language-tools` 2.1 (được dùng cho cả language service trong IDE lẫn `vue-tsc`), kiểu của ref được tạo bởi `useTemplateRef()` trong SFC có thể được **tự động suy luận** đối với ref tĩnh dựa trên phần tử hoặc component mà thuộc tính `ref` tương ứng được dùng lên.

Trong những trường hợp không thể tự động suy luận (ví dụ: dùng ngoài SFC hoặc component động), bạn vẫn có thể ép template ref sang kiểu tường minh thông qua đối số generic.

Để lấy kiểu instance của một component đã import, trước tiên ta cần lấy kiểu của nó qua `typeof`, sau đó dùng kiểu tiện ích dựng sẵn `InstanceType` của TypeScript để trích xuất kiểu instance:

```vue{6,7} [App.vue]
<script setup lang="ts">
import { useTemplateRef } from 'vue'
import Foo from './Foo.vue'
import Bar from './Bar.vue'

type FooType = InstanceType<typeof Foo>
type BarType = InstanceType<typeof Bar>

const compRef = useTemplateRef<FooType | BarType>('comp')
</script>

<template>
  <component :is="Math.random() > 0.5 ? Foo : Bar" ref="comp" />
</template>
```

Trong trường hợp kiểu chính xác của component không sẵn có hoặc không quan trọng, bạn có thể dùng `ComponentPublicInstance` thay thế. Kiểu này chỉ bao gồm các thuộc tính chung cho mọi component, chẳng hạn `$el`:

```ts
import { useTemplateRef } from 'vue'
import type { ComponentPublicInstance } from 'vue'

const child = useTemplateRef<ComponentPublicInstance>('child')
```

Trong trường hợp component được tham chiếu là [generic component](/guide/typescript/overview.html#generic-components), ví dụ `MyGenericModal`:

```vue [MyGenericModal.vue]
<script setup lang="ts" generic="ContentType extends string | number">
import { ref } from 'vue'

const content = ref<ContentType | null>(null)

const open = (newContent: ContentType) => (content.value = newContent)

defineExpose({
  open
})
</script>
```

Nó cần được tham chiếu bằng `ComponentExposed` từ thư viện [`vue-component-type-helpers`](https://www.npmjs.com/package/vue-component-type-helpers) vì `InstanceType` sẽ không hoạt động.

```vue [App.vue]
<script setup lang="ts">
import { useTemplateRef } from 'vue'
import MyGenericModal from './MyGenericModal.vue'
import type { ComponentExposed } from 'vue-component-type-helpers'

const modal =
  useTemplateRef<ComponentExposed<typeof MyGenericModal>>('modal')

const openModal = () => {
  modal.value?.open('newValue')
}
</script>
```

Lưu ý rằng với `@vue/language-tools` 2.1+, kiểu của template ref tĩnh có thể được tự động suy luận và phần trên chỉ cần thiết trong các trường hợp biên.

## Khai Báo Kiểu Cho Custom Directive Toàn Cục {#typing-global-custom-directives}

Để nhận gợi ý kiểu và kiểm tra kiểu cho các custom directive toàn cục được khai báo bằng `app.directive()`, bạn có thể mở rộng `GlobalDirectives`:

```ts [src/directives/highlight.ts]
import type { Directive } from 'vue'

export type HighlightDirective = Directive<HTMLElement, string>

declare module 'vue' {
  export interface GlobalDirectives {
    // thêm tiền tố v (v-highlight)
    vHighlight: HighlightDirective
  }
}

export default {
  mounted: (el, binding) => {
    el.style.backgroundColor = binding.value
  }
} satisfies HighlightDirective
```

```ts [main.ts]
import highlight from './directives/highlight'
// ...mã khác
const app = createApp(App)
app.directive('highlight', highlight)
```

Cách dùng trong component:

```vue [App.vue]
<template>
  <p v-highlight="'blue'">Câu này rất quan trọng!</p>
</template>
```
