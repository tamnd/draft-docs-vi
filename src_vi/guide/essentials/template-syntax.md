# Cú pháp template {#template-syntax}

<ScrimbaLink href="https://scrimba.com/links/vue-template-syntax" title="Free Vue.js Template Syntax Lesson" type="scrimba">
  Xem bài học video tương tác trên Scrimba
</ScrimbaLink>

Vue sử dụng cú pháp template dựa trên HTML, cho phép bạn khai báo ràng buộc giữa DOM được render và dữ liệu của component instance. Mọi template Vue đều là HTML hợp lệ về mặt cú pháp, nên có thể được phân tích bởi các trình duyệt và HTML parser tuân chuẩn.

Ở phía sau, Vue biên dịch template thành mã JavaScript được tối ưu cao. Kết hợp với hệ thống tính phản ứng, Vue có thể xác định thông minh số lượng component tối thiểu cần render lại và chỉ thực hiện lượng thao tác DOM ít nhất khi state ứng dụng thay đổi.

Nếu bạn đã quen với khái niệm Virtual DOM và thích sự linh hoạt thuần túy của JavaScript, bạn cũng có thể [viết render function trực tiếp](/guide/extras/render-function) thay cho template, kèm hỗ trợ JSX nếu muốn. Tuy vậy, hãy lưu ý rằng chúng không có cùng mức tối ưu ở thời điểm biên dịch như template.

## Nội suy văn bản {#text-interpolation}

Dạng ràng buộc dữ liệu cơ bản nhất là nội suy văn bản bằng cú pháp "Mustache" với hai dấu ngoặc nhọn:

```vue-html
<span>Thông điệp: {{ msg }}</span>
```

