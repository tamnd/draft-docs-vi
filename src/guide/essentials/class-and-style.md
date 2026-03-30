# Ràng buộc class và style {#class-and-style-bindings}

Một nhu cầu rất thường gặp khi bind dữ liệu là thay đổi danh sách class và inline style của phần tử. Vì `class` và `style` đều là thuộc tính, chúng ta có thể dùng `v-bind` để gán giá trị chuỗi cho chúng một cách động, giống như với các thuộc tính khác. Tuy nhiên, nếu cứ nối chuỗi để tạo ra những giá trị này thì rất dễ rối và dễ sai. Vì vậy, Vue có thêm một số hỗ trợ riêng khi `v-bind` được dùng với `class` và `style`. Ngoài chuỗi, biểu thức ở đây còn có thể trả về object hoặc mảng.

## Ràng buộc class HTML {#binding-html-classes}

<div class="options-api">
  <VueSchoolLink href="https://vueschool.io/lessons/dynamic-css-classes-with-vue-3" title="Bài học miễn phí về class CSS động trong Vue.js"/>
</div>

<div class="composition-api">
  <VueSchoolLink href="https://vueschool.io/lessons/vue-fundamentals-capi-dynamic-css-classes-with-vue" title="Bài học miễn phí về class CSS động trong Vue.js"/>
</div>

### Bind với object {#binding-to-objects}

Chúng ta có thể truyền một object vào `:class`, viết tắt của `v-bind:class`, để bật/tắt class một cách động:

```vue-html
<div :class="{ active: isActive }"></div>
```

