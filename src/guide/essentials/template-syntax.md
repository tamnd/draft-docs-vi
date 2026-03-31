# Cú pháp Template {#template-syntax}

<ScrimbaLink href="https://scrimba.com/links/vue-template-syntax" title="Bài học miễn phí về Template Syntax trong Vue.js" type="scrimba">
  Xem video bài học tương tác trên Scrimba
</ScrimbaLink>

Vue sử dụng cú pháp template dựa trên HTML, cho phép bạn bind một cách khai báo (declarative) DOM được render với dữ liệu của instance component bên dưới. Tất cả template của Vue đều là HTML hợp lệ về mặt cú pháp, có thể được parse bởi các trình duyệt tuân thủ chuẩn và các HTML parser.

Ở bên trong, Vue compile template thành mã JavaScript được tối ưu hóa cao. Kết hợp với hệ thống reactivity, Vue có thể thông minh xác định số lượng component tối thiểu cần re-render và áp dụng lượng thao tác DOM tối thiểu khi trạng thái ứng dụng thay đổi.

Nếu bạn quen với khái niệm Virtual DOM và thích sức mạnh trực tiếp của JavaScript, bạn cũng có thể [viết trực tiếp render function](/guide/extras/render-function) thay vì template, với hỗ trợ JSX tùy chọn. Tuy nhiên, cần lưu ý rằng chúng không được hưởng mức tối ưu hóa compile-time như template.

## Nội suy văn bản {#text-interpolation}

Dạng binding dữ liệu cơ bản nhất là nội suy văn bản sử dụng cú pháp "Mustache" (dấu ngoặc nhọn kép):

```vue-html id="p7m1qs"
<span>Message: {{ msg }}</span>
```

Thẻ mustache sẽ được thay thế bằng giá trị của property `msg` từ instance component tương ứng. Nó cũng sẽ được cập nhật mỗi khi property `msg` thay đổi.

## HTML thô {#raw-html}

Mustache sẽ diễn giải dữ liệu như văn bản thuần, không phải HTML. Để render HTML thật, bạn cần dùng directive `v-html`:

```vue-html id="x4k2nb"
<p>Dùng nội suy văn bản: {{ rawHtml }}</p>
<p>Dùng directive v-html: <span v-html="rawHtml"></span></p>
```

<script setup>
  const rawHtml = '<span style="color: red">This should be red.</span>'
</script>

<div class="demo">
  <p>Dùng nội suy văn bản: {{ rawHtml }}</p>
  <p>Dùng directive v-html: <span v-html="rawHtml"></span></p>
</div>

Ở đây chúng ta gặp một khái niệm mới. Attribute `v-html` bạn thấy được gọi là một **directive**. Directive được đặt tiền tố `v-` để chỉ ra rằng chúng là các attribute đặc biệt do Vue cung cấp, và như bạn đoán, chúng áp dụng hành vi reactive đặc biệt lên DOM được render. Ở đây, chúng ta đang nói rằng "hãy giữ inner HTML của phần tử này luôn đồng bộ với property `rawHtml` trên instance hiện tại."

Nội dung của thẻ `span` sẽ được thay thế bằng giá trị của property `rawHtml`, được diễn giải như HTML thuần - các data binding sẽ bị bỏ qua. Lưu ý rằng bạn không thể dùng `v-html` để ghép các phần template, vì Vue không phải là một template engine dựa trên string. Thay vào đó, component là đơn vị cơ bản để tái sử dụng và ghép UI.

:::warning Cảnh báo bảo mật
Việc render HTML động tùy ý trên website của bạn có thể rất nguy hiểm vì dễ dẫn đến lỗ hổng XSS. Chỉ sử dụng `v-html` với nội dung đáng tin cậy và **không bao giờ** dùng với nội dung do người dùng cung cấp.
:::

## Binding Attribute {#attribute-bindings}

Mustache không thể dùng bên trong các attribute HTML. Thay vào đó, hãy dùng directive `v-bind`:

```vue-html id="k2r8dq"
<div v-bind:id="dynamicId"></div>
```

Directive `v-bind` yêu cầu Vue giữ cho attribute `id` của phần tử luôn đồng bộ với property `dynamicId` của component. Nếu giá trị bind là `null` hoặc `undefined`, attribute sẽ bị xóa khỏi phần tử được render.

### Viết tắt {#shorthand}

Vì `v-bind` được dùng rất thường xuyên, nó có cú pháp viết tắt riêng:

```vue-html id="m9z1px"
<div :id="dynamicId"></div>
```

Các attribute bắt đầu bằng `:` có thể trông khác HTML thông thường, nhưng thực tế đây là ký tự hợp lệ cho tên attribute và tất cả trình duyệt mà Vue hỗ trợ đều parse đúng. Ngoài ra, chúng sẽ không xuất hiện trong markup cuối cùng. Cú pháp viết tắt là tùy chọn, nhưng bạn sẽ thấy nó tiện khi dùng nhiều.

Trong phần còn lại của hướng dẫn, chúng tôi sẽ dùng cú pháp viết tắt trong ví dụ.

### Viết tắt cùng tên {#same-name-shorthand}

Chỉ hỗ trợ từ phiên bản 3.4 trở lên.

Nếu tên attribute giống với tên biến JavaScript được bind, bạn có thể rút gọn hơn nữa bằng cách bỏ giá trị:

