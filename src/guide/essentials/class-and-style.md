# Ràng Buộc Class và Style {#class-and-style-bindings}

Một nhu cầu phổ biến khi ràng buộc dữ liệu là thao tác với danh sách class và inline style của một phần tử. Vì `class` và `style` đều là thuộc tính, ta có thể dùng `v-bind` để gán giá trị chuỗi động cho chúng, tương tự như các thuộc tính khác. Tuy nhiên, việc tạo ra các giá trị đó bằng cách nối chuỗi thủ công thường rất bất tiện và dễ xảy ra lỗi. Vì vậy, Vue cung cấp các tính năng đặc biệt khi dùng `v-bind` với `class` và `style`. Ngoài chuỗi, các biểu thức còn có thể nhận giá trị là đối tượng hoặc mảng.

## Ràng Buộc HTML Classes {#binding-html-classes}

<div class="options-api">
  <VueSchoolLink href="https://vueschool.io/lessons/dynamic-css-classes-with-vue-3" title="Free Vue.js Dynamic CSS Classes Lesson"/>
</div>

<div class="composition-api">
  <VueSchoolLink href="https://vueschool.io/lessons/vue-fundamentals-capi-dynamic-css-classes-with-vue" title="Free Vue.js Dynamic CSS Classes Lesson"/>
</div>

### Ràng Buộc với Đối Tượng {#binding-to-objects}

Ta có thể truyền một đối tượng vào `:class` (viết tắt của `v-bind:class`) để bật/tắt các class một cách động:

```vue-html
<div :class="{ active: isActive }"></div>
```

