# Vue Và Web Components {#vue-and-web-components}

[Web Components](https://developer.mozilla.org/en-US/docs/Web/Web_Components) là tên gọi chung cho một tập API gốc của nền tảng web, cho phép developer tạo ra các custom element có thể tái sử dụng.

Chúng tôi xem Vue và Web Components chủ yếu là hai công nghệ bổ trợ cho nhau. Vue hỗ trợ rất tốt cả việc dùng custom element lẫn tạo custom element. Dù bạn đang tích hợp custom element vào một ứng dụng Vue sẵn có, hay đang dùng Vue để xây và phát hành custom element, đây đều là những trường hợp sử dụng rất phù hợp.

## Dùng Custom Elements Trong Vue {#using-custom-elements-in-vue}

Vue [đạt điểm tuyệt đối 100% trong bộ kiểm thử Custom Elements Everywhere](https://custom-elements-everywhere.com/libraries/vue/results/results.html). Việc dùng custom element bên trong một ứng dụng Vue nhìn chung hoạt động tương tự như khi dùng phần tử HTML gốc, chỉ có một vài điểm cần lưu ý:

### Bỏ Qua Bước Resolve Component {#skipping-component-resolution}

Mặc định, Vue sẽ cố resolve một thẻ HTML không phải native thành component Vue đã đăng ký trước, rồi mới fallback sang render nó như custom element. Điều này sẽ khiến Vue phát cảnh báo "failed to resolve component" trong lúc phát triển. Để báo cho Vue biết rằng một số phần tử nhất định cần được xem là custom element và bỏ qua bước resolve component, ta có thể cấu hình [`compilerOptions.isCustomElement`](/api/application#app-config-compileroptions).

Nếu bạn đang dùng Vue với một thiết lập build, option này nên được truyền qua cấu hình build vì đây là option ở thời điểm biên dịch.

#### Ví Dụ Cấu Hình Trực Tiếp Trong Trình Duyệt {#example-in-browser-config}

```js
// Chỉ hoạt động khi dùng in-browser compilation.
// Nếu dùng build tool, xem các ví dụ cấu hình bên dưới.
app.config.compilerOptions.isCustomElement = (tag) => tag.includes('-')
```

#### Ví Dụ Cấu Hình Vite {#example-vite-config}

```js [vite.config.js]
import vue from '@vitejs/plugin-vue'

export default {
  plugins: [
    vue({
      template: {
        compilerOptions: {
          // xem mọi thẻ có dấu gạch ngang là custom element
          isCustomElement: (tag) => tag.includes('-')
        }
      }
    })
  ]
}
```

#### Ví Dụ Cấu Hình Vue CLI {#example-vue-cli-config}

```js [vue.config.js]
module.exports = {
  chainWebpack: (config) => {
    config.module
      .rule('vue')
      .use('vue-loader')
      .tap((options) => ({
        ...options,
        compilerOptions: {
          // xem mọi thẻ bắt đầu bằng ion- là custom element
          isCustomElement: (tag) => tag.startsWith('ion-')
        }
      }))
  }
}
```

### Truyền DOM Property {#passing-dom-properties}

Vì attribute của DOM chỉ có thể là chuỗi, ta cần truyền dữ liệu phức tạp vào custom element dưới dạng DOM property. Khi gán prop cho custom element, Vue 3 sẽ tự động kiểm tra sự hiện diện của DOM property bằng toán tử `in`, và nếu key tồn tại, Vue sẽ ưu tiên gán giá trị như một DOM property. Điều này có nghĩa là trong phần lớn trường hợp, bạn không cần bận tâm đến chuyện này nếu custom element tuân theo [best practice được khuyến nghị](https://web.dev/custom-elements-best-practices/).

Tuy nhiên, vẫn có những trường hợp hiếm gặp khi dữ liệu bắt buộc phải được truyền dưới dạng DOM property, nhưng custom element lại không định nghĩa / phản chiếu property đó đúng cách, khiến phép kiểm tra `in` thất bại. Khi đó, bạn có thể ép `v-bind` gán theo dạng DOM property bằng modifier `.prop`:

```vue-html
<my-element :user.prop="{ name: 'jack' }"></my-element>

<!-- cú pháp rút gọn tương đương -->
<my-element .user="{ name: 'jack' }"></my-element>
```

## Xây Custom Elements Bằng Vue {#building-custom-elements-with-vue}

Lợi ích lớn nhất của custom element là nó có thể được dùng với bất kỳ framework nào, thậm chí không cần framework. Điều này khiến custom element rất phù hợp để phát hành component khi phía sử dụng cuối có thể không cùng frontend stack với bạn, hoặc khi bạn muốn tách ứng dụng tiêu thụ khỏi chi tiết cài đặt của component.

### defineCustomElement {#definecustomelement}

Vue hỗ trợ tạo custom element bằng chính cùng bộ API component Vue quen thuộc thông qua phương thức [`defineCustomElement`](/api/custom-elements#definecustomelement). Phương thức này nhận đối số giống [`defineComponent`](/api/general#definecomponent), nhưng thay vì trả về định nghĩa component, nó trả về constructor của custom element kế thừa từ `HTMLElement`:

```vue-html
<my-vue-element></my-vue-element>
```

```js
import { defineCustomElement } from 'vue'

const MyVueElement = defineCustomElement({
  // option component Vue thông thường
  props: {},
  emits: {},
  template: `...`,

  // chỉ dành cho defineCustomElement: CSS được chèn vào shadow root
  styles: [`/* inlined css */`]
})

// Đăng ký custom element.
// Sau khi đăng ký, mọi thẻ <my-vue-element>
// trên trang sẽ được nâng cấp.
customElements.define('my-vue-element', MyVueElement)

// Bạn cũng có thể khởi tạo phần tử bằng mã:
// (chỉ làm được sau khi đã đăng ký)
document.body.appendChild(
  new MyVueElement({
    // prop ban đầu (tùy chọn)
  })
)
```

#### Vòng Đời {#lifecycle}

- Một custom element của Vue sẽ mount một instance component Vue nội bộ bên trong shadow root của nó khi [`connectedCallback`](https://developer.mozilla.org/en-US/docs/Web/Web_Components/Using_custom_elements#using_the_lifecycle_callbacks) của phần tử được gọi lần đầu.

- Khi `disconnectedCallback` của phần tử được gọi, Vue sẽ kiểm tra sau một microtask xem phần tử có còn nằm trong document hay không.

  - Nếu phần tử vẫn còn trong document thì đây là thao tác di chuyển, và instance component sẽ được giữ nguyên.

  - Nếu phần tử đã tách khỏi document thì đây là thao tác xóa, và instance component sẽ bị unmount.

#### Props {#props}

- Mọi prop được khai báo bằng option `props` sẽ được định nghĩa trên custom element như property. Vue cũng sẽ tự xử lý việc phản chiếu giữa attribute / property ở những nơi phù hợp.

  - Attribute luôn được phản chiếu sang property tương ứng.

  - Property có giá trị nguyên thủy (`string`, `boolean` hoặc `number`) sẽ được phản chiếu thành attribute.

- Vue cũng tự động ép kiểu cho prop khai báo với kiểu `Boolean` hoặc `Number` khi chúng được gán dưới dạng attribute, vốn luôn là chuỗi. Ví dụ với khai báo prop sau:

  ```js
  props: {
    selected: Boolean,
    index: Number
  }
  ```

  Và cách dùng custom element như sau:

  ```vue-html
  <my-element selected index="1"></my-element>
  ```

  Bên trong component, `selected` sẽ được ép thành `true` (boolean) và `index` sẽ được ép thành `1` (number).

#### Sự Kiện {#events}

Sự kiện được emit qua `this.$emit` hoặc `emit` trong `setup` sẽ được dispatch như [CustomEvent](https://developer.mozilla.org/en-US/docs/Web/Events/Creating_and_triggering_events#adding_custom_data_%E2%80%93_customevent) gốc trên custom element. Các đối số sự kiện bổ sung (payload) sẽ được lộ ra dưới dạng một mảng trong property `detail` của object CustomEvent.

#### Slots {#slots}

Bên trong component, slot vẫn có thể được render bằng phần tử `<slot/>` như bình thường. Tuy nhiên, khi dùng phần tử kết quả ở phía tiêu thụ, nó chỉ chấp nhận [cú pháp slot gốc](https://developer.mozilla.org/en-US/docs/Web/Web_Components/Using_templates_and_slots):

- [Scoped slot](/guide/components/slots#scoped-slots) không được hỗ trợ.

- Khi truyền named slot, hãy dùng attribute `slot` thay vì directive `v-slot`:

  ```vue-html
  <my-element>
    <div slot="named">hello</div>
  </my-element>
  ```

#### Provide / Inject {#provide-inject}

[Provide / Inject API](/guide/components/provide-inject#provide-inject) và [phiên bản tương đương trong Composition API](/api/composition-api-dependency-injection#provide) cũng hoạt động giữa các custom element được định nghĩa bằng Vue. Tuy nhiên, cần lưu ý rằng điều này **chỉ hoạt động giữa các custom element với nhau**. Nói cách khác, một custom element định nghĩa bằng Vue sẽ không thể inject property do một component Vue không phải custom element cung cấp.

#### Cấu Hình Ở Cấp Ứng Dụng <sup class="vt-badge" data-text="3.5+" /> {#app-level-config}

Bạn có thể cấu hình app instance của custom element Vue bằng option `configureApp`:

```js
defineCustomElement(MyComponent, {
  configureApp(app) {
    app.config.errorHandler = (err) => {
      /* ... */
    }
  }
})
```

### Dùng SFC Làm Custom Element {#sfc-as-custom-element}

`defineCustomElement` cũng hoạt động với Vue Single-File Component (SFC). Tuy nhiên, với thiết lập công cụ mặc định, phần `<style>` trong SFC vẫn sẽ bị tách ra và gộp vào một file CSS duy nhất trong build production. Khi dùng SFC như một custom element, ta thường muốn chèn các thẻ `<style>` vào shadow root của custom element thay vào đó.

Bộ công cụ SFC chính thức hỗ trợ import SFC ở "custom element mode" (yêu cầu `@vitejs/plugin-vue@^1.4.0` hoặc `vue-loader@^16.5.0`). Một SFC được nạp ở chế độ custom element sẽ inline các thẻ `<style>` thành chuỗi CSS và lộ chúng qua option `styles` của component. `defineCustomElement` sẽ nhận được các chuỗi này và chèn chúng vào shadow root của phần tử khi phần tử được khởi tạo.

Để bật chế độ này, chỉ cần kết thúc tên file component bằng `.ce.vue`:

```js
import { defineCustomElement } from 'vue'
import Example from './Example.ce.vue'

console.log(Example.styles) // ["/* inlined css */"]

// chuyển thành constructor custom element
const ExampleElement = defineCustomElement(Example)

// đăng ký
customElements.define('my-example', ExampleElement)
```

Nếu bạn muốn tùy biến file nào sẽ được import ở custom element mode, chẳng hạn xem _mọi_ SFC đều là custom element, bạn có thể truyền option `customElement` cho plugin build tương ứng:

- [@vitejs/plugin-vue](https://github.com/vitejs/vite-plugin-vue/tree/main/packages/plugin-vue#using-vue-sfcs-as-custom-elements)
- [vue-loader](https://github.com/vuejs/vue-loader/tree/next#v16-only-options)

### Mẹo Khi Làm Thư Viện Vue Custom Elements {#tips-for-a-vue-custom-elements-library}

Khi xây custom element bằng Vue, các phần tử đó sẽ phụ thuộc vào runtime của Vue. Chi phí kích thước nền tảng vào khoảng ~16kb, tùy vào số lượng tính năng bạn dùng. Điều này có nghĩa là nếu bạn chỉ phát hành một custom element đơn lẻ thì Vue có thể không phải lựa chọn lý tưởng. Khi đó bạn có thể cân nhắc JavaScript thuần, [petite-vue](https://github.com/vuejs/petite-vue), hoặc những framework tối ưu cho runtime nhỏ. Tuy nhiên, nếu bạn phát hành cả một bộ custom element với logic phức tạp, kích thước nền đó hoàn toàn xứng đáng vì Vue giúp mỗi component được viết với ít mã hơn nhiều. Bạn phát hành càng nhiều phần tử cùng nhau thì sự đánh đổi này càng có lợi.

Nếu các custom element sẽ được dùng trong một ứng dụng cũng đang dùng Vue, bạn có thể externalize Vue khỏi bundle build để các phần tử dùng chung cùng một bản Vue của ứng dụng host.

Bạn nên export riêng từng constructor của element để người dùng có thể import theo nhu cầu và đăng ký chúng với tên thẻ mong muốn. Bạn cũng có thể export một hàm tiện ích để tự động đăng ký toàn bộ element. Đây là ví dụ về entry point của một thư viện Vue custom elements:

```js [elements.js]
import { defineCustomElement } from 'vue'
import Foo from './MyFoo.ce.vue'
import Bar from './MyBar.ce.vue'

const MyFoo = defineCustomElement(Foo)
const MyBar = defineCustomElement(Bar)

// export từng element riêng lẻ
export { MyFoo, MyBar }

export function register() {
  customElements.define('my-foo', MyFoo)
  customElements.define('my-bar', MyBar)
}
```

Phía sử dụng có thể dùng các element trong một file Vue:

```vue
<script setup>
import { register } from 'path/to/elements.js'
register()
</script>

<template>
  <my-foo ...>
    <my-bar ...></my-bar>
  </my-foo>
</template>
```

Hoặc trong framework khác, ví dụ framework dùng JSX, với tên tùy ý:

```jsx
import { MyFoo, MyBar } from 'path/to/elements.js'

customElements.define('some-foo', MyFoo)
customElements.define('some-bar', MyBar)

export function MyComponent() {
  return <>
    <some-foo ... >
      <some-bar ... ></some-bar>
    </some-foo>
  </>
}
```

### Web Components Dùng Vue Và TypeScript {#web-components-and-typescript}

Khi viết template cho Vue SFC, bạn có thể muốn [kiểm tra kiểu](/guide/scaling-up/tooling.html#typescript) cho component Vue của mình, bao gồm cả những component được định nghĩa như custom element.

Custom element được đăng ký toàn cục trong trình duyệt thông qua API tích hợp sẵn, và mặc định khi dùng trong template Vue chúng sẽ không có type inference. Để hỗ trợ kiểu cho component Vue đã đăng ký dưới dạng custom element, ta có thể đăng ký kiểu component toàn cục bằng cách mở rộng [`GlobalComponents` interface](https://github.com/vuejs/language-tools/wiki/Global-Component-Types) để kiểm tra kiểu trong template Vue (người dùng JSX có thể mở rộng kiểu [JSX.IntrinsicElements](https://www.typescriptlang.org/docs/handbook/jsx.html#intrinsic-elements), nhưng phần đó không trình bày ở đây).

Đây là cách định nghĩa kiểu cho một custom element tạo bằng Vue:

```typescript
import { defineCustomElement } from 'vue'

// Import component Vue.
import SomeComponent from './src/components/SomeComponent.ce.vue'

// Chuyển component Vue thành class Custom Element.
export const SomeElement = defineCustomElement(SomeComponent)

// Đừng quên đăng ký class element với trình duyệt.
customElements.define('some-element', SomeElement)

// Thêm kiểu element mới vào GlobalComponents của Vue.
declare module 'vue' {
  interface GlobalComponents {
    // Hãy chắc chắn truyền kiểu component Vue ở đây
    // (SomeComponent, *không phải* SomeElement).
    // Custom Element bắt buộc phải có dấu gạch ngang trong tên,
    // nên hãy dùng tên phần tử dạng gạch ngang ở đây.
    'some-element': typeof SomeComponent
  }
}
```

## Web Components Không Dùng Vue Và TypeScript {#non-vue-web-components-and-typescript}

Đây là cách được khuyến nghị để bật kiểm tra kiểu trong template SFC cho Custom Element không được xây bằng Vue.

:::tip Lưu ý
Cách này là một trong những hướng làm khả thi, nhưng có thể khác đi tùy framework được dùng để tạo custom element.
:::

Giả sử ta có một custom element với một số property và event JavaScript được định nghĩa sẵn, và nó được phát hành trong thư viện tên `some-lib`:

```ts [some-lib/src/SomeElement.ts]
// Định nghĩa một class với JS property có kiểu.
export class SomeElement extends HTMLElement {
  foo: number = 123
  bar: string = 'blah'

  lorem: boolean = false

  // Phương thức này không nên được lộ ra cho kiểu trong template.
  someMethod() {
    /* ... */
  }

  // ... lược bỏ chi tiết cài đặt ...
  // ... giả sử phần tử này phát sự kiện tên "apple-fell" ...
}

customElements.define('some-element', SomeElement)

// Đây là danh sách các property của SomeElement sẽ được chọn để kiểm tra kiểu
// trong template của framework (ví dụ template Vue SFC). Mọi property khác
// sẽ không được lộ ra.
export type SomeElementAttributes = 'foo' | 'bar'

// Định nghĩa kiểu của các sự kiện mà SomeElement phát ra.
export type SomeElementEvents = {
  'apple-fell': AppleFellEvent
}

export class AppleFellEvent extends Event {
  /* ... lược bỏ chi tiết ... */
}
```

Chi tiết cài đặt đã được lược bỏ, nhưng điểm quan trọng là ta có định nghĩa kiểu cho hai thứ: kiểu prop và kiểu event.

Hãy tạo một helper type để dễ đăng ký định nghĩa kiểu custom element trong Vue:

```ts [some-lib/src/DefineCustomElement.ts]
// Ta có thể dùng lại helper type này cho từng element cần định nghĩa.
type DefineCustomElement<
  ElementType extends HTMLElement,
  Events extends EventMap = {},
  SelectedAttributes extends keyof ElementType = keyof ElementType
> = new () => ElementType & {
  // Dùng $props để định nghĩa những property được lộ ra cho kiểm tra kiểu trong
  // template. Vue đọc định nghĩa prop từ kiểu `$props`. Lưu ý rằng ta kết hợp
  // prop của element với prop HTML toàn cục và các prop đặc biệt của Vue.
  /** @deprecated Đừng dùng property $props trên ref của Custom Element,
    đây chỉ dùng cho kiểu prop trong template. */
  $props: HTMLAttributes &
    Partial<Pick<ElementType, SelectedAttributes>> &
    PublicProps

  // Dùng $emit để định nghĩa rõ kiểu event. Vue đọc kiểu event từ `$emit`.
  // Lưu ý `$emit` cần một định dạng nhất định, nên ta ánh xạ `Events` về dạng đó.
  /** @deprecated Đừng dùng property $emit trên ref của Custom Element,
    đây chỉ dùng cho kiểu prop trong template. */
  $emit: VueEmit<Events>
}

type EventMap = {
  [event: string]: Event
}

// Ánh xạ EventMap sang định dạng mà kiểu $emit của Vue mong đợi.
type VueEmit<T extends EventMap> = EmitFn<{
  [K in keyof T]: (event: T[K]) => void
}>
```

:::tip Lưu ý
Ta đánh dấu `$props` và `$emit` là deprecated để khi lấy `ref` tới custom element, ta sẽ không bị cám dỗ dùng các property này. Chúng chỉ phục vụ cho mục đích kiểm tra kiểu với custom element trong template, và không thực sự tồn tại trên instance của custom element.
:::

Dùng helper type trên, giờ ta có thể chọn các JS property cần lộ ra cho việc kiểm tra kiểu trong template Vue:

```ts [some-lib/src/SomeElement.vue.ts]
import {
  SomeElement,
  SomeElementAttributes,
  SomeElementEvents
} from './SomeElement.js'
import type { Component } from 'vue'
import type { DefineCustomElement } from './DefineCustomElement'

// Thêm kiểu element mới vào GlobalComponents của Vue.
declare module 'vue' {
  interface GlobalComponents {
    'some-element': DefineCustomElement<
      SomeElement,
      SomeElementAttributes,
      SomeElementEvents
    >
  }
}
```

Giả sử `some-lib` build mã nguồn TypeScript của nó vào thư mục `dist/`. Người dùng của `some-lib` khi đó có thể import `SomeElement` và dùng nó trong Vue SFC như sau:

```vue [SomeElementImpl.vue]
<script setup lang="ts">
// Thao tác này sẽ tạo và đăng ký element với trình duyệt.
import 'some-lib/dist/SomeElement.js'

// Người dùng dùng TypeScript và Vue nên import thêm file định nghĩa kiểu
// dành riêng cho Vue (người dùng framework khác có thể import file định nghĩa
// kiểu dành riêng cho framework của họ).
import type {} from 'some-lib/dist/SomeElement.vue.js'

import { useTemplateRef, onMounted } from 'vue'

const el = useTemplateRef('el')

onMounted(() => {
  console.log(
    el.value!.foo,
    el.value!.bar,
    el.value!.lorem,
    el.value!.someMethod()
  )

  // Đừng dùng các property này, chúng là `undefined`
  // IDE sẽ hiển thị chúng bằng kiểu gạch ngang
  el.$props
  el.$emit
})
</script>

<template>
  <!-- Giờ ta có thể dùng element với kiểm tra kiểu: -->
  <some-element
    ref="el"
    :foo="456"
    :blah="'hello'"
    @apple-fell="
      (event) => {
        // Kiểu của `event` ở đây sẽ được suy ra là `AppleFellEvent`
      }
    "
  ></some-element>
</template>
```

Nếu một element không có định nghĩa kiểu sẵn, kiểu của property và event có thể được định nghĩa thủ công hơn:

```vue [SomeElementImpl.vue]
<script setup lang="ts">
// Giả sử `some-lib` là JS thuần không có định nghĩa kiểu, và TypeScript
// không thể suy ra kiểu:
import { SomeElement } from 'some-lib'

// Ta sẽ dùng lại helper type như ở trên.
import { DefineCustomElement } from './DefineCustomElement'

type SomeElementProps = { foo?: number; bar?: string }
type SomeElementEvents = { 'apple-fell': AppleFellEvent }
interface AppleFellEvent extends Event {
  /* ... */
}

// Thêm kiểu element mới vào GlobalComponents của Vue.
declare module 'vue' {
  interface GlobalComponents {
    'some-element': DefineCustomElement<
      SomeElementProps,
      SomeElementEvents
    >
  }
}

// ... giống như trên, dùng ref tới element ...
</script>

<template>
  <!-- ... giống như trên, dùng element trong template ... -->
</template>
```

Tác giả custom element không nên tự động export các định nghĩa kiểu custom element dành riêng cho framework từ thư viện của họ, ví dụ không nên export chúng từ `index.ts` cùng với phần còn lại của thư viện, vì người dùng sẽ gặp lỗi module augmentation ngoài ý muốn. Thay vào đó, người dùng nên tự import file định nghĩa kiểu dành cho framework mà họ cần.

## Web Components So Với Vue Components {#web-components-vs-vue-components}

Một số developer cho rằng nên tránh các mô hình component mang tính riêng của framework, và rằng chỉ dùng Custom Elements mới làm ứng dụng "future-proof". Ở đây, chúng tôi muốn giải thích vì sao chúng tôi cho rằng đó là một cách nhìn quá đơn giản về vấn đề.

Đúng là giữa Custom Elements và Vue Components có một mức độ chồng lặp tính năng nhất định: cả hai đều cho phép định nghĩa component tái sử dụng với khả năng truyền dữ liệu, emit sự kiện và quản lý vòng đời. Tuy nhiên, API Web Components khá thấp tầng và tối giản. Để xây được một ứng dụng thực tế, ta còn cần thêm khá nhiều khả năng mà bản thân nền tảng không cung cấp:

- Một hệ thống template khai báo và hiệu quả.

- Một hệ thống quản lý state phản ứng giúp tách và tái sử dụng logic xuyên component.

- Một cách hiệu quả để render component ở phía server và hydrate ở phía client (SSR), điều rất quan trọng cho SEO và [các chỉ số Web Vitals như LCP](https://web.dev/vitals/). SSR với custom element gốc thường phải mô phỏng DOM trong Node.js rồi serialize DOM đã thay đổi, trong khi Vue SSR có thể biên dịch thành nối chuỗi ở nơi phù hợp, nên hiệu quả hơn nhiều.

Mô hình component của Vue được thiết kế như một hệ thống nhất quán để đáp ứng chính những nhu cầu đó.

Với một đội ngũ kỹ thuật đủ mạnh, bạn có lẽ vẫn có thể tự xây ra thứ tương đương trên nền Custom Elements gốc. Nhưng điều đó cũng đồng nghĩa với việc bạn đang tự gánh trách nhiệm bảo trì dài hạn cho một framework nội bộ, đồng thời bỏ lỡ lợi ích của hệ sinh thái và cộng đồng mà một framework trưởng thành như Vue mang lại.

Cũng có những framework xây trên nền Custom Elements làm mô hình component cốt lõi, nhưng rốt cuộc chúng vẫn phải đưa vào các giải pháp riêng cho những vấn đề vừa nêu. Dùng các framework đó nghĩa là bạn vẫn phải chấp nhận các quyết định kỹ thuật của chúng về cách giải các bài toán này. Nói cách khác, bất chấp quảng bá, việc đó không tự động giúp bạn tránh khỏi biến động trong tương lai.

Ngoài ra, cũng có vài khía cạnh mà theo chúng tôi custom element còn khá hạn chế:

- Việc đánh giá slot một cách eager cản trở khả năng kết hợp component. [Scoped slot](/guide/components/slots#scoped-slots) của Vue là cơ chế rất mạnh để phối hợp component, nhưng custom element không hỗ trợ được do slot gốc vốn eager. Slot eager cũng có nghĩa là component nhận không thể kiểm soát khi nào hoặc liệu một mẩu nội dung slot có được render hay không.

- Việc phát hành custom element với CSS phạm vi theo shadow DOM hiện nay đòi hỏi phải nhúng CSS vào JavaScript để chèn vào shadow root lúc chạy. Trong kịch bản SSR, nó cũng làm lặp style trong markup. Nền tảng đang phát triển [một số tính năng](https://github.com/whatwg/html/pull/4898/) cho lĩnh vực này, nhưng hiện tại chúng vẫn chưa được hỗ trợ rộng rãi, và vẫn còn những vấn đề về hiệu năng production / SSR cần xử lý. Trong lúc chờ đợi, Vue SFC đã có [cơ chế phạm vi CSS](/api/sfc-css-features) hỗ trợ tách style thành file CSS thuần.

Vue sẽ luôn theo sát các tiêu chuẩn mới nhất của nền tảng web, và chúng tôi sẵn sàng tận dụng bất cứ thứ gì nền tảng cung cấp nếu nó giúp công việc trở nên dễ hơn. Tuy nhiên, mục tiêu của chúng tôi là cung cấp giải pháp hoạt động tốt ngay bây giờ. Điều đó có nghĩa là ta phải tiếp cận các tính năng nền tảng mới với tư duy phản biện, đồng thời lấp các khoảng trống khi tiêu chuẩn vẫn chưa đủ.
