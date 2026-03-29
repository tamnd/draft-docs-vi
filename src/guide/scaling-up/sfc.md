# Single-File Components {#single-file-components}

## Giới Thiệu {#introduction}

Vue Single-File Components (hay file `*.vue`, viết tắt là **SFC**) là một định dạng file đặc biệt cho phép ta đóng gói template, logic **và** style của một component Vue trong cùng một file. Dưới đây là một ví dụ về SFC:

<div class="options-api">

```vue
<script>
export default {
  data() {
    return {
      greeting: 'Hello World!'
    }
  }
}
</script>

<template>
  <p class="greeting">{{ greeting }}</p>
</template>

<style>
.greeting {
  color: red;
  font-weight: bold;
}
</style>
```

</div>

<div class="composition-api">

```vue
<script setup>
import { ref } from 'vue'
const greeting = ref('Hello World!')
</script>

<template>
  <p class="greeting">{{ greeting }}</p>
</template>

<style>
.greeting {
  color: red;
  font-weight: bold;
}
</style>
```

</div>

Như bạn thấy, Vue SFC là phần mở rộng rất tự nhiên từ bộ ba kinh điển HTML, CSS và JavaScript. Các khối `<template>`, `<script>` và `<style>` giúp đóng gói và đặt phần view, logic và style của một component cạnh nhau trong cùng một file. Cú pháp đầy đủ được định nghĩa trong [Đặc tả cú pháp SFC](/api/sfc-spec).

## Vì Sao Dùng SFC {#why-sfc}

SFC cần một bước build, nhưng đổi lại có rất nhiều lợi ích:

- Viết component theo kiểu mô-đun bằng cú pháp HTML, CSS và JavaScript quen thuộc
- [Đặt cạnh nhau những mối quan tâm vốn gắn chặt với nhau](#what-about-separation-of-concerns)
- Template được biên dịch trước, không tốn chi phí biên dịch lúc chạy
- [CSS được giới hạn phạm vi theo component](/api/sfc-css-features)
- [Cú pháp gọn và thuận tiện hơn khi làm việc với Composition API](/api/sfc-script-setup)
- Có thêm tối ưu hóa ở thời điểm biên dịch nhờ phân tích chéo giữa template và script
- [Hỗ trợ IDE](/guide/scaling-up/tooling#ide-support) với tự động hoàn thành và kiểm tra kiểu cho biểu thức trong template
- Hỗ trợ sẵn Hot Module Replacement (HMR)

SFC là một đặc trưng định hình Vue với tư cách framework, đồng thời là cách tiếp cận được khuyến nghị khi dùng Vue trong các trường hợp sau:

- Single-Page Applications (SPA)
- Static Site Generation (SSG)
- Mọi frontend không quá đơn giản, nơi bước build là hợp lý để đổi lấy trải nghiệm phát triển (DX) tốt hơn.

Dù vậy, chúng tôi hiểu rằng có những tình huống mà SFC có thể là quá tay. Vì vậy, Vue vẫn có thể được dùng bằng JavaScript thuần mà không cần bước build. Nếu bạn chỉ muốn tăng cường một trang HTML phần lớn là tĩnh bằng một vài tương tác nhẹ, hãy xem thêm [petite-vue](https://github.com/vuejs/petite-vue), một tập con 6 kB của Vue được tối ưu cho progressive enhancement.

## Cách Nó Hoạt Động {#how-it-works}

Vue SFC là một định dạng file riêng của framework và phải được [@vue/compiler-sfc](https://github.com/vuejs/core/tree/main/packages/compiler-sfc) biên dịch trước thành JavaScript và CSS tiêu chuẩn. Một SFC sau khi biên dịch là một module JavaScript (ES) tiêu chuẩn, tức là với cấu hình build phù hợp, bạn có thể import SFC như một module:

```js
import MyComponent from './MyComponent.vue'

export default {
  components: {
    MyComponent
  }
}
```

Các thẻ `<style>` bên trong SFC thường được chèn dưới dạng thẻ `<style>` gốc trong quá trình phát triển để hỗ trợ cập nhật nóng. Ở môi trường production, chúng có thể được tách ra và gộp thành một file CSS duy nhất.

Bạn có thể thử nghiệm SFC và xem cách chúng được biên dịch trong [Vue SFC Playground](https://play.vuejs.org/).

Trong các dự án thực tế, ta thường tích hợp trình biên dịch SFC với một build tool như [Vite](https://vite.dev/) hoặc [Vue CLI](http://cli.vuejs.org/) (dựa trên [webpack](https://webpack.js.org/)), và Vue cung cấp các công cụ khởi tạo chính thức để bạn bắt đầu với SFC nhanh nhất có thể. Xem thêm chi tiết ở phần [Công cụ cho SFC](/guide/scaling-up/tooling).

## Còn Việc Tách Biệt Mối Quan Tâm Thì Sao? {#what-about-separation-of-concerns}

Một số người dùng có nền tảng phát triển web truyền thống có thể lo rằng SFC đang trộn nhiều mối quan tâm khác nhau vào cùng một chỗ, trong khi HTML/CSS/JS vốn được tạo ra để tách chúng ra.

Để trả lời câu hỏi này, trước hết ta cần thống nhất rằng **tách biệt mối quan tâm không đồng nghĩa với tách biệt loại file**. Mục tiêu cuối cùng của các nguyên tắc kỹ thuật là cải thiện khả năng bảo trì của codebase. Trong bối cảnh các ứng dụng frontend ngày càng phức tạp, việc áp dụng máy móc nguyên tắc này thành tách riêng theo loại file không giúp ta đạt được mục tiêu đó.

Trong quá trình phát triển UI hiện đại, chúng tôi nhận ra rằng thay vì chia codebase thành ba tầng lớn đan xen chặt chẽ với nhau, việc chia nó thành các component liên kết lỏng và ghép chúng lại với nhau hợp lý hơn nhiều. Bên trong một component, template, logic và style vốn dĩ gắn chặt với nhau, nên việc đặt chúng cạnh nhau thực ra giúp component gắn kết hơn và dễ bảo trì hơn.

Lưu ý rằng ngay cả khi bạn không thích ý tưởng Single-File Components, bạn vẫn có thể tận dụng khả năng tải nóng lại và biên dịch trước của nó bằng cách tách JavaScript và CSS ra các file riêng qua [Src Imports](/api/sfc-spec#src-imports).
