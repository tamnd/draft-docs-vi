---
footer: false
---

# Giới thiệu {#introduction}

:::info Bạn đang đọc tài liệu dành cho Vue 3!

- Vue 2 đã ngừng hỗ trợ từ **31/12/2023**. Xem thêm tại [Vue 2 EOL](https://v2.vuejs.org/eol/).
- Nếu bạn đang nâng cấp từ Vue 2, xem [Hướng dẫn nâng cấp](https://v3-migration.vuejs.org/).
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

Vue (đọc là "view") là một framework JavaScript dùng để xây dựng giao diện người dùng (UI).

Nó dựa trên HTML, CSS và JavaScript tiêu chuẩn, đồng thời cung cấp cách lập trình theo kiểu khai báo (declarative) và dựa trên component, giúp xây dựng UI từ đơn giản đến phức tạp một cách hiệu quả.

Ví dụ đơn giản:

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

Ví dụ trên thể hiện hai điểm chính của Vue:

- **Khai báo giao diện (Declarative Rendering)**: Vue mở rộng HTML với cú pháp template, cho phép mô tả HTML dựa trên state trong JavaScript theo cách khai báo.

- **Tính phản ứng (Reactivity)**: Vue tự động theo dõi thay đổi của state và cập nhật DOM khi có thay đổi.

Bạn có thể đã có một số thắc mắc, đừng lo. Phần còn lại của tài liệu sẽ giải thích chi tiết từng phần. Bây giờ, hãy tiếp tục đọc để có cái nhìn tổng quan về những gì Vue cung cấp.

:::tip Điều kiện tiên quyết
Bạn nên biết cơ bản về HTML, CSS và JavaScript. Nếu chưa, nên học nền tảng trước rồi quay lại. Bạn có thể kiểm tra kiến thức qua các bài tổng quan về [JavaScript](https://developer.mozilla.org/en-US/docs/Web/JavaScript/A_re-introduction_to_JavaScript), [HTML](https://developer.mozilla.org/en-US/docs/Learn/HTML/Introduction_to_HTML), và [CSS](https://developer.mozilla.org/en-US/docs/Learn/CSS/First_steps). Kinh nghiệm với các framework khác sẽ có ích, nhưng không bắt buộc.
:::

## Framework linh động {#the-progressive-framework}

Vue là một framework cùng hệ sinh thái bao phủ hầu hết nhu cầu phát triển frontend. Tuy nhiên, vì ứng dụng web rất đa dạng, Vue được thiết kế để dùng linh hoạt, có thể áp dụng dần dần. Bạn có thể dùng Vue theo nhiều cách:

- Thêm vào HTML có sẵn (không cần build)
- Nhúng như Web Components
- Xây dựng SPA (Single Page Application)
- SSR (render phía server)
- SSG (tạo site tĩnh)
- Dùng cho desktop, mobile, WebGL, terminal

Nếu bạn thấy các khái niệm này hơi khó hiểu, đừng lo. Phần tutorial và hướng dẫn chỉ yêu cầu kiến thức cơ bản về HTML và JavaScript, và bạn vẫn có thể theo kịp mà không cần là chuyên gia.

Nếu bạn là lập trình viên có kinh nghiệm và muốn biết cách tích hợp Vue vào stack của mình, xem thêm [Các cách sử dụng Vue](/guide/extras/ways-of-using-vue).

Dù rất linh hoạt, các kiến thức cốt lõi về cách Vue hoạt động vẫn giống nhau trong mọi trường hợp sử dụng. Ngay cả khi bạn mới bắt đầu, những gì bạn học được vẫn sẽ hữu ích khi bạn tiến tới những mục tiêu phức tạp hơn sau này. Nếu bạn đã có kinh nghiệm, bạn có thể chọn cách dùng Vue phù hợp nhất với bài toán của mình mà vẫn giữ được năng suất. Đó là lý do Vue được gọi là "Progressive Framework": một framework có thể phát triển cùng bạn và thích nghi theo nhu cầu của bạn.

## Single-File Components {#single-file-components}

Trong các dự án có build tool, Vue thường dùng file `.vue`, gọi là **Single-File Component** (SFC). Một file SFC chứa logic (JavaScript), template (HTML), và style (CSS) trong cùng một tệp. Ví dụ:

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

SFC là cách viết component được khuyến nghị khi dùng build tool. Bạn có thể tìm hiểu thêm về [cách thức và lý do dùng SFC](/guide/scaling-up/sfc) trong phần riêng của nó.

## Các kiểu API {#api-styles}

Vue có hai cách viết component: **Options API** và **Composition API**.

### Options API {#options-api}

Bạn định nghĩa logic bằng một object gồm các option như `data`, `methods`, `mounted`. Các thuộc tính này sẽ dùng qua `this`, trỏ đến instance của component:

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

Với Composition API, logic của component được viết bằng các hàm API import từ Vue.
Trong Single-File Component (SFC), Composition API thường đi cùng với [`<script setup>`](/api/sfc-script-setup). Đây là một cú pháp đặc biệt giúp Vue xử lý ở thời điểm biên dịch (compile-time), từ đó giảm bớt code lặp (boilerplate).
Cụ thể, các import, biến và hàm khai báo ở cấp cao nhất trong `<script setup>` có thể dùng trực tiếp trong template mà không cần return thủ công như trước.
Dưới đây là cùng một component với template giống hệt, nhưng được viết bằng Composition API và `<script setup>`:

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

Cả hai kiểu API đều đủ khả năng đáp ứng các trường hợp sử dụng phổ biến. Chúng chỉ khác nhau ở cách viết (interface), còn bên dưới chạy trên cùng một hệ thống. Thực tế, Options API được xây dựng dựa trên Composition API. Vì vậy, các khái niệm và kiến thức cốt lõi của Vue là giống nhau ở cả hai kiểu.

Options API xoay quanh khái niệm "component instance" (`this` như trong ví dụ). Cách này thường phù hợp hơn với tư duy kiểu class của những người đến từ các ngôn ngữ lập trình hướng đối tượng (OOP). Nó cũng dễ tiếp cận với người mới vì đã ẩn bớt chi tiết về reactivity, đồng thời giúp tổ chức code rõ ràng thông qua các nhóm option.

Composition API thì tập trung vào việc khai báo trực tiếp các biến state phản ứng trong phạm vi của hàm, và kết hợp state từ nhiều hàm để xử lý các logic phức tạp. Cách này linh hoạt hơn, nhưng cũng yêu cầu bạn hiểu cách reactivity hoạt động trong Vue để sử dụng hiệu quả. Đổi lại, nó cho phép áp dụng các pattern mạnh hơn để tổ chức và tái sử dụng logic.

Bạn có thể tìm hiểu thêm về sự khác nhau giữa hai kiểu này cũng như các lợi ích của Composition API trong phần [Composition API FAQ](/guide/extras/composition-api-faq).

Nếu bạn mới làm quen với Vue, đây là một số khuyến nghị chung:

- Khi học, hãy chọn kiểu nào bạn thấy dễ hiểu hơn. Hầu hết các khái niệm cốt lõi đều giống nhau, và bạn luôn có thể học thêm kiểu còn lại sau.

- Khi dùng trong production:

  - Chọn Options API nếu bạn không dùng build tool, hoặc chỉ dùng Vue cho các trường hợp đơn giản (ví dụ: progressive enhancement).
  - Chọn Composition API + Single-File Components nếu bạn xây dựng ứng dụng hoàn chỉnh với Vue.

Bạn không cần phải chọn cố định một kiểu trong giai đoạn học. Tài liệu sẽ cung cấp ví dụ cho cả hai kiểu khi phù hợp, và bạn có thể chuyển đổi qua lại bất cứ lúc nào bằng **nút API Preference** ở phía trên thanh điều hướng bên trái.

## Vẫn còn câu hỏi? {#still-got-questions}

Xem thêm phần [FAQ](/about/faq).

## Chọn lộ trình học {#pick-your-learning-path}

Mỗi người học theo cách khác nhau, bạn có thể chọn. Nên đọc toàn bộ nếu có thể.

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
