---
outline: deep
---

# Render Functions Và JSX {#render-functions-jsx}

Vue khuyến nghị dùng template để xây ứng dụng trong phần lớn trường hợp. Tuy nhiên, vẫn có những tình huống mà ta cần toàn bộ sức mạnh lập trình của JavaScript. Khi đó, ta có thể dùng **render function**.

> Nếu bạn còn mới với virtual DOM và render function, hãy đọc trước chương [Cơ Chế Render](/guide/extras/rendering-mechanism).

## Cách Dùng Cơ Bản {#basic-usage}

### Tạo VNode {#creating-vnodes}

Vue cung cấp hàm `h()` để tạo vnode:

```js
import { h } from 'vue'

const vnode = h(
  'div', // type
  { id: 'foo', class: 'bar' }, // props
  [
    /* children */
  ]
)
```

`h()` là viết tắt của **hyperscript**, tức "JavaScript tạo ra HTML". Tên gọi này đến từ quy ước dùng chung của nhiều hệ virtual DOM. Một cái tên mô tả hơn có thể là `createVNode()`, nhưng tên ngắn sẽ tiện hơn khi bạn phải gọi hàm này rất nhiều lần trong render function.

Hàm `h()` được thiết kế khá linh hoạt:

```js
// mọi đối số ngoại trừ type đều là tùy chọn
h('div')
h('div', { id: 'foo' })

// cả attribute lẫn property đều có thể dùng trong props
// Vue sẽ tự chọn cách gán phù hợp
h('div', { class: 'bar', innerHTML: 'hello' })

// modifier props như `.prop` và `.attr` có thể thêm
// bằng tiền tố `.` và `^`
h('div', { '.name': 'some-name', '^width': '100' })

// class và style hỗ trợ object / array
// giống như trong template
h('div', { class: [foo, { bar }], style: { color: 'red' } })

// listener sự kiện cần truyền theo dạng onXxx
h('div', { onClick: () => {} })

// children có thể là chuỗi
h('div', { id: 'foo' }, 'hello')

// có thể bỏ props nếu không có props
h('div', 'hello')
h('div', [h('span', 'hello')])

// mảng children có thể trộn vnode và chuỗi
h('div', ['hello', h('span', 'hello')])
```

VNode tạo ra sẽ có dạng:

```js
const vnode = h('div', { id: 'foo' }, [])

vnode.type // 'div'
vnode.props // { id: 'foo' }
vnode.children // []
vnode.key // null
```

:::warning Lưu ý
Interface `VNode` đầy đủ còn có nhiều property nội bộ khác, nhưng bạn nên tránh phụ thuộc vào bất kỳ property nào ngoài những thứ được liệt kê ở đây. Điều này giúp tránh vỡ ngoài ý muốn khi property nội bộ thay đổi.
:::

### Khai Báo Render Function {#declaring-render-functions}

<div class="composition-api">

Khi dùng template với Composition API, giá trị trả về của hook `setup()` được dùng để lộ dữ liệu ra template. Nhưng khi dùng render function, ta có thể trả về trực tiếp chính render function:

```js
import { ref, h } from 'vue'

export default {
  props: {
    /* ... */
  },
  setup(props) {
    const count = ref(1)

    // trả về render function
    return () => h('div', props.msg + count.value)
  }
}
```

Render function được khai báo bên trong `setup()`, nên tự nhiên nó có quyền truy cập vào props và mọi state reactive được khai báo trong cùng scope.

Ngoài việc trả về một vnode duy nhất, bạn cũng có thể trả về chuỗi hoặc mảng:

```js
export default {
  setup() {
    return () => 'hello world!'
  }
}
```

```js
import { h } from 'vue'

export default {
  setup() {
    // dùng mảng để trả về nhiều node gốc
    return () => [
      h('div'),
      h('div'),
      h('div')
    ]
  }
}
```

:::tip
Hãy chắc chắn là bạn trả về một hàm thay vì trả về giá trị trực tiếp. `setup()` chỉ được gọi một lần cho mỗi component, còn render function trả về từ nó sẽ được gọi nhiều lần.
:::

