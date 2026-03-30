# Kết xuất khai báo {#declarative-rendering}

<div class="sfc">

Những gì bạn thấy trong trình soạn thảo là một Vue Single-File Component (SFC). SFC là một khối code độc lập, có thể tái sử dụng đóng gói HTML, CSS và JavaScript thuộc về nhau, được viết trong file `.vue`.

</div>

Tính năng cốt lõi của Vue là **kết xuất khai báo**: dùng cú pháp template mở rộng HTML, chúng ta có thể mô tả HTML trông như thế nào dựa trên JavaScript state. Khi state thay đổi, HTML tự động cập nhật.

<div class="composition-api">

State có thể kích hoạt cập nhật khi thay đổi được coi là **reactive**. Chúng ta có thể khai báo reactive state bằng API `reactive()` của Vue. Các object được tạo từ `reactive()` là JavaScript [Proxy](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy) hoạt động giống như object thông thường:

```js
import { reactive } from 'vue'

const counter = reactive({
  count: 0
})

console.log(counter.count) // 0
counter.count++
```

`reactive()` chỉ hoạt động trên object (bao gồm mảng và các kiểu tích hợp như `Map` và `Set`). `ref()`, mặt khác, có thể nhận bất kỳ kiểu giá trị nào và tạo một object expose giá trị bên trong dưới thuộc tính `.value`:

```js
import { ref } from 'vue'

const message = ref('Hello World!')

console.log(message.value) // "Hello World!"
message.value = 'Changed'
```

Chi tiết về `reactive()` và `ref()` được thảo luận trong <a target="_blank" href="/guide/essentials/reactivity-fundamentals.html">Hướng dẫn - Nền tảng về Tính phản ứng</a>.

<div class="sfc">

Reactive state được khai báo trong khối `<script setup>` của component có thể dùng trực tiếp trong template. Đây là cách chúng ta render text động dựa trên giá trị của object `counter` và ref `message`, bằng cú pháp mustache:

</div>

<div class="html">

Object được truyền vào `createApp()` là một Vue component. State của component nên được khai báo bên trong hàm `setup()` của nó, và được trả về bằng một object:

```js{2,5}
setup() {
  const counter = reactive({ count: 0 })
  const message = ref('Hello World!')
  return {
    counter,
    message
  }
}
```

Các thuộc tính trong object được trả về sẽ có sẵn trong template. Đây là cách chúng ta render text động dựa trên giá trị của `message`, bằng cú pháp mustache:

</div>

```vue-html
<h1>{{ message }}</h1>
<p>Count is: {{ counter.count }}</p>
```

Chú ý cách chúng ta không cần dùng `.value` khi truy cập ref `message` trong template: nó được tự động unwrap để sử dụng ngắn gọn hơn.

</div>

<div class="options-api">

State có thể kích hoạt cập nhật khi thay đổi được coi là **reactive**. Trong Vue, reactive state được lưu trong component. <span class="html">Trong code ví dụ, object được truyền vào `createApp()` là một component.</span>

Chúng ta có thể khai báo reactive state bằng tùy chọn component `data`, đây phải là hàm trả về một object:

<div class="sfc">

```js{3-5}
export default {
  data() {
    return {
      message: 'Hello World!'
    }
  }
}
```

</div>
<div class="html">

```js{3-5}
createApp({
  data() {
    return {
      message: 'Hello World!'
    }
  }
})
```

</div>

Thuộc tính `message` sẽ có sẵn trong template. Đây là cách chúng ta render text động dựa trên giá trị của `message`, bằng cú pháp mustache:

```vue-html
<h1>{{ message }}</h1>
```

</div>

Nội dung bên trong mustache không chỉ giới hạn ở identifier hay path - chúng ta có thể dùng bất kỳ biểu thức JavaScript hợp lệ nào:

```vue-html
<h1>{{ message.split('').reverse().join('') }}</h1>
```

<div class="composition-api">

Bây giờ, hãy thử tự tạo một số reactive state và dùng nó để render nội dung text động cho `<h1>` trong template.

</div>

<div class="options-api">

Bây giờ, hãy thử tự tạo một data property và dùng nó làm nội dung text cho `<h1>` trong template.

</div>
