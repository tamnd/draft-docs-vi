# Custom Directive {#custom-directives}

<script setup>
const vHighlight = {
  mounted: el => {
    el.classList.add('is-highlight')
  }
}
</script>

<style>
.vt-doc p.is-highlight {
  margin-bottom: 0;
}

.is-highlight {
  background-color: yellow;
  color: black;
}
</style>

## Giới Thiệu {#introduction}

Ngoài tập directive mặc định đi kèm trong core (như `v-model` hoặc `v-show`), Vue còn cho phép bạn đăng ký custom directive của riêng mình.

Ta đã giới thiệu hai hình thức tái sử dụng mã trong Vue: [component](/guide/essentials/component-basics) và [composable](./composables). Component là khối xây dựng chính, còn composable tập trung vào việc tái sử dụng logic có state. Trong khi đó, custom directive chủ yếu được dùng để tái sử dụng logic có liên quan tới thao tác DOM ở mức thấp trên các phần tử thuần.

Custom directive được định nghĩa dưới dạng object chứa các hook vòng đời tương tự như của component. Các hook này nhận phần tử mà directive đang bind vào. Đây là ví dụ về một directive thêm class vào phần tử khi nó được Vue chèn vào DOM:

<div class="composition-api">

```vue
<script setup>
// bật v-highlight trong template
const vHighlight = {
  mounted: (el) => {
    el.classList.add('is-highlight')
  }
}
</script>

<template>
  <p v-highlight>This sentence is important!</p>
</template>
```

</div>

<div class="options-api">

```js
const highlight = {
  mounted: (el) => el.classList.add('is-highlight')
}

export default {
  directives: {
    // bật v-highlight trong template
    highlight
  }
}
```

```vue-html
<p v-highlight>This sentence is important!</p>
```

</div>

<div class="demo">
  <p v-highlight>This sentence is important!</p>
</div>

<div class="composition-api">

Trong `<script setup>`, mọi biến camelCase bắt đầu bằng tiền tố `v` đều có thể được dùng như custom directive. Trong ví dụ trên, `vHighlight` có thể được dùng trong template dưới dạng `v-highlight`.

Nếu bạn không dùng `<script setup>`, custom directive có thể được đăng ký bằng option `directives`:

```js
export default {
  setup() {
    /*...*/
  },
  directives: {
    // bật v-highlight trong template
    highlight: {
      /* ... */
    }
  }
}
```

</div>

<div class="options-api">

Tương tự component, custom directive phải được đăng ký trước thì mới dùng được trong template. Trong ví dụ trên, ta đang dùng cách đăng ký cục bộ thông qua option `directives`.

</div>

Việc đăng ký custom directive toàn cục ở cấp ứng dụng cũng rất phổ biến:

```js
const app = createApp({})

// làm cho v-highlight dùng được trong mọi component
app.directive('highlight', {
  /* ... */
})
```

Bạn có thể gắn kiểu cho global custom directive bằng cách mở rộng interface `GlobalDirectives` từ `vue`.