</div>
<div class="options-api">

Ta có thể khai báo render function bằng option `render`:

```js
import { h } from 'vue'

export default {
  data() {
    return {
      msg: 'hello'
    }
  },
  render() {
    return h('div', this.msg)
  }
}
```

Hàm `render()` có thể truy cập instance component qua `this`.

Ngoài việc trả về một vnode duy nhất, bạn cũng có thể trả về chuỗi hoặc mảng:

```js
export default {
  render() {
    return 'hello world!'
  }
}
```

```js
import { h } from 'vue'

export default {
  render() {
    // dùng mảng để trả về nhiều node gốc
    return [
      h('div'),
      h('div'),
      h('div')
    ]
  }
}
```

</div>

Nếu component render function không cần state của instance, nó cũng có thể được khai báo trực tiếp thành một hàm để viết ngắn gọn hơn:

```js
function Hello() {
  return 'hello world!'
}
```

Đúng vậy, đây là một component Vue hợp lệ. Xem phần [Functional Components](#functional-components) để biết thêm về cú pháp này.

### VNode Phải Là Duy Nhất {#vnodes-must-be-unique}

Mọi vnode trong cây component đều phải là duy nhất. Nghĩa là render function sau là không hợp lệ:

```js
function render() {
  const p = h('p', 'hi')
  return h('div', [
    // Trùng vnode
    p,
    p
  ])
}
```

Nếu bạn thực sự muốn lặp cùng một phần tử / component nhiều lần, hãy dùng một hàm factory. Ví dụ, render function sau là cách hợp lệ để render 20 đoạn `<p>` giống hệt nhau:

```js
function render() {
  return h(
    'div',
    Array.from({ length: 20 }).map(() => {
      return h('p', 'hi')
    })
  )
}
```

### Dùng VNode Trong `<template>` {#using-vnodes-in-template}

```vue
<script setup>
import { h } from 'vue'

const vnode = h('button', ['Hello'])
</script>

<template>
  <!-- Qua <component /> -->
  <component :is="vnode">Hi</component>

  <!-- Hoặc dùng trực tiếp như phần tử -->
  <vnode />
  <vnode>Hi</vnode>
</template>
```

Một object vnode được khai báo trong `setup()` có thể được dùng như component bình thường để render.

:::warning
VNode đại diện cho một kết quả render đã được tạo sẵn, không phải định nghĩa component. Dùng vnode trong `<template>` không tạo ra instance component mới, và vnode sẽ được render nguyên như hiện có.

Mẫu này cần được dùng cẩn thận và không phải là thay thế cho component thông thường.
:::

## JSX / TSX {#jsx-tsx}

[JSX](https://facebook.github.io/jsx/) là một phần mở rộng giống XML cho JavaScript, cho phép ta viết mã như sau:

```jsx
const vnode = <div>hello</div>
```

Trong biểu thức JSX, hãy dùng dấu ngoặc nhọn để nhúng giá trị động:

```jsx
const vnode = <div id={dynamicId}>hello, {userName}</div>
```

`create-vue` và Vue CLI đều có tùy chọn scaffold dự án với hỗ trợ JSX được cấu hình sẵn. Nếu bạn tự cấu hình JSX, hãy xem tài liệu của [`@vue/babel-plugin-jsx`](https://github.com/vuejs/jsx-next).

Dù được React giới thiệu đầu tiên, JSX thực ra không có ngữ nghĩa runtime cố định và có thể được biên dịch thành nhiều kiểu đầu ra khác nhau. Nếu bạn đã từng làm việc với JSX, hãy lưu ý rằng **phép biến đổi JSX của Vue khác với React**, nên bạn không thể dùng JSX transform của React cho ứng dụng Vue. Một số khác biệt đáng chú ý:

- Bạn có thể dùng HTML attribute như `class` và `for` làm prop, không cần `className` hay `htmlFor`.
- Việc truyền children cho component (tức slot) [hoạt động khác](#passing-slots).

Định nghĩa kiểu của Vue cũng hỗ trợ type inference cho TSX. Khi dùng TSX, hãy nhớ đặt `"jsx": "preserve"` trong `tsconfig.json` để TypeScript giữ nguyên cú pháp JSX cho Vue JSX transform xử lý.

### Suy Luận Kiểu JSX {#jsx-type-inference}

Tương tự transform, JSX của Vue cũng cần định nghĩa kiểu riêng.

Từ Vue 3.4 trở đi, Vue không còn ngầm đăng ký namespace toàn cục `JSX`. Để hướng dẫn TypeScript dùng định nghĩa kiểu JSX của Vue, hãy đảm bảo `tsconfig.json` có phần sau:

```json
{
  "compilerOptions": {
    "jsx": "preserve",
    "jsxImportSource": "vue"
    // ...
  }
}
```

Bạn cũng có thể opt-in theo từng file bằng cách thêm chú thích `/* @jsxImportSource vue */` ở đầu file.

Nếu có mã phụ thuộc vào namespace toàn cục `JSX`, bạn có thể giữ nguyên hành vi toàn cục như trước 3.4 bằng cách import hoặc reference tường minh `vue/jsx` trong dự án, thứ sẽ đăng ký namespace `JSX` toàn cục.

## Một Số Mẫu Render Function Thường Gặp {#render-function-recipes}

Bên dưới là một số mẫu phổ biến để hiện thực tính năng template bằng render function / JSX tương đương.

### `v-if` {#v-if}

Template:

```vue-html
<div>
  <div v-if="ok">yes</div>
  <span v-else>no</span>
</div>
```

Render function / JSX tương đương:

<div class="composition-api">

```js
h('div', [ok.value ? h('div', 'yes') : h('span', 'no')])
```

```jsx
<div>{ok.value ? <div>yes</div> : <span>no</span>}</div>
```

</div>
<div class="options-api">

```js
h('div', [this.ok ? h('div', 'yes') : h('span', 'no')])
```

```jsx
<div>{this.ok ? <div>yes</div> : <span>no</span>}</div>
```

</div>

### `v-for` {#v-for}

Template:

```vue-html
<ul>
  <li v-for="{ id, text } in items" :key="id">
    {{ text }}
  </li>
</ul>
```

Render function / JSX tương đương:

<div class="composition-api">

```js
h(
  'ul',
  // giả sử `items` là ref chứa mảng
  items.value.map(({ id, text }) => {
    return h('li', { key: id }, text)
  })
)
```

```jsx
<ul>
  {items.value.map(({ id, text }) => {
    return <li key={id}>{text}</li>
  })}
</ul>
```

</div>
<div class="options-api">

```js
h(
  'ul',
  this.items.map(({ id, text }) => {
    return h('li', { key: id }, text)
  })
)
```

```jsx
<ul>
  {this.items.map(({ id, text }) => {
    return <li key={id}>{text}</li>
  })}
</ul>
```

</div>

### `v-on` {#v-on}

Prop có tên bắt đầu bằng `on` theo sau là một chữ cái viết hoa sẽ được xem là listener sự kiện. Ví dụ, `onClick` tương đương với `@click` trong template.

```js
h(
  'button',
  {
    onClick(event) {
      /* ... */
    }
  },
  'Click Me'
)
```

```jsx
<button
  onClick={(event) => {
    /* ... */
  }}
>
  Click Me
</button>
```

#### Modifier Sự Kiện {#event-modifiers}

Với các modifier sự kiện `.passive`, `.capture` và `.once`, bạn có thể ghép chúng sau tên sự kiện theo camelCase.

Ví dụ:

```js
h('input', {
  onClickCapture() {
    /* listener ở capture mode */
  },
  onKeyupOnce() {
    /* chỉ chạy một lần */
  },
  onMouseoverOnceCapture() {
    /* once + capture */
  }
})
```

```jsx
<input
  onClickCapture={() => {}}
  onKeyupOnce={() => {}}
  onMouseoverOnceCapture={() => {}}
/>
```

Với các event modifier và key modifier khác, bạn có thể dùng helper [`withModifiers`](/api/render-function#withmodifiers):

```js
import { withModifiers } from 'vue'

h('div', {
  onClick: withModifiers(() => {}, ['self'])
})
```

```jsx
<div onClick={withModifiers(() => {}, ['self'])} />
```

### Components {#components}

Để tạo vnode cho một component, đối số đầu tiên truyền vào `h()` phải là định nghĩa component. Điều này có nghĩa là khi dùng render function, bạn không cần đăng ký component, chỉ cần dùng trực tiếp component đã import:

```js
import Foo from './Foo.vue'
import Bar from './Bar.jsx'

function render() {
  return h('div', [h(Foo), h(Bar)])
}
```

```jsx
function render() {
  return (
    <div>
      <Foo />
      <Bar />
    </div>
  )
}
```

Như vậy, `h` có thể làm việc với component import từ bất kỳ định dạng file nào, miễn đó là component Vue hợp lệ.

Component động cũng rất dễ với render function:

```js
import Foo from './Foo.vue'
import Bar from './Bar.jsx'

function render() {
  return ok.value ? h(Foo) : h(Bar)
}
```

```jsx
function render() {
  return ok.value ? <Foo /> : <Bar />
}
```

Nếu component được đăng ký theo tên và không thể import trực tiếp, ví dụ do thư viện đăng ký toàn cục, bạn có thể resolve bằng helper [`resolveComponent()`](/api/render-function#resolvecomponent).

### Render Slot {#rendering-slots}

<div class="composition-api">

Trong render function, slot có thể truy cập từ context của `setup()`. Mỗi slot trên object `slots` là một **hàm trả về mảng vnode**:

```js
export default {
  props: ['message'],
  setup(props, { slots }) {
    return () => [
      // slot mặc định:
      // <div><slot /></div>
      h('div', slots.default()),

      // named slot:
      // <div><slot name="footer" :text="message" /></div>
      h(
        'div',
        slots.footer({
          text: props.message
        })
      )
    ]
  }
}
```

Tương đương JSX:

```jsx
// mặc định
<div>{slots.default()}</div>

// named
<div>{slots.footer({ text: props.message })}</div>
```

</div>
<div class="options-api">

Trong render function, slot có thể truy cập từ [`this.$slots`](/api/component-instance#slots):

```js
export default {
  props: ['message'],
  render() {
    return [
      // <div><slot /></div>
      h('div', this.$slots.default()),

      // <div><slot name="footer" :text="message" /></div>
      h(
        'div',
        this.$slots.footer({
          text: this.message
        })
      )
    ]
  }
}
```

Tương đương JSX:

```jsx
// <div><slot /></div>
<div>{this.$slots.default()}</div>

// <div><slot name="footer" :text="message" /></div>
<div>{this.$slots.footer({ text: this.message })}</div>
```

</div>

### Truyền Slot {#passing-slots}

Việc truyền children cho component hơi khác so với truyền children cho phần tử HTML. Thay vì mảng, ta cần truyền hoặc một hàm slot, hoặc một object các hàm slot. Hàm slot có thể trả về bất kỳ thứ gì mà render function bình thường có thể trả về, và khi được truy cập trong component con, chúng luôn được chuẩn hóa thành mảng vnode.

```js
// một slot mặc định duy nhất
h(MyComponent, () => 'hello')

// named slot
// lưu ý `null` là bắt buộc để tránh
// object slot bị xem là props
h(MyComponent, null, {
  default: () => 'default slot',
  foo: () => h('div', 'foo'),
  bar: () => [h('span', 'one'), h('span', 'two')]
})
```

Tương đương JSX:

```jsx
// mặc định
<MyComponent>{() => 'hello'}</MyComponent>

// named
<MyComponent>{{
  default: () => 'default slot',
  foo: () => <div>foo</div>,
  bar: () => [<span>one</span>, <span>two</span>]
}}</MyComponent>
```

Việc truyền slot dưới dạng hàm cho phép component con gọi chúng một cách lazy. Nhờ đó dependency của slot sẽ được track bởi component con thay vì component cha, giúp việc cập nhật chính xác và hiệu quả hơn.

### Scoped Slot {#scoped-slots}

Để render scoped slot trong component cha, ta truyền một slot xuống component con. Hãy để ý slot lúc này có tham số `text`. Slot sẽ được gọi trong component con và dữ liệu từ component con sẽ được truyền ngược lên component cha.

```js
// component cha
export default {
  setup() {
    return () => h(MyComp, null, {
      default: ({ text }) => h('p', text)
    })
  }
}
```

Hãy nhớ truyền `null` để slot không bị xem là props.

```js
// component con
export default {
  setup(props, { slots }) {
    const text = ref('hi')
    return () => h('div', null, slots.default({ text: text.value }))
  }
}
```

Tương đương JSX:

```jsx
<MyComponent>{{
  default: ({ text }) => <p>{ text }</p>
}}</MyComponent>
```

### Component Dựng Sẵn {#built-in-components}

[Các component dựng sẵn](/api/built-in-components) như `<KeepAlive>`, `<Transition>`, `<TransitionGroup>`, `<Teleport>` và `<Suspense>` phải được import khi dùng trong render function:

<div class="composition-api">

```js
import { h, KeepAlive, Teleport, Transition, TransitionGroup } from 'vue'

export default {
  setup () {
    return () => h(Transition, { mode: 'out-in' }, /* ... */)
  }
}
```

</div>
<div class="options-api">

```js
import { h, KeepAlive, Teleport, Transition, TransitionGroup } from 'vue'

export default {
  render () {
    return h(Transition, { mode: 'out-in' }, /* ... */)
  }
}
```

</div>

### `v-model` {#v-model}

Directive `v-model` sẽ được mở rộng thành prop `modelValue` và `onUpdate:modelValue` trong quá trình biên dịch template, nên ta phải tự truyền các prop này:

<div class="composition-api">

```js
export default {
  props: ['modelValue'],
  emits: ['update:modelValue'],
  setup(props, { emit }) {
    return () =>
      h(SomeComponent, {
        modelValue: props.modelValue,
        'onUpdate:modelValue': (value) => emit('update:modelValue', value)
      })
  }
}
```

</div>
<div class="options-api">

```js
export default {
  props: ['modelValue'],
  emits: ['update:modelValue'],
  render() {
    return h(SomeComponent, {
      modelValue: this.modelValue,
      'onUpdate:modelValue': (value) => this.$emit('update:modelValue', value)
    })
  }
}
```

</div>

### Custom Directive {#custom-directives}

Custom directive có thể được áp dụng lên vnode bằng [`withDirectives`](/api/render-function#withdirectives):

```js
import { h, withDirectives } from 'vue'

// một custom directive
const pin = {
  mounted() { /* ... */ },
  updated() { /* ... */ }
}

// <div v-pin:top.animate="200"></div>
const vnode = withDirectives(h('div'), [
  [pin, 200, 'top', { animate: true }]
])
```

Nếu directive được đăng ký theo tên và không thể import trực tiếp, bạn có thể resolve nó bằng helper [`resolveDirective`](/api/render-function#resolvedirective).

### Template Ref {#template-refs}

<div class="composition-api">

Với Composition API, khi dùng [`useTemplateRef()`](/api/composition-api-helpers#usetemplateref) <sup class="vt-badge" data-text="3.5+" />, template ref được tạo bằng cách truyền giá trị chuỗi làm prop cho vnode:

```js
import { h, useTemplateRef } from 'vue'

export default {
  setup() {
    const divEl = useTemplateRef('my-div')

    // <div ref="my-div">
    return () => h('div', { ref: 'my-div' })
  }
}
```

<details>
<summary>Cách dùng trước 3.5</summary>

Trong các phiên bản trước 3.5, khi `useTemplateRef()` chưa có, template ref được tạo bằng cách truyền chính `ref()` làm prop cho vnode:

```js
import { h, ref } from 'vue'

export default {
  setup() {
    const divEl = ref()

    // <div ref="divEl">
    return () => h('div', { ref: divEl })
  }
}
```
</details>
</div>
<div class="options-api">

Với Options API, template ref được tạo bằng cách truyền tên ref dưới dạng chuỗi trong props của vnode:

```js
export default {
  render() {
    // <div ref="divEl">
    return h('div', { ref: 'divEl' })
  }
}
```

</div>

## Functional Components {#functional-components}

Functional component là một dạng component thay thế, không có state riêng. Chúng hoạt động như hàm thuần: props vào, vnode ra. Chúng được render mà không tạo instance component, tức là không có `this`, và cũng không có các hook vòng đời component như thông thường.

Để tạo functional component, ta dùng một hàm thuần thay vì object option. Hàm đó về bản chất chính là `render` function của component.

<div class="composition-api">

Chữ ký của functional component giống với hook `setup()`:

```js
function MyComponent(props, { slots, emit, attrs }) {
  // ...
}
```

</div>
<div class="options-api">

Vì không có tham chiếu `this` cho functional component, Vue sẽ truyền `props` làm đối số đầu tiên:

```js
function MyComponent(props, context) {
  // ...
}
```

Đối số thứ hai `context` chứa ba property: `attrs`, `emit` và `slots`. Chúng lần lượt tương đương với [`$attrs`](/api/component-instance#attrs), [`$emit`](/api/component-instance#emit) và [`$slots`](/api/component-instance#slots) của instance.

</div>

Phần lớn option cấu hình thông thường của component không có sẵn cho functional component. Tuy nhiên, bạn vẫn có thể định nghĩa [`props`](/api/options-state#props) và [`emits`](/api/options-state#emits) bằng cách gắn chúng làm property:

```js
MyComponent.props = ['value']
MyComponent.emits = ['click']
```

Nếu option `props` không được khai báo, object `props` truyền vào hàm sẽ chứa toàn bộ attribute, tương tự `attrs`. Tên prop sẽ không được chuẩn hóa sang camelCase trừ khi `props` được khai báo.

Với functional component có khai báo `props` tường minh, [thuộc tính kế thừa](/guide/components/attrs) hoạt động gần giống component thường. Nhưng với functional component không khai báo `props`, mặc định chỉ `class`, `style` và listener sự kiện `onXxx` mới được kế thừa từ `attrs`. Trong cả hai trường hợp, bạn có thể đặt `inheritAttrs` thành `false` để tắt kế thừa attribute:

```js
MyComponent.inheritAttrs = false
```

Functional component có thể được đăng ký và dùng giống như component thường. Nếu bạn truyền một hàm làm đối số đầu tiên cho `h()`, Vue sẽ coi đó là functional component.

### Gắn Kiểu Cho Functional Components<sup class="vt-badge ts" /> {#typing-functional-components}

Functional component có thể được gắn kiểu tùy theo việc chúng có tên hay ẩn danh. [Vue - Official extension](https://github.com/vuejs/language-tools) cũng hỗ trợ kiểm tra kiểu cho functional component được khai báo đúng cách khi dùng trong template SFC.

**Named Functional Component**

```tsx
import type { SetupContext } from 'vue'
type FComponentProps = {
  message: string
}

type Events = {
  sendMessage(message: string): void
}

function FComponent(
  props: FComponentProps,
  context: SetupContext<Events>
) {
  return (
    <button onClick={() => context.emit('sendMessage', props.message)}>
        {props.message} {' '}
    </button>
  )
}

FComponent.props = {
  message: {
    type: String,
    required: true
  }
}

FComponent.emits = {
  sendMessage: (value: unknown) => typeof value === 'string'
}
```

**Anonymous Functional Component**

```tsx
import type { FunctionalComponent } from 'vue'

type FComponentProps = {
  message: string
}

type Events = {
  sendMessage(message: string): void
}

const FComponent: FunctionalComponent<FComponentProps, Events> = (
  props,
  context
) => {
  return (
    <button onClick={() => context.emit('sendMessage', props.message)}>
      {props.message}{' '}
    </button>
  )
}

FComponent.props = {
  message: {
    type: String,
    required: true
  }
}

FComponent.emits = {
  sendMessage: (value) => typeof value === 'string'
}
```
