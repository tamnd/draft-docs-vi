# Class và Style Bindings {#class-and-style-bindings}

Một nhu cầu phổ biến của data binding là thao tác với danh sách class và style inline của một phần tử. Vì `class` và `style` đều là attribute, chúng ta có thể dùng `v-bind` để gán giá trị chuỗi một cách động, giống như với các attribute khác. Tuy nhiên, việc tạo các giá trị đó bằng cách nối chuỗi (string concatenation) có thể gây phiền và dễ lỗi. Vì lý do này, Vue cung cấp các cải tiến đặc biệt khi `v-bind` được dùng với `class` và `style`. Ngoài chuỗi, các biểu thức cũng có thể trả về object hoặc array.

## Binding HTML Classes {#binding-html-classes}

<div class="options-api">
  <VueSchoolLink href="https://vueschool.io/lessons/dynamic-css-classes-with-vue-3" title="Free Vue.js Dynamic CSS Classes Lesson"/>
</div>

<div class="composition-api">
  <VueSchoolLink href="https://vueschool.io/lessons/vue-fundamentals-capi-dynamic-css-classes-with-vue" title="Free Vue.js Dynamic CSS Classes Lesson"/>
</div>

### Binding với Object {#binding-to-objects}

Chúng ta có thể truyền một object vào `:class` (viết tắt của `v-bind:class`) để bật tắt class một cách động:

```vue-html
<div :class="{ active: isActive }"></div>
```

Cú pháp trên có nghĩa là việc có hay không class `active` sẽ được quyết định bởi [truthiness (giá trị truthy)](https://developer.mozilla.org/en-US/docs/Glossary/Truthy) của thuộc tính dữ liệu `isActive`.

Bạn có thể bật tắt nhiều class bằng cách thêm nhiều field trong object. Ngoài ra, directive `:class` cũng có thể tồn tại cùng với attribute `class` thông thường. Với state như sau:

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

Nó sẽ render:

```vue-html
<div class="static active"></div>
```

Khi `isActive` hoặc `hasError` thay đổi, danh sách class sẽ được cập nhật tương ứng. Ví dụ, nếu `hasError` trở thành `true`, danh sách class sẽ trở thành `"static active text-danger"`.

Object được bind không nhất thiết phải viết inline:

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

Điều này sẽ render:

```vue-html
<div class="active"></div>
```

Chúng ta cũng có thể bind tới một [computed property](./computed) trả về object. Đây là một pattern (mẫu) phổ biến và mạnh mẽ:

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

### Binding với Array {#binding-to-arrays}

Chúng ta có thể bind `:class` với một array để áp dụng danh sách class:

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

Sẽ render:

```vue-html
<div class="active text-danger"></div>
```

Nếu bạn muốn bật tắt một class trong danh sách theo điều kiện, bạn có thể dùng biểu thức ternary:

```vue-html
<div :class="[isActive ? activeClass : '', errorClass]"></div>
```

Điều này sẽ luôn áp dụng `errorClass`, nhưng `activeClass` chỉ được áp dụng khi `isActive` là truthy.

Tuy nhiên, cách này có thể hơi dài nếu bạn có nhiều class điều kiện. Vì vậy, bạn cũng có thể dùng cú pháp object bên trong cú pháp array:

```vue-html
<div :class="[{ [activeClass]: isActive }, errorClass]"></div>
```

### Với Component {#with-components}

> Phần này giả định bạn đã biết về [Components](/guide/essentials/component-basics). Bạn có thể bỏ qua và quay lại sau.

Khi bạn sử dụng attribute `class` trên một component có một root element duy nhất, các class đó sẽ được thêm vào root element của component và được gộp với các class đã có sẵn.

Ví dụ, nếu chúng ta có một component tên `MyComponent` với template như sau:

```vue-html
<!-- template của component con -->
<p class="foo bar">Hi!</p>
```

Sau đó thêm một số class khi sử dụng:

```vue-html
<!-- khi sử dụng component -->
<MyComponent class="baz boo" />
```

HTML render sẽ là:

```vue-html
<p class="foo bar baz boo">Hi!</p>
```

Điều tương tự cũng áp dụng cho class binding:

```vue-html
<MyComponent :class="{ active: isActive }" />
```

Khi `isActive` là truthy, HTML render sẽ là:

```vue-html
<p class="foo bar active">Hi!</p>
```

Nếu component của bạn có nhiều root element, bạn cần xác định phần tử nào sẽ nhận class này. Bạn có thể làm điều này bằng cách sử dụng property `$attrs` của component:

```vue-html
<!-- template MyComponent sử dụng $attrs -->
<p :class="$attrs.class">Hi!</p>
<span>This is a child component</span>
```

```vue-html
<MyComponent class="baz" />
```

Sẽ render:

```html
<p class="baz">Hi!</p>
<span>This is a child component</span>
```

Bạn có thể tìm hiểu thêm về kế thừa attribute của component trong phần [Fallthrough Attributes](/guide/components/attrs).

## Binding Inline Styles {#binding-inline-styles}

### Binding với Object {#binding-to-objects-1}

`:style` hỗ trợ bind với giá trị object JavaScript, tương ứng với [property `style` của HTML element](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/style):

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

Mặc dù khuyến nghị dùng key dạng camelCase, `:style` cũng hỗ trợ key CSS dạng kebab-case (giống như cách dùng trong CSS thực tế), ví dụ:

```vue-html
<div :style="{ 'font-size': fontSize + 'px' }"></div>
```

Thường thì nên bind trực tiếp tới một object style để template gọn gàng hơn:

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

Tương tự, style binding dạng object thường được dùng cùng với computed property trả về object.

Directive `:style` cũng có thể tồn tại cùng với attribute `style` thông thường, giống như `:class`.

Template:

```vue-html
<h1 style="color: red" :style="'font-size: 1em'">hello</h1>
```

Sẽ render:

```vue-html
<h1 style="color: red; font-size: 1em;">hello</h1>
```

### Binding với Array {#binding-to-arrays-1}

Chúng ta có thể bind `:style` với một array gồm nhiều object style. Các object này sẽ được gộp lại và áp dụng cho cùng một phần tử:

```vue-html
<div :style="[baseStyles, overridingStyles]"></div>
```

### Auto-prefixing {#auto-prefixing}

Khi bạn sử dụng một CSS property cần [vendor prefix (tiền tố trình duyệt)](https://developer.mozilla.org/en-US/docs/Glossary/Vendor_Prefix) trong `:style`, Vue sẽ tự động thêm tiền tố phù hợp. Vue làm điều này bằng cách kiểm tra lúc runtime để xem property style nào được hỗ trợ trong trình duyệt hiện tại. Nếu trình duyệt không hỗ trợ một property cụ thể, các biến thể có prefix khác nhau sẽ được thử để tìm ra cái được hỗ trợ.

### Multiple Values {#multiple-values}

Bạn có thể cung cấp một array gồm nhiều giá trị (có prefix) cho một property style, ví dụ:

```vue-html
<div :style="{ display: ['-webkit-box', '-ms-flexbox', 'flex'] }"></div>
```

Điều này sẽ chỉ render giá trị cuối cùng trong array mà trình duyệt hỗ trợ. Trong ví dụ này, nó sẽ render `display: flex` đối với các trình duyệt hỗ trợ phiên bản không có prefix của flexbox.