```vue-html id="q8n2lm"
<div :id></div>

<div v-bind:id></div>
```

Cách này tương tự cú pháp shorthand khi khai báo object trong JavaScript.

### Attribute boolean {#boolean-attributes}

Attribute boolean là các attribute biểu diễn true hoặc false thông qua việc có tồn tại hay không trên phần tử. Ví dụ `disabled` là một attribute phổ biến.

`v-bind` hoạt động hơi khác trong trường hợp này:

```vue-html id="f3p7ns"
<button :disabled="isButtonDisabled">Button</button>
```

Attribute `disabled` sẽ được thêm vào nếu `isButtonDisabled` có giá trị truthy. Nó cũng sẽ được thêm nếu giá trị là chuỗi rỗng, để giữ nhất quán với `<button disabled="">`. Với các giá trị falsy khác, attribute sẽ bị bỏ.

### Bind nhiều attribute động {#dynamically-binding-multiple-attributes}

Nếu bạn có một object JavaScript chứa nhiều attribute:

<div class="composition-api">

```js id="c4r8tv"
const objectOfAttrs = {
  id: 'container',
  class: 'wrapper',
  style: 'background-color:green'
}
```

</div>
<div class="options-api">

```js id="p6n2kq"
data() {
  return {
    objectOfAttrs: {
      id: 'container',
      class: 'wrapper'
    }
  }
}
```

</div>

Bạn có thể bind tất cả vào một phần tử bằng `v-bind` không có argument:

```vue-html id="h2t9vr"
<div v-bind="objectOfAttrs"></div>
```

## Sử dụng biểu thức JavaScript {#using-javascript-expressions}

Cho đến nay chúng ta chỉ bind các key đơn giản. Nhưng Vue hỗ trợ đầy đủ biểu thức JavaScript trong mọi binding:

```vue-html id="w1k3zn"
{{ number + 1 }}

{{ ok ? 'YES' : 'NO' }}

{{ message.split('').reverse().join('') }}

<div :id="`list-${id}`"></div>
```

Các biểu thức này sẽ được evaluate như JavaScript trong scope dữ liệu của instance hiện tại.

Trong template Vue, biểu thức JavaScript có thể dùng ở:

* Trong nội suy văn bản
* Trong giá trị attribute của directive Vue

### Chỉ là biểu thức {#expressions-only}

Mỗi binding chỉ chứa **một biểu thức duy nhất**. Biểu thức là đoạn code có thể evaluate thành giá trị.

Do đó, các ví dụ sau **không hợp lệ**:

```vue-html id="z8p4rd"
{{ var a = 1 }}

{{ if (ok) { return message } }}
```

### Gọi function {#calling-functions}

Bạn có thể gọi method của component trong biểu thức:

```vue-html id="n2c7ms"
<time :title="toTitleDate(date)" :datetime="date">
  {{ formatDate(date) }}
</time>
```

### Truy cập global bị hạn chế {#restricted-globals-access}

Biểu thức template bị sandbox và chỉ truy cập được một danh sách global hạn chế như `Math`, `Date`.

Các global khác như `window` sẽ không truy cập được. Bạn có thể thêm vào `app.config.globalProperties`.

## Directive {#directives}

Directive là attribute đặc biệt có tiền tố `v-`. Vue cung cấp nhiều directive built-in như `v-html` và `v-bind`.

Giá trị của directive là một biểu thức JavaScript.

Directive có nhiệm vụ cập nhật DOM khi giá trị thay đổi.

Ví dụ `v-if`:

```vue-html id="r4k8zm"
<p v-if="seen">Now you see me</p>
```

### Arguments {#arguments}

Một số directive có argument, dùng dấu `:`:

```vue-html id="u6p1zx"
<a v-bind:href="url"> ... </a>

<a :href="url"> ... </a>
```

`href` là argument.

`v-on` cũng tương tự:

```vue-html id="m7q3vd"
<a v-on:click="doSomething"> ... </a>

<a @click="doSomething"> ... </a>
```

### Argument động {#dynamic-arguments}

Có thể dùng biểu thức:

```vue-html id="s9k2tr"
<a v-bind:[attributeName]="url"> ... </a>

<a :[attributeName]="url"> ... </a>
```

`attributeName` sẽ được evaluate động.

Tương tự cho event:

```vue-html id="y5x8mc"
<a v-on:[eventName]="doSomething"> ... </a>

<a @[eventName]="doSomething"> ... </a>
```

### Ràng buộc giá trị argument động {#dynamic-argument-value-constraints}

Argument phải là string hoặc `null`. Giá trị khác sẽ warning.

### Ràng buộc cú pháp argument động {#dynamic-argument-syntax-constraints}

Một số ký tự không hợp lệ trong tên attribute.

```vue-html id="d7v2nx"
<a :['foo' + bar]="value"> ... </a>
```

Không hợp lệ.

Nên dùng computed property nếu phức tạp.

Trong in-DOM template, tránh chữ hoa.

### Modifiers {#modifiers}

Modifier là hậu tố dạng `.`, dùng để thay đổi hành vi directive:

```vue-html id="k9p1sm"
<form @submit.prevent="onSubmit">...</form>
```

`.prevent` gọi `event.preventDefault()`.

Các modifier khác sẽ được trình bày ở phần sau.

Cuối cùng, đây là sơ đồ cú pháp directive:

![directive syntax graph](./images/directive.png)