Thẻ mustache sẽ được thay bằng giá trị của thuộc tính `msg` [trong component instance tương ứng](/guide/essentials/reactivity-fundamentals#declaring-reactive-state). Nó cũng sẽ tự cập nhật mỗi khi thuộc tính `msg` thay đổi.

## HTML thô {#raw-html}

Hai dấu mustache sẽ coi dữ liệu là văn bản thuần, không phải HTML. Nếu muốn xuất ra HTML thật, bạn cần dùng [`v-html` directive](/api/built-in-directives#v-html):

```vue-html
<p>Dùng nội suy văn bản: {{ rawHtml }}</p>
<p>Dùng directive v-html: <span v-html="rawHtml"></span></p>
```

<script setup>
  const rawHtml = '<span style="color: red">Dòng này phải có màu đỏ.</span>'
</script>

<div class="demo">
  <p>Dùng nội suy văn bản: {{ rawHtml }}</p>
  <p>Dùng directive v-html: <span v-html="rawHtml"></span></p>
</div>

Ở đây chúng ta gặp một khái niệm mới. Thuộc tính `v-html` bạn thấy ở đây được gọi là **directive**. Directive đều bắt đầu bằng tiền tố `v-` để cho biết đây là những thuộc tính đặc biệt do Vue cung cấp. Như bạn có thể đoán, chúng áp dụng hành vi phản ứng đặc biệt lên DOM được render. Ở đây, ý nghĩa của nó gần như là: "hãy luôn giữ `innerHTML` của phần tử này đồng bộ với thuộc tính `rawHtml` trên instance hiện tại."

Nội dung của thẻ `span` sẽ được thay bằng giá trị của thuộc tính `rawHtml`, và giá trị này sẽ được hiểu như HTML thuần, các ràng buộc dữ liệu bên trong sẽ bị bỏ qua. Lưu ý là bạn không thể dùng `v-html` để ghép các mẩu template lại với nhau, vì Vue không phải là một template engine dựa trên chuỗi. Thay vào đó, component mới là đơn vị cơ bản để tái sử dụng và tổ chức UI.

:::warning Cảnh báo bảo mật
Việc render HTML tùy ý một cách động trên website có thể rất nguy hiểm vì nó dễ dẫn đến [lỗ hổng XSS](https://en.wikipedia.org/wiki/Cross-site_scripting). Chỉ dùng `v-html` với nội dung đáng tin cậy và **tuyệt đối không** dùng với nội dung do người dùng cung cấp.
:::

## Ràng buộc thuộc tính {#attribute-bindings}

Mustache không thể dùng bên trong thuộc tính HTML. Thay vào đó, hãy dùng [`v-bind` directive](/api/built-in-directives#v-bind):

```vue-html
<div v-bind:id="dynamicId"></div>
```

Directive `v-bind` bảo Vue giữ cho thuộc tính `id` của phần tử luôn đồng bộ với thuộc tính `dynamicId` của component. Nếu giá trị được bind là `null` hoặc `undefined`, thuộc tính đó sẽ bị xóa khỏi phần tử đã render.

### Cú pháp rút gọn {#shorthand}

Vì `v-bind` được dùng rất thường xuyên nên nó có một cú pháp rút gọn riêng:

```vue-html
<div :id="dynamicId"></div>
```

Những thuộc tính bắt đầu bằng `:` có thể trông hơi lạ so với HTML thông thường, nhưng thật ra đây là ký tự hợp lệ trong tên thuộc tính, và mọi trình duyệt mà Vue hỗ trợ đều có thể phân tích đúng. Ngoài ra, chúng cũng sẽ không xuất hiện trong phần markup cuối cùng sau khi render. Cú pháp rút gọn là tùy chọn, nhưng rất có thể bạn sẽ thấy nó tiện khi dùng Vue nhiều hơn.

> Trong phần còn lại của hướng dẫn, chúng tôi sẽ dùng cú pháp rút gọn trong các ví dụ mã nguồn, vì đó là cách dùng phổ biến nhất của lập trình viên Vue.

### Cú pháp rút gọn khi trùng tên {#same-name-shorthand}

- Chỉ hỗ trợ từ 3.4+

Nếu thuộc tính có cùng tên với biến JavaScript đang được bind, cú pháp có thể được rút gọn thêm bằng cách bỏ hẳn phần giá trị của thuộc tính:

```vue-html
<!-- giống với :id="id" -->
<div :id></div>

<!-- cách này cũng hợp lệ -->
<div v-bind:id></div>
```

Điều này tương tự với cú pháp rút gọn thuộc tính khi khai báo object trong JavaScript. Lưu ý rằng đây là tính năng chỉ có từ Vue 3.4 trở lên.

### Thuộc tính boolean {#boolean-attributes}

[Boolean attributes](https://html.spec.whatwg.org/multipage/common-microsyntaxes.html#boolean-attributes) là những thuộc tính thể hiện giá trị đúng / sai thông qua việc chúng có xuất hiện trên phần tử hay không. Ví dụ, [`disabled`](https://developer.mozilla.org/en-US/docs/Web/HTML/Attributes/disabled) là một trong những thuộc tính boolean được dùng nhiều nhất.

Trong trường hợp này, `v-bind` hoạt động hơi khác một chút:

```vue-html
<button :disabled="isButtonDisabled">Nút bấm</button>
```

Thuộc tính `disabled` sẽ được thêm vào nếu `isButtonDisabled` có [giá trị truthy](https://developer.mozilla.org/en-US/docs/Glossary/Truthy). Nó cũng sẽ được thêm vào nếu giá trị là chuỗi rỗng, để giữ cho hành vi nhất quán với `<button disabled="">`. Với các [giá trị falsy](https://developer.mozilla.org/en-US/docs/Glossary/Falsy) khác, thuộc tính này sẽ bị lược bỏ.

### Ràng buộc nhiều thuộc tính một cách động {#dynamically-binding-multiple-attributes}

Nếu bạn có một object JavaScript đại diện cho nhiều thuộc tính như thế này:

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

Bạn có thể bind toàn bộ chúng vào một phần tử bằng cách dùng `v-bind` mà không truyền đối số:

```vue-html
<div v-bind="objectOfAttrs"></div>
```

## Dùng biểu thức JavaScript {#using-javascript-expressions}

Cho tới đây, chúng ta mới chỉ bind với những khóa thuộc tính đơn giản trong template. Nhưng thật ra Vue hỗ trợ đầy đủ sức mạnh của biểu thức JavaScript trong mọi chỗ ràng buộc dữ liệu:

```vue-html
{{ number + 1 }}

{{ ok ? 'CÓ' : 'KHÔNG' }}

{{ message.split('').reverse().join('') }}

<div :id="`list-${id}`"></div>
```

Các biểu thức này sẽ được đánh giá như JavaScript trong phạm vi dữ liệu của component instance hiện tại.

Trong template Vue, biểu thức JavaScript có thể được dùng ở những vị trí sau:

- Bên trong nội suy văn bản bằng mustache
- Trong giá trị thuộc tính của bất kỳ directive Vue nào, tức những thuộc tính đặc biệt bắt đầu bằng `v-`

### Chỉ được dùng biểu thức {#expressions-only}

Mỗi chỗ bind chỉ có thể chứa **một biểu thức duy nhất**. Biểu thức là một đoạn mã có thể được tính ra thành một giá trị. Cách kiểm tra đơn giản là xem nó có thể đặt sau `return` hay không.

Vì vậy, những ví dụ sau sẽ **KHÔNG** hoạt động:

```vue-html
<!-- đây là câu lệnh, không phải biểu thức: -->
{{ var a = 1 }}

<!-- điều khiển luồng cũng không dùng được, hãy dùng toán tử ba ngôi -->
{{ if (ok) { return message } }}
```

### Gọi hàm {#calling-functions}

Bạn hoàn toàn có thể gọi một method được component expose ra bên trong biểu thức bind:

```vue-html
<time :title="toTitleDate(date)" :datetime="date">
  {{ formatDate(date) }}
</time>
```

:::tip
Những hàm được gọi bên trong biểu thức bind sẽ được gọi lại mỗi khi component cập nhật. Vì vậy, chúng **không nên** có side effect, ví dụ như thay đổi dữ liệu hoặc kích hoạt thao tác bất đồng bộ.
:::

### Quyền truy cập global bị giới hạn {#restricted-globals-access}

Biểu thức trong template được chạy trong môi trường sandbox và chỉ có quyền truy cập vào [một danh sách global bị giới hạn](https://github.com/vuejs/core/blob/main/packages/shared/src/globalsAllowList.ts#L3). Danh sách này cung cấp một số global built-in thường dùng như `Math` và `Date`.

Những global không được liệt kê rõ trong danh sách, ví dụ như các thuộc tính do người dùng gắn lên `window`, sẽ không thể truy cập được trong biểu thức template. Tuy nhiên, bạn vẫn có thể tự khai báo thêm global cho toàn bộ biểu thức Vue bằng cách thêm chúng vào [`app.config.globalProperties`](/api/application#app-config-globalproperties).

## Directives {#directives}

Directive là những thuộc tính đặc biệt có tiền tố `v-`. Vue cung cấp một số [built-in directives](/api/built-in-directives), trong đó có `v-html` và `v-bind` mà chúng ta vừa gặp ở trên.

Giá trị của thuộc tính directive được kỳ vọng là một biểu thức JavaScript duy nhất, ngoại trừ `v-for`, `v-on`, và `v-slot`, những directive này sẽ được nói riêng ở các phần sau. Nhiệm vụ của directive là áp dụng các cập nhật lên DOM theo cơ chế phản ứng khi giá trị của biểu thức thay đổi. Lấy [`v-if`](/api/built-in-directives#v-if) làm ví dụ:

```vue-html
<p v-if="seen">Bây giờ bạn thấy tôi</p>
```

Ở đây, directive `v-if` sẽ xóa hoặc chèn phần tử `<p>` tùy theo tính truthy của giá trị biểu thức `seen`.

### Đối số {#arguments}

Một số directive có thể nhận một "đối số", được ký hiệu bằng dấu hai chấm đặt sau tên directive. Ví dụ, directive `v-bind` được dùng để cập nhật một thuộc tính HTML theo cơ chế phản ứng:

```vue-html
<a v-bind:href="url"> ... </a>

<!-- cú pháp rút gọn -->
<a :href="url"> ... </a>
```

Ở đây, `href` là đối số, nó cho directive `v-bind` biết rằng cần bind thuộc tính `href` của phần tử với giá trị của biểu thức `url`. Trong cú pháp rút gọn, mọi thứ đứng trước đối số, tức `v-bind:`, được thu gọn thành một ký tự duy nhất là `:`.

Một ví dụ khác là directive `v-on`, dùng để lắng nghe sự kiện DOM:

```vue-html
<a v-on:click="doSomething"> ... </a>

<!-- cú pháp rút gọn -->
<a @click="doSomething"> ... </a>
```

Ở đây, đối số là tên của sự kiện cần lắng nghe: `click`. `v-on` cũng có cú pháp rút gọn tương ứng là ký tự `@`. Chúng ta sẽ còn nói kỹ hơn về xử lý sự kiện sau.

### Đối số động {#dynamic-arguments}

Bạn cũng có thể dùng một biểu thức JavaScript bên trong đối số của directive bằng cách đặt nó trong dấu ngoặc vuông:

```vue-html
<!--
Lưu ý là biểu thức dùng trong đối số có một số ràng buộc,
như sẽ được giải thích ở các phần "Ràng buộc về giá trị đối số động" và "Ràng buộc về cú pháp đối số động" bên dưới.
-->
<a v-bind:[attributeName]="url"> ... </a>

<!-- cú pháp rút gọn -->
<a :[attributeName]="url"> ... </a>
```

Ở đây, `attributeName` sẽ được đánh giá động như một biểu thức JavaScript, và giá trị sau khi đánh giá sẽ được dùng làm giá trị cuối cùng của đối số. Ví dụ, nếu component instance của bạn có một thuộc tính dữ liệu tên là `attributeName` với giá trị `"href"`, thì cách bind này sẽ tương đương với `v-bind:href`.

Tương tự, bạn cũng có thể dùng đối số động để bind một handler vào tên sự kiện động:

```vue-html
<a v-on:[eventName]="doSomething"> ... </a>

<!-- cú pháp rút gọn -->
<a @[eventName]="doSomething"> ... </a>
```

Trong ví dụ này, khi giá trị của `eventName` là `"focus"`, thì `v-on:[eventName]` sẽ tương đương với `v-on:focus`.

#### Ràng buộc về giá trị đối số động {#dynamic-argument-value-constraints}

Đối số động được kỳ vọng sẽ đánh giá ra một chuỗi, ngoại trừ `null`. Giá trị đặc biệt `null` có thể được dùng để chủ động xóa binding. Bất kỳ giá trị nào khác không phải chuỗi đều sẽ gây ra cảnh báo.

#### Ràng buộc về cú pháp đối số động {#dynamic-argument-syntax-constraints}

Biểu thức đối số động có một số ràng buộc về cú pháp vì có những ký tự, như khoảng trắng và dấu nháy, không hợp lệ trong tên thuộc tính HTML. Ví dụ sau là không hợp lệ:

```vue-html
<!-- Cách này sẽ gây ra cảnh báo từ compiler. -->
<a :['foo' + bar]="value"> ... </a>
```

Nếu bạn cần truyền vào một đối số động phức tạp, tốt hơn hết là dùng [computed property](./computed), chủ đề mà chúng ta sẽ gặp ngay sau đây.

Khi dùng in-DOM template, tức template được viết trực tiếp trong một tệp HTML, bạn cũng nên tránh đặt tên key có ký tự in hoa, vì trình duyệt sẽ tự chuyển tên thuộc tính về chữ thường:

```vue-html
<a :[someAttr]="value"> ... </a>
```

Cách viết trên sẽ bị chuyển thành `:[someattr]` trong in-DOM template. Nếu component của bạn có thuộc tính `someAttr` thay vì `someattr`, mã của bạn sẽ không chạy đúng. Template bên trong Single-File Components thì **không** bị ràng buộc này.

### Modifier {#modifiers}

Modifier là phần hậu tố đặc biệt được ký hiệu bằng dấu chấm, dùng để cho biết directive cần được bind theo một cách riêng. Ví dụ, modifier `.prevent` bảo directive `v-on` gọi `event.preventDefault()` khi sự kiện xảy ra:

```vue-html
<form @submit.prevent="onSubmit">...</form>
```

Bạn sẽ còn gặp các ví dụ khác về modifier ở phần sau, [với `v-on`](./event-handling#event-modifiers) và [với `v-model`](./forms#modifiers), khi chúng ta đi sâu vào các tính năng đó.

Cuối cùng, đây là hình minh họa đầy đủ cho cú pháp directive:

![directive syntax graph](./images/directive.png)

<!-- https://www.figma.com/file/BGWUknIrtY9HOmbmad0vFr/Directive -->