Cú pháp trên có nghĩa là sự xuất hiện của class `active` sẽ được xác định bởi [giá trị truthy](https://developer.mozilla.org/en-US/docs/Glossary/Truthy) của thuộc tính dữ liệu `isActive`.

Bạn có thể bật/tắt nhiều class bằng cách thêm nhiều trường vào đối tượng. Ngoài ra, directive `:class` cũng có thể dùng đồng thời với thuộc tính `class` thông thường. Ví dụ với state sau:

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

Và template sau:

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

Khi `isActive` hoặc `hasError` thay đổi, danh sách class sẽ được cập nhật tương ứng. Ví dụ, nếu `hasError` trở thành `true`, danh sách class sẽ là `"static active text-danger"`.

Đối tượng được ràng buộc không nhất thiết phải là inline:

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

Ta cũng có thể ràng buộc với một [thuộc tính computed](./computed) trả về đối tượng. Đây là một pattern phổ biến và rất mạnh mẽ:

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

### Ràng Buộc với Mảng {#binding-to-arrays}

Ta có thể ràng buộc `:class` với một mảng để áp dụng danh sách các class:

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

Nếu muốn bật/tắt một class trong danh sách theo điều kiện, bạn có thể dùng biểu thức ternary:

```vue-html
<div :class="[isActive ? activeClass : '', errorClass]"></div>
```

`errorClass` sẽ luôn được áp dụng, nhưng `activeClass` chỉ được áp dụng khi `isActive` là truthy.

Tuy nhiên, cú pháp này có thể trở nên dài dòng nếu có nhiều class điều kiện. Vì vậy, bạn cũng có thể dùng cú pháp đối tượng bên trong cú pháp mảng:

```vue-html
<div :class="[{ [activeClass]: isActive }, errorClass]"></div>
```

### Dùng với Component {#with-components}

> Phần này giả định bạn đã có kiến thức về [Components](/guide/essentials/component-basics). Bạn có thể bỏ qua và quay lại sau.

Khi bạn dùng thuộc tính `class` trên một component có một phần tử gốc duy nhất, các class đó sẽ được thêm vào phần tử gốc của component và được hợp nhất với các class đã có trên đó.

Ví dụ, nếu có một component tên `MyComponent` với template sau:

```vue-html
<!-- child component template -->
<p class="foo bar">Hi!</p>
```

Sau đó thêm một số class khi sử dụng component:

```vue-html
<!-- when using the component -->
<MyComponent class="baz boo" />
```

HTML được render ra sẽ là:

```vue-html
<p class="foo bar baz boo">Hi!</p>
```

Điều tương tự cũng đúng với ràng buộc class:

```vue-html
<MyComponent :class="{ active: isActive }" />
```

Khi `isActive` là truthy, HTML được render ra sẽ là:

```vue-html
<p class="foo bar active">Hi!</p>
```

Nếu component có nhiều phần tử gốc, bạn cần chỉ định phần tử nào sẽ nhận class đó. Bạn có thể làm điều này bằng thuộc tính component `$attrs`:

```vue-html
<!-- MyComponent template using $attrs -->
<p :class="$attrs.class">Hi!</p>
<span>This is a child component</span>
```

```vue-html
<MyComponent class="baz" />
```

Kết quả render sẽ là:

```html
<p class="baz">Hi!</p>
<span>This is a child component</span>
```

Bạn có thể tìm hiểu thêm về cơ chế kế thừa thuộc tính của component trong phần [Fallthrough Attributes](/guide/components/attrs).

## Ràng Buộc Inline Style {#binding-inline-styles}

### Ràng Buộc với Đối Tượng {#binding-to-objects-1}

`:style` hỗ trợ ràng buộc với các giá trị đối tượng JavaScript — tương ứng với [thuộc tính `style` của HTML element](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/style):

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

Mặc dù khóa dạng camelCase được khuyến nghị, `:style` cũng hỗ trợ khóa CSS dạng kebab-case (tương ứng với cách dùng trong CSS thực tế) — ví dụ:

```vue-html
<div :style="{ 'font-size': fontSize + 'px' }"></div>
```

Thông thường, nên ràng buộc trực tiếp với một đối tượng style để template trở nên gọn gàng hơn:

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

Tương tự, ràng buộc style theo đối tượng thường được dùng kết hợp với các thuộc tính computed trả về đối tượng.

Directive `:style` cũng có thể dùng đồng thời với thuộc tính style thông thường, tương tự như `:class`.

Template:

```vue-html
<h1 style="color: red" :style="'font-size: 1em'">hello</h1>
```

Kết quả render sẽ là:

```vue-html
<h1 style="color: red; font-size: 1em;">hello</h1>
```

### Ràng Buộc với Mảng {#binding-to-arrays-1}

Ta có thể ràng buộc `:style` với một mảng nhiều đối tượng style. Các đối tượng này sẽ được hợp nhất và áp dụng lên cùng một phần tử:

```vue-html
<div :style="[baseStyles, overridingStyles]"></div>
```

### Tự Động Thêm Tiền Tố {#auto-prefixing}

Khi bạn dùng một thuộc tính CSS yêu cầu [tiền tố của nhà cung cấp](https://developer.mozilla.org/en-US/docs/Glossary/Vendor_Prefix) trong `:style`, Vue sẽ tự động thêm tiền tố phù hợp. Vue thực hiện điều này bằng cách kiểm tra tại runtime xem trình duyệt hiện tại hỗ trợ những thuộc tính style nào. Nếu trình duyệt không hỗ trợ một thuộc tính cụ thể, các biến thể có tiền tố khác nhau sẽ được thử lần lượt để tìm ra biến thể được hỗ trợ.

### Nhiều Giá Trị {#multiple-values}

Bạn có thể cung cấp một mảng nhiều giá trị (có tiền tố) cho một thuộc tính style, ví dụ:

```vue-html
<div :style="{ display: ['-webkit-box', '-ms-flexbox', 'flex'] }"></div>
```

Kết quả chỉ render giá trị cuối cùng trong mảng mà trình duyệt hỗ trợ. Trong ví dụ này, với các trình duyệt hỗ trợ phiên bản flexbox không có tiền tố, kết quả sẽ là `display: flex`.
