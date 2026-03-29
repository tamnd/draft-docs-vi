# Async Component {#async-components}

## Cách dùng cơ bản {#basic-usage}

Trong các ứng dụng lớn, đôi khi ta cần chia ứng dụng thành những phần nhỏ hơn và chỉ tải một component từ server khi thực sự cần. Để làm điều đó, Vue cung cấp hàm [`defineAsyncComponent`](/api/general#defineasynccomponent):

```js
import { defineAsyncComponent } from 'vue'

const AsyncComp = defineAsyncComponent(() => {
  return new Promise((resolve, reject) => {
    // ...tải component từ server
    resolve(/* component đã tải xong */)
  })
})
// ... dùng `AsyncComp` như một component bình thường
```

Như bạn thấy, `defineAsyncComponent` nhận vào một loader function trả về Promise. Callback `resolve` của Promise nên được gọi khi bạn đã lấy được định nghĩa component từ server. Bạn cũng có thể gọi `reject(reason)` để báo rằng việc tải đã thất bại.

[Dynamic import của ES module](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/import) cũng trả về Promise, vì vậy phần lớn thời gian ta sẽ dùng nó cùng với `defineAsyncComponent`. Các bundler như Vite và webpack cũng hỗ trợ cú pháp này, đồng thời dùng nó làm điểm tách bundle, nên ta có thể dùng nó để import Vue SFC:

```js
import { defineAsyncComponent } from 'vue'

const AsyncComp = defineAsyncComponent(() =>
  import('./components/MyComponent.vue')
)
```

`AsyncComp` tạo ra từ đây là một component bọc, chỉ gọi loader function khi nó thực sự được render trên trang. Ngoài ra, nó sẽ chuyển tiếp mọi prop và slot xuống component bên trong, nên bạn có thể dùng lớp bọc async này để thay thế component gốc một cách liền mạch trong khi vẫn đạt được lazy loading.

Giống như component thông thường, async component cũng có thể được [đăng ký toàn cục](/guide/components/registration#global-registration) bằng `app.component()`:

```js
app.component('MyComponent', defineAsyncComponent(() =>
  import('./components/MyComponent.vue')
))
```

<div class="options-api">

Bạn cũng có thể dùng `defineAsyncComponent` khi [đăng ký component cục bộ](/guide/components/registration#local-registration):

```vue
<script>
import { defineAsyncComponent } from 'vue'

export default {
  components: {
    AdminPage: defineAsyncComponent(() =>
      import('./components/AdminPageComponent.vue')
    )
  }
}
</script>

<template>
  <AdminPage />
</template>
```

</div>

<div class="composition-api">

Chúng cũng có thể được định nghĩa trực tiếp bên trong component cha:

```vue
<script setup>
import { defineAsyncComponent } from 'vue'

const AdminPage = defineAsyncComponent(() =>
  import('./components/AdminPageComponent.vue')
)
</script>

<template>
  <AdminPage />
</template>
```

</div>

## Trạng thái loading và lỗi {#loading-and-error-states}

Các thao tác bất đồng bộ tất yếu sẽ có trạng thái loading và lỗi. `defineAsyncComponent()` hỗ trợ xử lý các trạng thái này thông qua các option nâng cao:

```js
const AsyncComp = defineAsyncComponent({
  // loader function
  loader: () => import('./Foo.vue'),

  // component dùng trong lúc async component đang tải
  loadingComponent: LoadingComponent,
  // độ trễ trước khi hiện loading component. Mặc định: 200ms.
  delay: 200,

  // component dùng nếu việc tải thất bại
  errorComponent: ErrorComponent,
  // error component sẽ được hiển thị nếu có timeout
  // và timeout đó bị vượt quá. Mặc định: Infinity.
  timeout: 3000
})
```

Nếu có khai báo loading component, nó sẽ được hiển thị trước trong lúc component bên trong đang được tải. Mặc định có độ trễ 200ms trước khi loading component xuất hiện. Lý do là trên mạng nhanh, trạng thái loading xuất hiện ngay có thể bị thay thế quá nhanh và trông như nhấp nháy.

Nếu có khai báo error component, nó sẽ được hiển thị khi Promise mà loader function trả về bị reject. Bạn cũng có thể đặt timeout để hiển thị error component khi yêu cầu mất quá nhiều thời gian.

## Lazy Hydration <sup class="vt-badge" data-text="3.5+" /> {#lazy-hydration}

> Phần này chỉ áp dụng nếu bạn đang dùng [Server-Side Rendering](/guide/scaling-up/ssr).

Trong Vue 3.5 trở lên, async component có thể kiểm soát thời điểm được hydrate bằng cách cung cấp một hydration strategy.

- Vue cung cấp sẵn một số hydration strategy. Các strategy tích hợp sẵn này cần được import riêng lẻ để có thể tree-shake nếu không dùng đến.

- Thiết kế này cố ý ở mức thấp để giữ tính linh hoạt. Trong tương lai, cú pháp sugar ở cấp compiler có thể được xây dựng bên trên nó, trong core hoặc ở các giải pháp cấp cao hơn, chẳng hạn Nuxt.

### Hydrate khi rảnh {#hydrate-on-idle}

Hydrate thông qua `requestIdleCallback`:

```js
import { defineAsyncComponent, hydrateOnIdle } from 'vue'

const AsyncComp = defineAsyncComponent({
  loader: () => import('./Comp.vue'),
  hydrate: hydrateOnIdle(/* tùy chọn truyền timeout tối đa */)
})
```

### Hydrate khi hiển thị {#hydrate-on-visible}

Hydrate khi phần tử trở nên hiển thị thông qua `IntersectionObserver`.

```js
import { defineAsyncComponent, hydrateOnVisible } from 'vue'

const AsyncComp = defineAsyncComponent({
  loader: () => import('./Comp.vue'),
  hydrate: hydrateOnVisible()
})
```

Bạn cũng có thể truyền vào một object options cho observer:

```js
hydrateOnVisible({ rootMargin: '100px' })
```

### Hydrate theo media query {#hydrate-on-media-query}

Hydrate khi media query được chỉ định khớp.

```js
import { defineAsyncComponent, hydrateOnMediaQuery } from 'vue'

const AsyncComp = defineAsyncComponent({
  loader: () => import('./Comp.vue'),
  hydrate: hydrateOnMediaQuery('(max-width:500px)')
})
```

### Hydrate khi có tương tác {#hydrate-on-interaction}

Hydrate khi các event được chỉ định xảy ra trên phần tử của component. Event đã kích hoạt hydration cũng sẽ được phát lại sau khi hydration hoàn tất.

```js
import { defineAsyncComponent, hydrateOnInteraction } from 'vue'

const AsyncComp = defineAsyncComponent({
  loader: () => import('./Comp.vue'),
  hydrate: hydrateOnInteraction('click')
})
```

Bạn cũng có thể truyền vào danh sách nhiều loại event:

```js
hydrateOnInteraction(['wheel', 'mouseover'])
```

### Strategy tùy chỉnh {#custom-strategy}

```ts
import { defineAsyncComponent, type HydrationStrategy } from 'vue'

const myStrategy: HydrationStrategy = (hydrate, forEachElement) => {
  // forEachElement là hàm hỗ trợ duyệt qua mọi root element
  // trong DOM chưa hydrate của component, vì root có thể là fragment
  // thay vì một phần tử đơn lẻ
  forEachElement(el => {
    // ...
  })
  // gọi `hydrate` khi đã sẵn sàng
  hydrate()
  return () => {
    // trả về teardown function nếu cần
  }
}

const AsyncComp = defineAsyncComponent({
  loader: () => import('./Comp.vue'),
  hydrate: myStrategy
})
```

## Dùng cùng với Suspense {#using-with-suspense}

Async component có thể được dùng cùng với component dựng sẵn `<Suspense>`. Cách `<Suspense>` tương tác với async component đã được mô tả trong [chương dành riêng cho `<Suspense>`](/guide/built-ins/suspense).