Chi tiết hơn: [Typing Custom Global Directives](/guide/typescript/composition-api#typing-global-custom-directives) <sup class="vt-badge ts" />

## Khi Nào Nên Dùng Custom Directive {#when-to-use}

Custom directive chỉ nên được dùng khi chức năng mong muốn chỉ có thể đạt được thông qua thao tác DOM trực tiếp.

Một ví dụ phổ biến là custom directive `v-focus`, dùng để đưa một phần tử vào trạng thái focus.

<div class="composition-api">

```vue
<script setup>
// bật v-focus trong template
const vFocus = {
  mounted: (el) => el.focus()
}
</script>

<template>
  <input v-focus />
</template>
```

</div>

<div class="options-api">

```js
const focus = {
  mounted: (el) => el.focus()
}

export default {
  directives: {
    // bật v-focus trong template
    focus
  }
}
```

```vue-html
<input v-focus />
```

</div>

Directive này hữu ích hơn thuộc tính `autofocus` vì nó không chỉ hoạt động lúc tải trang, mà còn hoạt động khi phần tử được Vue chèn vào một cách động.

Trong trường hợp có thể, hãy ưu tiên template khai báo cùng directive dựng sẵn như `v-bind`, vì chúng hiệu quả hơn và thân thiện với kết xuất phía máy chủ hơn.

## Hook Của Directive {#directive-hooks}

Object định nghĩa directive có thể cung cấp một số hàm hook (tất cả đều là tùy chọn):

```js
const myDirective = {
  // được gọi trước khi các thuộc tính
  // hoặc listener sự kiện của phần tử được áp dụng
  created(el, binding, vnode) {
    // xem phần dưới để biết chi tiết về các đối số
  },
  // được gọi ngay trước khi phần tử được chèn vào DOM.
  beforeMount(el, binding, vnode) {},
  // được gọi khi component cha của phần tử được bind
  // và toàn bộ các component con của nó đã được mount.
  mounted(el, binding, vnode) {},
  // được gọi trước khi component cha được cập nhật
  beforeUpdate(el, binding, vnode, prevVnode) {},
  // được gọi sau khi component cha
  // và toàn bộ component con của nó đã cập nhật xong
  updated(el, binding, vnode, prevVnode) {},
  // được gọi trước khi component cha bị unmount
  beforeUnmount(el, binding, vnode) {},
  // được gọi khi component cha bị unmount
  unmounted(el, binding, vnode) {}
}
```

### Đối Số Của Hook {#hook-arguments}

Các hook của directive sẽ nhận các đối số sau:

- `el`: phần tử mà directive đang bind vào. Đối số này có thể được dùng để thao tác DOM trực tiếp.

- `binding`: một object chứa các property sau.

  - `value`: giá trị được truyền vào directive. Ví dụ trong `v-my-directive="1 + 1"`, giá trị sẽ là `2`.
  - `oldValue`: giá trị trước đó, chỉ khả dụng trong `beforeUpdate` và `updated`. Nó luôn có mặt bất kể giá trị có thay đổi hay không.
  - `arg`: đối số được truyền vào directive, nếu có. Ví dụ trong `v-my-directive:foo`, `arg` sẽ là `"foo"`.
  - `modifiers`: object chứa modifier, nếu có. Ví dụ trong `v-my-directive.foo.bar`, object `modifiers` sẽ là `{ foo: true, bar: true }`.
  - `instance`: instance của component nơi directive được dùng.
  - `dir`: object định nghĩa directive.

- `vnode`: VNode nền tảng đại diện cho phần tử đang được bind.
- `prevVnode`: VNode đại diện cho phần tử đã bind ở lần render trước. Chỉ khả dụng trong hook `beforeUpdate` và `updated`.

Lấy ví dụ với cách dùng directive sau:

```vue-html
<div v-example:foo.bar="baz">
```

Đối số `binding` sẽ có dạng:

```js
{
  arg: 'foo',
  modifiers: { bar: true },
  value: /* giá trị của `baz` */,
  oldValue: /* giá trị của `baz` ở lần cập nhật trước */
}
```

Tương tự directive dựng sẵn, đối số của custom directive cũng có thể là động. Ví dụ:

```vue-html
<div v-example:[arg]="value"></div>
```

Ở đây, đối số của directive sẽ được cập nhật một cách phản ứng dựa trên property `arg` trong state của component.

:::tip Lưu ý
Ngoài `el`, bạn nên xem các đối số này là chỉ đọc và không bao giờ chỉnh sửa chúng. Nếu cần chia sẻ thông tin giữa các hook, bạn nên làm điều đó thông qua [dataset](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/dataset) của phần tử.
:::

## Cú Pháp Rút Gọn Bằng Hàm {#function-shorthand}

Rất thường gặp trường hợp một custom directive có cùng hành vi cho `mounted` và `updated`, và không cần đến các hook khác. Khi đó ta có thể định nghĩa directive dưới dạng một hàm:

```vue-html
<div v-color="color"></div>
```

```js
app.directive('color', (el, binding) => {
  // sẽ được gọi cho cả `mounted` lẫn `updated`
  el.style.color = binding.value
})
```

## Object Literal {#object-literals}

Nếu directive của bạn cần nhiều giá trị, bạn cũng có thể truyền vào một object literal JavaScript. Hãy nhớ rằng directive có thể nhận mọi biểu thức JavaScript hợp lệ.

```vue-html
<div v-demo="{ color: 'white', text: 'hello!' }"></div>
```

```js
app.directive('demo', (el, binding) => {
  console.log(binding.value.color) // => "white"
  console.log(binding.value.text) // => "hello!"
})
```

## Cách Dùng Trên Component {#usage-on-components}

:::warning Không được khuyến nghị
Không nên dùng custom directive trên component. Hành vi không mong muốn có thể xảy ra khi component có nhiều node gốc.
:::

Khi được dùng trên component, custom directive sẽ luôn áp dụng lên node gốc của component, tương tự như [thuộc tính kế thừa](/guide/components/attrs).

```vue-html
<MyComponent v-demo="test" />
```

```vue-html
<!-- template của MyComponent -->

<div> <!-- directive v-demo sẽ được áp dụng ở đây -->
  <span>My component content</span>
</div>
```

Lưu ý rằng component có thể có nhiều hơn một node gốc. Khi áp dụng lên component nhiều node gốc, directive sẽ bị bỏ qua và một cảnh báo sẽ được đưa ra. Khác với thuộc tính, directive không thể được chuyển sang phần tử khác bằng `v-bind="$attrs"`.
