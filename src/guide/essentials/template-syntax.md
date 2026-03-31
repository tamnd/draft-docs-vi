# Cú pháp Template {#template-syntax}

<ScrimbaLink href="https://scrimba.com/links/vue-template-syntax" title="Bài học miễn phí về Template Syntax trong Vue.js" type="scrimba">
  Xem video bài học tương tác trên Scrimba
</ScrimbaLink>

Vue sử dụng cú pháp template dựa trên HTML, cho phép bạn bind một cách khai báo (declarative) DOM được render với dữ liệu của instance component bên dưới. Tất cả template của Vue đều là HTML hợp lệ về mặt cú pháp, có thể được parse bởi các trình duyệt tuân thủ chuẩn và các HTML parser.

Ở bên trong, Vue compile template thành mã JavaScript được tối ưu hóa cao. Kết hợp với hệ thống reactivity, Vue có thể thông minh xác định số lượng component tối thiểu cần re-render và áp dụng lượng thao tác DOM tối thiểu khi trạng thái ứng dụng thay đổi.

Nếu bạn quen với khái niệm Virtual DOM và thích sức mạnh trực tiếp của JavaScript, bạn cũng có thể [viết trực tiếp render function](/guide/extras/render-function) thay vì template, với hỗ trợ JSX tùy chọn. Tuy nhiên, cần lưu ý rằng chúng không được hưởng mức tối ưu hóa compile-time như template.

## Nội suy văn bản {#text-interpolation}

Dạng binding dữ liệu cơ bản nhất là nội suy văn bản sử dụng cú pháp "Mustache" (dấu ngoặc nhọn kép):

```vue-html
<span>Message: {{ msg }}</span>
```

Thẻ mustache sẽ được thay thế bằng giá trị của property `msg` từ instance component tương ứng. Nó cũng sẽ được cập nhật mỗi khi property `msg` thay đổi.

## HTML thô {#raw-html}

Mustache sẽ diễn giải dữ liệu như văn bản thuần, không phải HTML. Để render HTML thật, bạn cần dùng directive `v-html`:

```vue-html
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

```vue-html
<div v-bind:id="dynamicId"></div>
```

Directive `v-bind` yêu cầu Vue giữ cho attribute `id` của phần tử luôn đồng bộ với property `dynamicId` của component. Nếu giá trị bind là `null` hoặc `undefined`, attribute sẽ bị xóa khỏi phần tử được render.

### Viết tắt {#shorthand}

Vì `v-bind` được dùng rất thường xuyên, nó có cú pháp viết tắt riêng:

```vue-html
<div :id="dynamicId"></div>
```

Các attribute bắt đầu bằng `:` có thể trông khác HTML thông thường, nhưng thực tế đây là ký tự hợp lệ cho tên attribute và tất cả trình duyệt mà Vue hỗ trợ đều parse đúng. Ngoài ra, chúng sẽ không xuất hiện trong markup cuối cùng. Cú pháp viết tắt là tùy chọn, nhưng bạn sẽ thấy nó tiện khi dùng nhiều.

> Trong phần còn lại của hướng dẫn, chúng tôi sẽ dùng cú pháp viết tắt trong các ví dụ, vì đây là cách dùng phổ biến nhất với các developer Vue.

### Viết tắt cùng tên {#same-name-shorthand}

- Chỉ hỗ trợ từ phiên bản 3.4 trở lên.

Nếu tên attribute giống với tên biến JavaScript được bind, bạn có thể rút gọn hơn nữa bằng cách bỏ giá trị:

```vue-html
<!-- tương đương :id="id" -->
<div :id></div>

<!-- cách này cũng hoạt động -->
<div v-bind:id></div>
```

Cách này tương tự cú pháp shorthand khi khai báo object trong JavaScript. Lưu ý đây là tính năng chỉ có từ Vue 3.4 trở lên.

### Attribute boolean {#boolean-attributes}

Attribute boolean là các attribute biểu diễn true hoặc false thông qua việc có tồn tại hay không trên phần tử. Ví dụ `disabled` là một attribute phổ biến.

`v-bind` hoạt động hơi khác trong trường hợp này:

```vue-html
<button :disabled="isButtonDisabled">Button</button>
```

Attribute `disabled` sẽ được thêm vào nếu `isButtonDisabled` có giá trị truthy. Nó cũng sẽ được thêm nếu giá trị là chuỗi rỗng, để giữ nhất quán với `<button disabled="">`. Với các giá trị falsy khác, attribute sẽ bị bỏ.

### Bind nhiều attribute động {#dynamically-binding-multiple-attributes}

Nếu bạn có một object JavaScript chứa nhiều attribute:

<div class="composition-api">

```js
const objectOfAttrs = {
  id: 'container',
  class: 'wrapper',
  style: 'background-color:green'
}
```

</div>
<div class="options-api">

```js
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

```vue-html
<div v-bind="objectOfAttrs"></div>
```

## Sử dụng biểu thức JavaScript {#using-javascript-expressions}

Cho đến nay chúng ta chỉ bind các key đơn giản. Nhưng Vue hỗ trợ đầy đủ biểu thức JavaScript trong mọi binding:

```vue-html
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

```vue-html
{{ var a = 1 }}

{{ if (ok) { return message } }}
```

### Gọi function {#calling-functions}

Bạn có thể gọi method của component trong biểu thức:

```vue-html
<time :title="toTitleDate(date)" :datetime="date">
  {{ formatDate(date) }}
