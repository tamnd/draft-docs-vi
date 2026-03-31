# Kết Xuất Có Điều Kiện {#conditional-rendering}

<div class="options-api">
  <VueSchoolLink href="https://vueschool.io/lessons/conditional-rendering-in-vue-3" title="Free Vue.js Conditional Rendering Lesson"/>
</div>

<div class="composition-api">
  <VueSchoolLink href="https://vueschool.io/lessons/vue-fundamentals-capi-conditionals-in-vue" title="Free Vue.js Conditional Rendering Lesson"/>
</div>

<script setup>
import { ref } from 'vue'
const awesome = ref(true)
</script>

## `v-if` {#v-if}

Directive `v-if` dùng để kết xuất có điều kiện một khối. Khối đó chỉ được kết xuất khi biểu thức của directive trả về giá trị truthy.

```vue-html
<h1 v-if="awesome">Vue is awesome!</h1>
```

## `v-else` {#v-else}

Bạn có thể dùng directive `v-else` để chỉ định khối "else" cho `v-if`:

```vue-html
<button @click="awesome = !awesome">Toggle</button>

<h1 v-if="awesome">Vue is awesome!</h1>
<h1 v-else>Oh no 😢</h1>
```

<div class="demo">
  <button @click="awesome = !awesome">Toggle</button>
  <h1 v-if="awesome">Vue is awesome!</h1>
  <h1 v-else>Oh no 😢</h1>
</div>

<div class="composition-api">

[Thử trong Playground](https://play.vuejs.org/#eNpFjkEOgjAQRa8ydIMulLA1hegJ3LnqBskAjdA27RQXhHu4M/GEHsEiKLv5mfdf/sBOxux7j+zAuCutNAQOyZtcKNkZbQkGsFjBCJXVHcQBjYUSqtTKERR3dLpDyCZmQ9bjViiezKKgCIGwM21BGBIAv3oireBYtrK8ZYKtgmg5BctJ13WLPJnhr0YQb1Lod7JaS4G8eATpfjMinjTphC8wtg7zcwNKw/v5eC1fnvwnsfEDwaha7w==)

</div>
<div class="options-api">

[Thử trong Playground](https://play.vuejs.org/#eNpFjj0OwjAMha9iMsEAFWuVVnACNqYsoXV/RJpEqVOQqt6DDYkTcgRSWoplWX7y56fXs6O1u84jixlvM1dbSoXGuzWOIMdCekXQCw2QS5LrzbQLckje6VEJglDyhq1pMAZyHidkGG9hhObRYh0EYWOVJAwKgF88kdFwyFSdXRPBZidIYDWvgqVkylIhjyb4ayOIV3votnXxfwrk2SPU7S/PikfVfsRnGFWL6akCbeD9fLzmK4+WSGz4AA5dYQY=)

</div>

Phần tử có `v-else` phải đứng ngay sau phần tử có `v-if` hoặc `v-else-if` — nếu không, Vue sẽ không nhận ra nó.

## `v-else-if` {#v-else-if}

`v-else-if`, đúng như tên gọi, đóng vai trò là khối "else if" cho `v-if`. Có thể dùng nối tiếp nhiều lần:

```vue-html
<div v-if="type === 'A'">
  A
</div>
<div v-else-if="type === 'B'">
  B
</div>
<div v-else-if="type === 'C'">
  C
</div>
<div v-else>
  Not A/B/C
</div>
```

Tương tự `v-else`, phần tử có `v-else-if` cũng phải đứng ngay sau phần tử có `v-if` hoặc `v-else-if`.

## `v-if` trên `<template>` {#v-if-on-template}

Vì `v-if` là một directive, nó phải được gắn vào một phần tử duy nhất. Nhưng nếu muốn ẩn/hiện nhiều phần tử cùng lúc thì sao? Trong trường hợp này, bạn có thể dùng `v-if` trên phần tử `<template>`, vốn đóng vai trò là một wrapper vô hình. Kết quả kết xuất cuối cùng sẽ không bao gồm phần tử `<template>`.

```vue-html
<template v-if="ok">
  <h1>Title</h1>
  <p>Paragraph 1</p>
  <p>Paragraph 2</p>
</template>
```

`v-else` và `v-else-if` cũng có thể dùng trên `<template>`.

## `v-show` {#v-show}

Một tùy chọn khác để hiển thị có điều kiện là directive `v-show`. Cách dùng về cơ bản giống nhau:

```vue-html
<h1 v-show="ok">Hello!</h1>
```

Điểm khác biệt là phần tử dùng `v-show` luôn được kết xuất và tồn tại trong DOM; `v-show` chỉ chuyển đổi thuộc tính CSS `display` của phần tử đó.

`v-show` không hỗ trợ phần tử `<template>` và không hoạt động cùng với `v-else`.

## `v-if` so với `v-show` {#v-if-vs-v-show}

`v-if` là kết xuất có điều kiện "thực sự" vì nó đảm bảo các event listener và component con bên trong khối điều kiện được hủy và tạo lại đúng cách mỗi khi trạng thái thay đổi.

`v-if` cũng có tính **lazy**: nếu điều kiện là false khi kết xuất lần đầu, Vue sẽ không làm gì cả — khối điều kiện sẽ không được kết xuất cho đến khi điều kiện trở thành true lần đầu tiên.

Ngược lại, `v-show` đơn giản hơn nhiều — phần tử luôn được kết xuất bất kể điều kiện ban đầu là gì, chỉ dùng CSS để ẩn/hiện.

Nói chung, `v-if` tốn chi phí hơn khi chuyển đổi trạng thái, còn `v-show` tốn chi phí hơn khi kết xuất lần đầu. Vì vậy, hãy dùng `v-show` khi cần chuyển đổi thường xuyên, và dùng `v-if` khi điều kiện ít có khả năng thay đổi trong quá trình chạy.

## `v-if` kết hợp với `v-for` {#v-if-with-v-for}

Khi cả `v-if` và `v-for` đều được dùng trên cùng một phần tử, `v-if` sẽ được ưu tiên đánh giá trước. Xem [hướng dẫn kết xuất danh sách](list#v-for-with-v-if) để biết thêm chi tiết.

::: warning Lưu ý
**Không** nên dùng `v-if` và `v-for` trên cùng một phần tử do thứ tự ưu tiên ngầm định có thể gây nhầm lẫn. Tham khảo [hướng dẫn kết xuất danh sách](list#v-for-with-v-if) để biết thêm chi tiết.
:::
