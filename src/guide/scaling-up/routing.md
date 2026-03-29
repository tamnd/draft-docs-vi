# Routing {#routing}

## Routing Phía Client So Với Phía Server {#client-side-vs-server-side-routing}

Routing ở phía server có nghĩa là server sẽ gửi phản hồi dựa trên đường dẫn URL mà người dùng đang truy cập. Khi ta bấm vào một liên kết trong ứng dụng web render phía server theo kiểu truyền thống, trình duyệt sẽ nhận phản hồi HTML từ server và tải lại toàn bộ trang bằng HTML mới.

Tuy nhiên, trong [Single-Page Application](https://developer.mozilla.org/en-US/docs/Glossary/SPA) (SPA), JavaScript phía client có thể chặn việc điều hướng, tải dữ liệu mới một cách động và cập nhật trang hiện tại mà không cần tải lại toàn bộ trang. Điều này thường mang lại trải nghiệm nhanh nhạy hơn, đặc biệt với những trường hợp sử dụng giống một "ứng dụng" thực thụ, nơi người dùng dự kiến sẽ tương tác nhiều trong một khoảng thời gian dài.

Trong những SPA như vậy, "routing" được thực hiện ở phía client, bên trong trình duyệt. Một router phía client chịu trách nhiệm quản lý phần view được render của ứng dụng bằng các API của trình duyệt như [History API](https://developer.mozilla.org/en-US/docs/Web/API/History) hoặc [`hashchange` event](https://developer.mozilla.org/en-US/docs/Web/API/Window/hashchange_event).

## Router Chính Thức {#official-router}

<!-- TODO update links -->
<div>
  <VueSchoolLink href="https://vueschool.io/courses/vue-router-4-for-everyone" title="Khóa học miễn phí về Vue Router">
    Xem khóa học video miễn phí trên Vue School
  </VueSchoolLink>
</div>

Vue rất phù hợp để xây dựng SPA. Với hầu hết SPA, bạn nên dùng [thư viện Vue Router](https://github.com/vuejs/router) do chính Vue hỗ trợ chính thức. Để biết thêm chi tiết, hãy xem [tài liệu của Vue Router](https://router.vuejs.org/).

## Tự Xây Dựng Routing Đơn Giản {#simple-routing-from-scratch}

Nếu bạn chỉ cần routing rất đơn giản và không muốn kéo vào một thư viện router đầy đủ tính năng, bạn có thể làm điều đó bằng [component động](/guide/essentials/component-basics#dynamic-components) và cập nhật state của component hiện tại bằng cách lắng nghe [`hashchange` event](https://developer.mozilla.org/en-US/docs/Web/API/Window/hashchange_event) của trình duyệt hoặc dùng [History API](https://developer.mozilla.org/en-US/docs/Web/API/History).

Đây là một ví dụ tối giản:

<div class="composition-api">

```vue
<script setup>
import { ref, computed } from 'vue'
import Home from './Home.vue'
import About from './About.vue'
import NotFound from './NotFound.vue'

const routes = {
  '/': Home,
  '/about': About
}

const currentPath = ref(window.location.hash)

window.addEventListener('hashchange', () => {
  currentPath.value = window.location.hash
})

const currentView = computed(() => {
  return routes[currentPath.value.slice(1) || '/'] || NotFound
})
</script>

<template>
  <a href="#/">Home</a> |
  <a href="#/about">About</a> |
  <a href="#/non-existent-path">Broken Link</a>
  <component :is="currentView" />
</template>
```

[Thử trên Playground](https://play.vuejs.org/#eNptUk1vgkAQ/SsTegAThZp4MmhikzY9mKanXkoPWxjLRpgly6JN1P/eWb5Eywlm572ZN2/m5GyKwj9U6CydsIy1LAyUaKpiHZHMC6UNnEDjbgqxyovKYAIX2GmVg8sktwe9qhzbdz+wga15TW++VWX6fB3dAt6UeVEVJT2me2hhEcWKSgOamVjCCk4RAbiBu6xbT5tI2ML8VDeI6HLlxZXWSOZdmJTJPJB3lJSoo5+pWBipyE9FmU4soU2IJHk+MGUrS4OE2nMtIk4F/aA7BW8Cq3WjYlDbP4isQu4wVp0F1Q1uFH1IPDK+c9cb1NW8B03tyJ//uvhlJmP05hM4n60TX/bb2db0CoNmpbxMDgzmRSYMcgQQCkjZhlXkPASRs7YmhoFYw/k+WXvKiNrTcQgpmuFv7ZOZFSyQ4U9a7ZFgK2lvSTXFDqmIQbCUJTMHFkQOBAwKg16kM3W6O7K3eSs+nbeK+eee1V/XKK0dY4Q3vLhR6uJxMUK8/AFKaB6k)

</div>

<div class="options-api">

```vue
<script>
import Home from './Home.vue'
import About from './About.vue'
import NotFound from './NotFound.vue'

const routes = {
  '/': Home,
  '/about': About
}

export default {
  data() {
    return {
      currentPath: window.location.hash
    }
  },
  computed: {
    currentView() {
      return routes[this.currentPath.slice(1) || '/'] || NotFound
    }
  },
  mounted() {
    window.addEventListener('hashchange', () => {
		  this.currentPath = window.location.hash
		})
  }
}
</script>

<template>
  <a href="#/">Home</a> |
  <a href="#/about">About</a> |
  <a href="#/non-existent-path">Broken Link</a>
  <component :is="currentView" />
</template>
```

[Thử trên Playground](https://play.vuejs.org/#eNptUstO6zAQ/ZVR7iKtVJKLxCpKK3Gli1ggxIoNZmGSKbFoxpEzoUi0/87YeVBKNonHPmfOmcdndN00yXuHURblbeFMwxtFpm6sY7i1NcLW2RriJPWBB8bT8/WL7Xh6D9FPwL3lG9tROWHGiwGmqLDUMjhhYgtr+FQEEKdxFqRXfaR9YrkKAoqOnocfQaDEre523PNKzXqx7M8ADrlzNEYAReccEj9orjLYGyrtPtnZQrOxlFS6rXqgZJdPUC5s3YivMhuTDCkeDe6/dSalvognrkybnIgl7c4UuLhcwuHgS3v2/7EPvzRruRXJ7/SDU12W/98l451pGQndIvaWi0rTK8YrEPx64ymKFQOce5DOzlfs4cdlkA+NzdNpBSRgrJudZpQIINdQOdyuVfQnVdHGzydP9QYO549hXIII45qHkKUL/Ail8EUjBgX+z9k3JLgz9OZJgeInYElAkJlWmCcDUBGkAsrTyWS0isYV9bv803x1OTiWwzlrWtxZ2lDGDO90mWepV3+vZojHL3QQKQE=)

</div>
