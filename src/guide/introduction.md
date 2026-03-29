---
footer: false
---

# Giới thiệu {#introduction}

:::info Bạn đang đọc tài liệu cho Vue 3!

- Hỗ trợ cho Vue 2 đã kết thúc vào **ngày 31 tháng 12 năm 2023**. Tìm hiểu thêm tại [Vue 2 EOL](https://v2.vuejs.org/eol/).
- Đang nâng cấp từ Vue 2? Hãy xem [Hướng dẫn di chuyển](https://v3-migration.vuejs.org/).
  :::

<style src="@theme/styles/vue-mastery.css"></style>
<div class="vue-mastery-link">
  <a href="https://www.vuemastery.com/courses/" target="_blank">
    <div class="banner-wrapper">
      <img class="banner" alt="Vue Mastery banner" width="96px" height="56px" src="https://storage.googleapis.com/vue-mastery.appspot.com/flamelink/media/vuemastery-graphical-link-96x56.png" />
    </div>
    <p class="description">Học Vue qua các video hướng dẫn tại <span>VueMastery.com</span></p>
    <div class="logo-wrapper">
        <img alt="Vue Mastery Logo" width="25px" src="https://storage.googleapis.com/vue-mastery.appspot.com/flamelink/media/vue-mastery-logo.png" />
    </div>
  </a>
</div>

## Vue là gì? {#what-is-vue}

Vue (đọc là /vjuː/, giống như **view**) là một framework JavaScript dùng để xây dựng giao diện người dùng. Nó được xây dựng trên nền HTML, CSS, và JavaScript tiêu chuẩn, đồng thời cung cấp mô hình lập trình khai báo dựa trên component giúp bạn phát triển giao diện người dùng ở mọi mức độ phức tạp một cách hiệu quả.

Đây là một ví dụ tối giản:

<div class="options-api">

```js
import { createApp } from 'vue'

createApp({
  data() {
    return {
      count: 0
    }
  }
}).mount('#app')
```

</div>
<div class="composition-api">

```js
import { createApp, ref } from 'vue'

createApp({
  setup() {
    return {
      count: ref(0)
    }
  }
}).mount('#app')
```

</div>

```vue-html
<div id="app">
  <button @click="count++">
    Số đếm là: {{ count }}
  </button>
</div>
```

**Kết quả**

<script setup>
import { ref } from 'vue'
const count = ref(0)
</script>

<div class="demo">
  <button @click="count++">
    Số đếm là: {{ count }}
  </button>
</div>

Ví dụ trên minh họa hai tính năng cốt lõi của Vue:

- **Kết xuất khai báo**: Vue mở rộng HTML tiêu chuẩn bằng cú pháp template cho phép chúng ta mô tả đầu ra HTML một cách khai báo dựa trên state trong JavaScript.

- **Tính phản ứng**: Vue tự động theo dõi các thay đổi của state trong JavaScript và cập nhật DOM một cách hiệu quả khi chúng xảy ra.

Có thể lúc này bạn đã có một số câu hỏi, đừng lo. Chúng ta sẽ lần lượt đi qua từng chi tiết trong phần còn lại của tài liệu. Còn bây giờ, hãy tiếp tục đọc để có được cái nhìn tổng quan về những gì Vue mang lại.

:::tip Điều kiện tiên quyết
Phần còn lại của tài liệu giả định rằng bạn đã quen với HTML, CSS, và JavaScript ở mức cơ bản. Nếu bạn hoàn toàn mới với phát triển frontend, thì bắt đầu ngay bằng một framework có thể chưa phải là ý tưởng tốt nhất. Hãy nắm vững kiến thức nền tảng trước rồi quay lại sau. Nếu cần, bạn có thể tự kiểm tra mức độ hiểu biết của mình qua các bài tổng quan về [JavaScript](https://developer.mozilla.org/en-US/docs/Web/JavaScript/A_re-introduction_to_JavaScript), [HTML](https://developer.mozilla.org/en-US/docs/Learn/HTML/Introduction_to_HTML), và [CSS](https://developer.mozilla.org/en-US/docs/Learn/CSS/First_steps). Kinh nghiệm với các framework khác sẽ có ích, nhưng không bắt buộc.
:::

## Framework Tiến Bộ {#the-progressive-framework}

Vue là một framework cùng hệ sinh thái bao phủ hầu hết các tính năng phổ biến cần thiết trong phát triển frontend. Tuy nhiên, web là một môi trường cực kỳ đa dạng, những gì chúng ta xây dựng trên đó có thể khác nhau rất nhiều về hình thức và quy mô. Vì vậy, Vue được thiết kế để linh hoạt và có thể được áp dụng dần dần. Tùy vào từng trường hợp sử dụng, Vue có thể được dùng theo nhiều cách khác nhau:

- Tăng cường HTML tĩnh mà không cần bước build
- Nhúng dưới dạng Web Components trên bất kỳ trang nào
- Ứng dụng một trang (SPA)
- Fullstack / Kết xuất phía máy chủ (SSR)
- Jamstack / Tạo trang tĩnh (SSG)
- Nhắm tới desktop, mobile, WebGL, và thậm chí cả terminal

Nếu những khái niệm này khiến bạn thấy hơi ngợp, đừng lo. Phần hướng dẫn tương tác và hướng dẫn chính chỉ yêu cầu kiến thức HTML và JavaScript cơ bản, và bạn vẫn có thể theo kịp mà không cần là chuyên gia trong bất kỳ lĩnh vực nào ở trên.

Nếu bạn là một lập trình viên có kinh nghiệm và muốn biết cách tích hợp Vue vào stack của mình sao cho phù hợp nhất, hoặc đơn giản là tò mò những thuật ngữ trên có nghĩa gì, chúng tôi đã thảo luận chi tiết hơn trong [Các cách sử dụng Vue](/guide/extras/ways-of-using-vue).

Dù rất linh hoạt, kiến thức cốt lõi về cách Vue hoạt động vẫn được chia sẻ giữa tất cả các trường hợp sử dụng này. Ngay cả khi bây giờ bạn mới chỉ là người mới bắt đầu, những gì bạn học được vẫn sẽ tiếp tục hữu ích khi bạn phát triển để chinh phục những mục tiêu tham vọng hơn trong tương lai. Còn nếu bạn là người đã dày dạn kinh nghiệm, bạn có thể chọn cách tận dụng Vue tối ưu nhất dựa trên vấn đề mình đang muốn giải quyết mà vẫn giữ nguyên năng suất. Đó là lý do chúng tôi gọi Vue là "Framework Tiến Bộ": một framework có thể phát triển cùng bạn và thích nghi với nhu cầu của bạn.

## Single-File Components {#single-file-components}

Trong hầu hết các dự án Vue có dùng build tool, chúng ta viết component Vue bằng một định dạng tệp giống HTML được gọi là **Single-File Component** (hay còn gọi là tệp `*.vue`, viết tắt là **SFC**). Đúng như tên gọi, một Vue SFC đóng gói logic của component (JavaScript), template (HTML), và style (CSS) trong cùng một tệp. Dưới đây là ví dụ trước đó được viết lại theo định dạng SFC:

<div class="options-api">

```vue
<script>
export default {
  data() {
    return {
      count: 0
    }
  }
}
</script>

<template>
  <button @click="count++">Số đếm là: {{ count }}</button>
</template>

<style scoped>
button {
  font-weight: bold;
}
</style>
```

</div>
<div class="composition-api">

```vue
<script setup>
import { ref } from 'vue'
const count = ref(0)
</script>

<template>
  <button @click="count++">Số đếm là: {{ count }}</button>
</template>

<style scoped>
button {
  font-weight: bold;
}
</style>
```

</div>

SFC là một tính năng đặc trưng của Vue và là cách được khuyến nghị để viết Vue component **nếu** trường hợp sử dụng của bạn phù hợp với một môi trường có build setup. Bạn có thể tìm hiểu thêm về [cách thức và lý do dùng SFC](/guide/scaling-up/sfc) trong phần riêng của nó, còn bây giờ chỉ cần biết rằng Vue sẽ giúp bạn xử lý toàn bộ phần thiết lập build tool.

## Các kiểu API {#api-styles}

Vue component có thể được viết theo hai kiểu API khác nhau: **Options API** và **Composition API**.

### Options API {#options-api}

Với Options API, chúng ta định nghĩa logic của component bằng một object các option như `data`, `methods`, và `mounted`. Những thuộc tính được khai báo trong các option này sẽ được expose trên `this` bên trong các hàm, và `this` sẽ trỏ tới instance của component:

```vue
<script>
export default {
  // Các thuộc tính trả về từ data() sẽ trở thành state phản ứng
  // và được expose trên `this`.
  data() {
    return {
      count: 0
    }
  },

  // Methods là các hàm làm thay đổi state và kích hoạt cập nhật.
  // Chúng có thể được gắn làm event handler trong template.
  methods: {
    increment() {
      this.count++
    }
  },

  // Lifecycle hooks được gọi ở các giai đoạn khác nhau
  // trong vòng đời của component.
  // Hàm này sẽ được gọi khi component được mount.
  mounted() {
    console.log(`Giá trị đếm ban đầu là ${this.count}.`)
  }
}
</script>

<template>
  <button @click="increment">Số đếm là: {{ count }}</button>
</template>
```

[Thử trên Playground](https://play.vuejs.org/#eNptkMFqxCAQhl9lkB522ZL0HNKlpa/Qo4e1ZpLIGhUdl5bgu9es2eSyIMio833zO7NP56pbRNawNkivHJ25wV9nPUGHvYiaYOYGoK7Bo5CkbgiBBOFy2AkSh2N5APmeojePCkDaaKiBt1KnZUuv3Ky0PppMsyYAjYJgigu0oEGYDsirYUAP0WULhqVrQhptF5qHQhnpcUJD+wyQaSpUd/Xp9NysVY/yT2qE0dprIS/vsds5Mg9mNVbaDofL94jZpUgJXUKBCvAy76ZUXY53CTd5tfX2k7kgnJzOCXIF0P5EImvgQ2olr++cbRE4O3+t6JxvXj0ptXVpye1tvbFY+ge/NJZt)

### Composition API {#composition-api}

Với Composition API, chúng ta định nghĩa logic của component bằng các hàm API được import vào. Trong SFC, Composition API thường được dùng cùng với [`<script setup>`](/api/sfc-script-setup). Thuộc tính `setup` là một chỉ dấu để Vue thực hiện các phép biến đổi ở thời điểm biên dịch, từ đó cho phép chúng ta dùng Composition API với ít boilerplate hơn. Ví dụ, các import và các biến / hàm cấp cao nhất được khai báo trong `<script setup>` có thể dùng trực tiếp trong template.

Đây là cùng một component, với chính xác template như trên, nhưng được viết bằng Composition API và `<script setup>`:

```vue
<script setup>
import { ref, onMounted } from 'vue'

// state phản ứng
const count = ref(0)

// các hàm làm thay đổi state và kích hoạt cập nhật
function increment() {
  count.value++
}

// lifecycle hooks
onMounted(() => {
  console.log(`Giá trị đếm ban đầu là ${count.value}.`)
})
</script>

<template>
  <button @click="increment">Số đếm là: {{ count }}</button>
</template>
```

[Thử trên Playground](https://play.vuejs.org/#eNpNkMFqwzAQRH9lMYU4pNg9Bye09NxbjzrEVda2iLwS0spQjP69a+yYHnRYad7MaOfiw/tqSliciybqYDxDRE7+qsiM3gWGGQJ2r+DoyyVivEOGLrgRDkIdFCmqa1G0ms2EELllVKQdRQa9AHBZ+PLtuEm7RCKVd+ChZRjTQqwctHQHDqbvMUDyd7mKip4AGNIBRyQujzArgtW/mlqb8HRSlLcEazrUv9oiDM49xGGvXgp5uT5his5iZV1f3r4HFHvDprVbaxPhZf4XkKub/CDLaep1T7IhGRhHb6WoTADNT2KWpu/aGv24qGKvrIrr5+Z7hnneQnJu6hURvKl3ryL/ARrVkuI=)

### Nên chọn kiểu nào? {#which-to-choose}

Cả hai kiểu API đều hoàn toàn đủ khả năng bao phủ các trường hợp sử dụng phổ biến. Chúng là hai giao diện khác nhau nhưng được vận hành bởi cùng một hệ thống nền tảng. Thực tế, Options API được xây dựng trên chính Composition API. Những khái niệm và kiến thức nền tảng về Vue được chia sẻ giữa cả hai kiểu này.

Options API xoay quanh khái niệm "component instance" (`this` như trong ví dụ), điều này thường phù hợp hơn với tư duy kiểu class đối với những người đến từ nền tảng ngôn ngữ lập trình hướng đối tượng. Nó cũng thân thiện hơn với người mới bắt đầu nhờ việc che bớt các chi tiết về reactivity và tổ chức mã nguồn thông qua các nhóm option.

Composition API xoay quanh việc khai báo trực tiếp các biến state phản ứng trong phạm vi của hàm và kết hợp state từ nhiều hàm để xử lý độ phức tạp. Nó linh hoạt hơn và đòi hỏi bạn phải hiểu cách reactivity hoạt động trong Vue để có thể sử dụng hiệu quả. Đổi lại, tính linh hoạt đó cho phép bạn áp dụng những mẫu tổ chức và tái sử dụng logic mạnh mẽ hơn.

Bạn có thể tìm hiểu thêm về sự so sánh giữa hai kiểu này cùng những lợi ích tiềm năng của Composition API trong [Composition API FAQ](/guide/extras/composition-api-faq).

Nếu bạn mới làm quen với Vue, đây là khuyến nghị chung của chúng tôi:

- Với mục đích học tập, hãy chọn kiểu nào mà bạn thấy dễ hiểu hơn. Một lần nữa, hầu hết các khái niệm cốt lõi đều được chia sẻ giữa hai kiểu này. Bạn luôn có thể học thêm kiểu còn lại sau.

- Với môi trường production:

  - Hãy chọn Options API nếu bạn không dùng build tool, hoặc dự định dùng Vue chủ yếu trong các tình huống có độ phức tạp thấp, ví dụ như progressive enhancement.

  - Hãy chọn Composition API + Single-File Components nếu bạn định xây dựng các ứng dụng hoàn chỉnh với Vue.

Bạn không cần phải cam kết chỉ dùng một kiểu duy nhất trong giai đoạn học tập. Phần còn lại của tài liệu sẽ cung cấp ví dụ mã nguồn ở cả hai kiểu khi phù hợp, và bạn có thể chuyển đổi giữa chúng bất kỳ lúc nào bằng **công tắc tùy chọn API** ở phía trên cùng của thanh bên trái.

## Vẫn còn câu hỏi? {#still-got-questions}

Hãy xem [FAQ](/about/faq) của chúng tôi.

## Chọn lộ trình học tập của bạn {#pick-your-learning-path}

Mỗi lập trình viên có một phong cách học khác nhau. Hãy thoải mái chọn lộ trình học phù hợp với bạn, dù vậy chúng tôi vẫn khuyến khích bạn đọc qua toàn bộ nội dung nếu có thể.

<div class="vt-box-container next-steps">
  <a class="vt-box" href="/tutorial/">
    <p class="next-steps-link">Thử phần hướng dẫn tương tác</p>
    <p class="next-steps-caption">Dành cho những ai thích học bằng cách trực tiếp thực hành.</p>
  </a>
  <a class="vt-box" href="/guide/quick-start.html">
    <p class="next-steps-link">Đọc phần hướng dẫn</p>
    <p class="next-steps-caption">Phần hướng dẫn sẽ đưa bạn đi qua từng khía cạnh của framework một cách đầy đủ và chi tiết.</p>
  </a>
  <a class="vt-box" href="/examples/">
    <p class="next-steps-link">Xem các ví dụ</p>
    <p class="next-steps-caption">Khám phá các ví dụ về những tính năng cốt lõi và các tác vụ UI phổ biến.</p>
  </a>
</div>