Cú pháp trên có nghĩa là việc class `active` có xuất hiện hay không sẽ phụ thuộc vào [tính truthy](https://developer.mozilla.org/en-US/docs/Glossary/Truthy) của thuộc tính dữ liệu `isActive`.

Bạn có thể bật/tắt nhiều class cùng lúc bằng cách thêm nhiều field hơn vào object. Ngoài ra, directive `:class` vẫn có thể cùng tồn tại với thuộc tính `class` thông thường. Ví dụ, với state như sau:

<div class="composition-api">

```js
const isActive = ref(true)
const hasError = ref(false)
```

</div>

<div class="options-api">

```js
data() {
  return {
    isActive: true,
    hasError: false
  }
}
```

</div>

Và template như sau:

```vue-html
<div
  class="static"
  :class="{ active: isActive, 'text-danger': hasError }"
></div>
```

Kết quả render sẽ là:

```vue-html
<div class="static active"></div>
```

Khi `isActive` hoặc `hasError` thay đổi, danh sách class cũng sẽ được cập nhật theo. Ví dụ, nếu `hasError` trở thành `true`, danh sách class sẽ là `"static active text-danger"`.

Object dùng để bind không nhất thiết phải viết inline:

<div class="composition-api">

```js
const classObject = reactive({
  active: true,
  'text-danger': false
})
```

</div>

<div class="options-api">

```js
data() {
  return {
    classObject: {
      active: true,
      'text-danger': false
    }
  }
}
```

</div>

```vue-html
<div :class="classObject"></div>
```

Kết quả render sẽ là:

```vue-html
<div class="active"></div>
```

Chúng ta cũng có thể bind với một [computed property](./computed) trả về object. Đây là một cách làm rất phổ biến và khá mạnh:

<div class="composition-api">

```js
const isActive = ref(true)
const error = ref(null)

const classObject = computed(() => ({
  active: isActive.value && !error.value,
  'text-danger': error.value && error.value.type === 'fatal'
}))
```

</div>

<div class="options-api">

```js
data() {
  return {
    isActive: true,
    error: null
  }
},
computed: {
  classObject() {
    return {
      active: this.isActive && !this.error,
      'text-danger': this.error && this.error.type === 'fatal'
    }
  }
}
```

</div>

```vue-html
<div :class="classObject"></div>
```

### Bind với mảng {#binding-to-arrays}

Chúng ta có thể bind `:class` với một mảng để áp dụng một danh sách class:

<div class="composition-api">

```js
const activeClass = ref('active')
const errorClass = ref('text-danger')
```

</div>

<div class="options-api">

```js
data() {
  return {
    activeClass: 'active',
    errorClass: 'text-danger'
  }
}
```

</div>

```vue-html
<div :class="[activeClass, errorClass]"></div>
```

Kết quả render sẽ là:

```vue-html
<div class="active text-danger"></div>
```

Nếu bạn muốn bật/tắt một class trong danh sách theo điều kiện, bạn có thể làm điều đó bằng toán tử ba ngôi:

```vue-html
<div :class="[isActive ? activeClass : '', errorClass]"></div>
```

`errorClass` sẽ luôn được áp dụng, còn `activeClass` chỉ được áp dụng khi `isActive` là truthy.

Tuy nhiên, cách này sẽ hơi dài dòng nếu bạn có nhiều class theo điều kiện. Vì vậy, bạn cũng có thể dùng cú pháp object bên trong cú pháp mảng:

```vue-html
<div :class="[{ [activeClass]: isActive }, errorClass]"></div>
```

### Dùng với component {#with-components}

> Phần này giả định rằng bạn đã biết về [Cơ bản về component](/guide/essentials/component-basics). Nếu muốn, bạn có thể bỏ qua và quay lại sau.

Khi bạn dùng thuộc tính `class` trên một component chỉ có một root element, những class đó sẽ được thêm vào root element của component và gộp chung với các class sẵn có trên nó.

Ví dụ, nếu chúng ta có một component tên là `MyComponent` với template như sau:

```vue-html
<!-- template của component con -->
<p class="foo bar">Xin chào!</p>
```

Sau đó thêm một vài class khi dùng nó:

```vue-html
<!-- khi dùng component -->
<MyComponent class="baz boo" />
```

HTML sau khi render sẽ là:

```vue-html
<p class="foo bar baz boo">Xin chào!</p>
```

Điều tương tự cũng đúng với class binding:

```vue-html
<MyComponent :class="{ active: isActive }" />
```

Khi `isActive` là truthy, HTML sau khi render sẽ là:

```vue-html
<p class="foo bar active">Xin chào!</p>
```

Nếu component của bạn có nhiều root element, bạn sẽ cần chỉ rõ phần tử nào sẽ nhận class đó. Bạn có thể làm điều này bằng thuộc tính component `$attrs`:

```vue-html
<!-- template của MyComponent dùng $attrs -->
<p :class="$attrs.class">Xin chào!</p>
<span>Đây là một component con</span>
```

```vue-html
<MyComponent class="baz" />
```

Kết quả render sẽ là:

```html
<p class="baz">Xin chào!</p>
<span>Đây là một component con</span>
```

Bạn có thể tìm hiểu thêm về việc kế thừa thuộc tính của component trong phần [Fallthrough Attributes](/guide/components/attrs).

## Ràng buộc inline style {#binding-inline-styles}

### Bind với object {#binding-to-objects-1}

`:style` hỗ trợ bind với giá trị object JavaScript, tương ứng với [thuộc tính `style` của phần tử HTML](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/style):

<div class="composition-api">

```js
const activeColor = ref('red')
const fontSize = ref(30)
```

</div>

<div class="options-api">

```js
data() {
  return {
    activeColor: 'red',
    fontSize: 30
  }
}
```

</div>

```vue-html
<div :style="{ color: activeColor, fontSize: fontSize + 'px' }"></div>
```

Dù key dạng camelCase được khuyến nghị, `:style` vẫn hỗ trợ key thuộc tính CSS dạng kebab-case, đúng như cách chúng được viết trong CSS thực tế. Ví dụ:

```vue-html
<div :style="{ 'font-size': fontSize + 'px' }"></div>
```

Thường thì bind trực tiếp với một object style sẽ giúp template gọn hơn:

<div class="composition-api">

```js
const styleObject = reactive({
  color: 'red',
  fontSize: '30px'
})
```

</div>

<div class="options-api">

```js
data() {
  return {
    styleObject: {
      color: 'red',
      fontSize: '13px'
    }
  }
}
```

</div>

```vue-html
<div :style="styleObject"></div>
```

Tương tự, kiểu bind style bằng object cũng thường đi cùng với computed property trả về object.

Directive `:style` cũng có thể cùng tồn tại với thuộc tính `style` thông thường, giống như `:class`.

Mẫu template:

```vue-html
<h1 style="color: red" :style="'font-size: 1em'">xin chào</h1>
```

Kết quả render sẽ là:

```vue-html
<h1 style="color: red; font-size: 1em;">xin chào</h1>
```

### Bind với mảng {#binding-to-arrays-1}

Chúng ta có thể bind `:style` với một mảng gồm nhiều object style. Các object này sẽ được gộp lại và áp dụng lên cùng một phần tử:

```vue-html
<div :style="[baseStyles, overridingStyles]"></div>
```

### Tự thêm tiền tố {#auto-prefixing}

Khi bạn dùng một thuộc tính CSS cần [vendor prefix](https://developer.mozilla.org/en-US/docs/Glossary/Vendor_Prefix) trong `:style`, Vue sẽ tự động thêm tiền tố phù hợp. Vue làm điều này bằng cách kiểm tra ở runtime xem trình duyệt hiện tại hỗ trợ thuộc tính style nào. Nếu trình duyệt không hỗ trợ một thuộc tính nào đó, Vue sẽ thử các biến thể có tiền tố để tìm ra biến thể được hỗ trợ.

### Nhiều giá trị {#multiple-values}

Bạn có thể truyền một mảng gồm nhiều giá trị, kể cả các giá trị có tiền tố, cho một thuộc tính style. Ví dụ:

```vue-html
<div :style="{ display: ['-webkit-box', '-ms-flexbox', 'flex'] }"></div>
```

Vue sẽ chỉ render giá trị cuối cùng trong mảng mà trình duyệt hỗ trợ. Trong ví dụ này, với các trình duyệt hỗ trợ flexbox không cần tiền tố, kết quả sẽ là `display: flex`.