</time>
```

:::tip
Các function được gọi trong biểu thức binding sẽ được gọi mỗi khi component cập nhật, vì vậy chúng **không** nên có side effects, ví dụ như thay đổi dữ liệu hoặc kích hoạt các thao tác bất đồng bộ.
:::

### Truy cập global bị hạn chế {#restricted-globals-access}

Biểu thức template bị sandbox và chỉ truy cập được một [danh sách global hạn chế](https://github.com/vuejs/core/blob/main/packages/shared/src/globalsAllowList.ts#L3). Danh sách này bao gồm các global built-in thường dùng như `Math` và `Date`.

Các global không có trong danh sách, ví dụ các property do người dùng gắn vào `window`, sẽ không truy cập được trong biểu thức template. Tuy nhiên, bạn có thể định nghĩa thêm global cho tất cả biểu thức Vue bằng cách thêm vào [`app.config.globalProperties`](/api/application#app-config-globalproperties).

## Directive {#directives}

Directive là attribute đặc biệt có tiền tố `v-`. Vue cung cấp nhiều [directive built-in](/api/built-in-directives) như `v-html` và `v-bind` đã giới thiệu ở trên.

Giá trị của directive là một biểu thức JavaScript đơn (ngoại trừ `v-for`, `v-on` và `v-slot`, sẽ được thảo luận trong các phần tương ứng sau này). Directive có nhiệm vụ phản ứng lại (reactively) để áp dụng các cập nhật lên DOM khi giá trị của biểu thức thay đổi. Lấy [`v-if`](/api/built-in-directives#v-if) làm ví dụ:

```vue-html
<p v-if="seen">Now you see me</p>
```

Ở đây, directive `v-if` sẽ xóa hoặc chèn phần tử `<p>` dựa trên tính truthy của giá trị biểu thức `seen`.

### Arguments {#arguments}

Một số directive có thể nhận "argument", được biểu thị bằng dấu hai chấm sau tên directive. Ví dụ, directive `v-bind` dùng để cập nhật reactive một attribute HTML:

```vue-html
<a v-bind:href="url"> ... </a>

<!-- viết tắt -->
<a :href="url"> ... </a>
```

Ở đây, `href` là argument, nói cho directive `v-bind` biết cần bind attribute `href` của phần tử với giá trị của biểu thức `url`. Trong cú pháp viết tắt, mọi thứ trước argument (tức là `v-bind:`) được rút gọn thành một ký tự duy nhất, `:`.

Ví dụ khác là directive `v-on`, lắng nghe các sự kiện DOM:

```vue-html
<a v-on:click="doSomething"> ... </a>

<!-- viết tắt -->
<a @click="doSomething"> ... </a>
```

Ở đây, argument là tên sự kiện cần lắng nghe: `click`. `v-on` có cú pháp viết tắt tương ứng là ký tự `@`. Chúng ta sẽ nói về xử lý sự kiện chi tiết hơn.

### Argument động {#dynamic-arguments}

Cũng có thể dùng biểu thức JavaScript trong argument của directive bằng cách bao trong dấu ngoặc vuông:

```vue-html
<!--
Lưu ý có một số ràng buộc với biểu thức argument,
như được giải thích trong phần "Ràng buộc giá trị argument động" và "Ràng buộc cú pháp argument động" bên dưới.
-->
<a v-bind:[attributeName]="url"> ... </a>

<!-- viết tắt -->
<a :[attributeName]="url"> ... </a>
```

Ở đây, `attributeName` sẽ được evaluate động như biểu thức JavaScript, và giá trị được tính sẽ dùng làm giá trị cuối cùng cho argument. Ví dụ, nếu instance component có data property `attributeName` với giá trị là `"href"`, thì binding này tương đương với `v-bind:href`.

Tương tự, bạn có thể dùng argument động để bind handler với tên sự kiện động:

```vue-html
<a v-on:[eventName]="doSomething"> ... </a>

<!-- viết tắt -->
<a @[eventName]="doSomething"> ... </a>
```

Trong ví dụ này, khi giá trị của `eventName` là `"focus"`, `v-on:[eventName]` sẽ tương đương với `v-on:focus`.

### Ràng buộc giá trị argument động {#dynamic-argument-value-constraints}

Argument phải là string hoặc `null`. Giá trị khác sẽ warning.

### Ràng buộc cú pháp argument động {#dynamic-argument-syntax-constraints}

Biểu thức argument động có một số ràng buộc cú pháp vì một số ký tự như dấu cách và dấu ngoặc kép không hợp lệ trong tên attribute HTML. Ví dụ, cú pháp sau không hợp lệ:

```vue-html
<!-- Đoạn này sẽ gây cảnh báo từ compiler. -->
<a :['foo' + bar]="value"> ... </a>
```

Nếu cần truyền argument động phức tạp, nên dùng [computed property](./computed), sẽ được đề cập sớm.

Khi dùng in-DOM template (template viết trực tiếp trong file HTML), bạn cũng nên tránh đặt tên key bằng chữ hoa, vì trình duyệt sẽ ép tên attribute thành chữ thường:

```vue-html
<a :[someAttr]="value"> ... </a>
```

Đoạn trên sẽ bị chuyển thành `:[someattr]` trong in-DOM template. Nếu component của bạn có property `someAttr` thay vì `someattr`, code sẽ không hoạt động. Template trong Single-File Component **không** bị ảnh hưởng bởi ràng buộc này.

### Modifiers {#modifiers}

Modifier là hậu tố dạng `.`, dùng để thay đổi hành vi directive:

```vue-html
<form @submit.prevent="onSubmit">...</form>
```

`.prevent` gọi `event.preventDefault()`.

Các modifier khác sẽ được trình bày ở phần sau.

Cuối cùng, đây là sơ đồ cú pháp directive:

![directive syntax graph](./images/directive.png)

