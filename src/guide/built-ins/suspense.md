---
outline: deep
---

# Suspense {#suspense}

:::warning Tính năng thử nghiệm
`<Suspense>` là một tính năng thử nghiệm. Chưa có gì bảo đảm nó sẽ đạt trạng thái ổn định, và API có thể thay đổi trước khi điều đó xảy ra.
:::

`<Suspense>` là một component dựng sẵn dùng để điều phối các dependency bất đồng bộ trong cây component. Nó có thể render trạng thái loading trong lúc chờ nhiều dependency bất đồng bộ lồng sâu trong cây component được giải quyết xong.

## Dependency bất đồng bộ {#async-dependencies}

Để giải thích vấn đề mà `<Suspense>` cố gắng giải quyết và cách nó tương tác với các dependency bất đồng bộ này, hãy tưởng tượng một cấu trúc component như sau:

```
<Suspense>
└─ <Dashboard>
   ├─ <Profile>
   │  └─ <FriendStatus> (component có async setup())
   └─ <Content>
      ├─ <ActivityFeed> (async component)
      └─ <Stats> (async component)
```

Trong cây component này có nhiều component lồng nhau mà việc render của chúng phụ thuộc vào một số tài nguyên bất đồng bộ cần được giải quyết trước. Nếu không có `<Suspense>`, mỗi component sẽ phải tự xử lý trạng thái loading / error và trạng thái đã tải xong của riêng nó. Trong trường hợp xấu nhất, ta có thể thấy ba loading spinner cùng xuất hiện trên trang, còn nội dung thì hiển thị vào những thời điểm khác nhau.

Component `<Suspense>` cho phép ta hiển thị các trạng thái loading / error ở cấp cao trong lúc chờ những dependency bất đồng bộ lồng nhau đó được giải quyết.

Có hai loại dependency bất đồng bộ mà `<Suspense>` có thể chờ:

1. Component có hook `setup()` bất đồng bộ. Điều này bao gồm cả component dùng `<script setup>` với biểu thức `await` ở cấp cao nhất.

2. [Async Components](/guide/components/async).

### `async setup()` {#async-setup}

Hook `setup()` của một component Composition API có thể là bất đồng bộ:

```js
export default {
  async setup() {
    const res = await fetch(...)
    const posts = await res.json()
    return {
      posts
    }
  }
}
```

Nếu dùng `<script setup>`, việc có biểu thức `await` ở cấp cao nhất sẽ tự động biến component đó thành một dependency bất đồng bộ:

```vue
<script setup>
const res = await fetch(...)
const posts = await res.json()
</script>

<template>
  {{ posts }}
</template>
```

### Async Component {#async-components}

Async component mặc định là **"suspensible"**. Điều đó có nghĩa là nếu trong chuỗi cha của nó có một `<Suspense>`, nó sẽ được xem là dependency bất đồng bộ của `<Suspense>` đó. Khi ấy, trạng thái loading sẽ do `<Suspense>` điều khiển, còn các option loading, error, delay và timeout của chính component sẽ bị bỏ qua.

Async component có thể từ chối để `Suspense` điều khiển bằng cách đặt `suspensible: false` trong option của nó, nhờ đó component luôn tự kiểm soát trạng thái loading của chính mình.

## Trạng thái loading {#loading-state}

Component `<Suspense>` có hai slot: `#default` và `#fallback`. Cả hai slot này chỉ cho phép **một** node con trực tiếp. Node trong default slot sẽ được hiển thị nếu có thể. Nếu không, node trong fallback slot sẽ được hiển thị thay thế.

```vue-html
<Suspense>
  <!-- component có dependency bất đồng bộ lồng nhau -->
  <Dashboard />

  <!-- trạng thái loading qua slot #fallback -->
  <template #fallback>
    Đang tải...
  </template>
</Suspense>
```

Ở lần render đầu tiên, `<Suspense>` sẽ render nội dung của default slot trong bộ nhớ. Nếu gặp bất kỳ dependency bất đồng bộ nào trong quá trình đó, nó sẽ chuyển sang trạng thái **pending**. Trong trạng thái pending, nội dung fallback sẽ được hiển thị. Khi mọi dependency bất đồng bộ đã gặp đều được giải quyết xong, `<Suspense>` sẽ chuyển sang trạng thái **resolved** và hiển thị nội dung default slot đã được giải quyết.

Nếu không gặp dependency bất đồng bộ nào ở lần render đầu tiên, `<Suspense>` sẽ đi thẳng vào trạng thái resolved.

Khi đã ở trạng thái resolved, `<Suspense>` chỉ quay lại trạng thái pending nếu root node của slot `#default` bị thay thế. Những dependency bất đồng bộ mới nằm sâu hơn trong cây sẽ **không** khiến `<Suspense>` quay lại trạng thái pending.

Khi việc quay lại đó xảy ra, nội dung fallback sẽ không được hiển thị ngay lập tức. Thay vào đó, `<Suspense>` sẽ tiếp tục hiển thị nội dung `#default` trước đó trong lúc chờ nội dung mới và các dependency bất đồng bộ của nó được giải quyết. Hành vi này có thể được cấu hình bằng prop `timeout`: `<Suspense>` sẽ chuyển sang hiển thị fallback nếu việc render nội dung default mới mất lâu hơn `timeout` mili giây. Nếu `timeout` bằng `0`, nội dung fallback sẽ được hiển thị ngay khi nội dung default bị thay thế.

## Event {#events}

Component `<Suspense>` phát ra 3 event: `pending`, `resolve` và `fallback`. Event `pending` xảy ra khi đi vào trạng thái pending. Event `resolve` được phát khi nội dung mới trong slot `default` đã được giải quyết xong. Event `fallback` được kích hoạt khi nội dung của slot `fallback` được hiển thị.

Những event này có thể được dùng, chẳng hạn, để hiển thị một loading indicator phía trước DOM cũ trong lúc các component mới đang tải.

## Xử lý lỗi {#error-handling}

Hiện tại, bản thân `<Suspense>` chưa cung cấp cơ chế xử lý lỗi riêng. Tuy vậy, bạn có thể dùng option [`errorCaptured`](/api/options-lifecycle#errorcaptured) hoặc hook [`onErrorCaptured()`](/api/composition-api-lifecycle#onerrorcaptured) để bắt và xử lý lỗi bất đồng bộ trong component cha của `<Suspense>`.

## Kết hợp với component khác {#combining-with-other-components}

Nhu cầu dùng `<Suspense>` cùng với [`<Transition>`](./transition) và [`<KeepAlive>`](./keep-alive) là khá phổ biến. Thứ tự lồng nhau của các component này rất quan trọng để mọi thứ hoạt động đúng.

Ngoài ra, các component này cũng thường được dùng cùng với component `<RouterView>` của [Vue Router](https://router.vuejs.org/).

Ví dụ dưới đây cho thấy cách lồng các component này để chúng cùng hoạt động như mong đợi. Nếu chỉ cần kết hợp đơn giản hơn, bạn có thể bỏ đi những component không cần dùng:

```vue-html
<RouterView v-slot="{ Component }">
  <template v-if="Component">
    <Transition mode="out-in">
      <KeepAlive>
        <Suspense>
          <!-- nội dung chính -->
          <component :is="Component"></component>

          <!-- trạng thái loading -->
          <template #fallback>
            Đang tải...
          </template>
        </Suspense>
      </KeepAlive>
    </Transition>
  </template>
</RouterView>
```

Vue Router có hỗ trợ tích hợp sẵn cho [việc tải component theo kiểu lazy](https://router.vuejs.org/guide/advanced/lazy-loading.html) bằng dynamic import. Cơ chế này khác với async component và hiện tại sẽ không kích hoạt `<Suspense>`. Tuy nhiên, chúng vẫn có thể chứa async component ở sâu bên trong, và khi đó các async component đó vẫn có thể kích hoạt `<Suspense>` như bình thường.

## Suspense lồng nhau {#nested-suspense}

- Chỉ được hỗ trợ từ 3.3+

Khi ta có nhiều async component, điều thường gặp ở route lồng nhau hoặc route dựa trên layout, như thế này:

```vue-html
<Suspense>
  <component :is="DynamicAsyncOuter">
    <component :is="DynamicAsyncInner" />
  </component>
</Suspense>
```

`<Suspense>` tạo ra một boundary sẽ giải quyết mọi async component nằm sâu trong cây, đúng như kỳ vọng. Tuy nhiên, khi ta đổi `DynamicAsyncOuter`, `<Suspense>` sẽ chờ nó một cách chính xác; còn khi đổi `DynamicAsyncInner`, `DynamicAsyncInner` lồng bên trong sẽ render ra một node rỗng cho tới khi nó được giải quyết xong, thay vì giữ lại nội dung cũ hoặc fallback slot.

Để giải quyết việc đó, ta có thể dùng một suspense lồng nhau để xử lý lần patch cho component lồng bên trong, như sau:

```vue-html
<Suspense>
  <component :is="DynamicAsyncOuter">
    <Suspense suspensible> <!-- ở đây -->
      <component :is="DynamicAsyncInner" />
    </Suspense>
  </component>
</Suspense>
```

Nếu bạn không đặt prop `suspensible`, `<Suspense>` bên trong sẽ bị `<Suspense>` cha xem như một component đồng bộ. Điều đó có nghĩa là nó có fallback slot riêng, và nếu cả hai component `Dynamic` cùng thay đổi một lúc, có thể sẽ xuất hiện node rỗng và nhiều vòng patch trong lúc `<Suspense>` con đang tải cây dependency của riêng nó, điều này có thể không mong muốn. Khi prop này được bật, toàn bộ việc xử lý dependency bất đồng bộ sẽ được giao cho `<Suspense>` cha, bao gồm cả các event được phát ra, còn `<Suspense>` bên trong chỉ đóng vai trò như một boundary bổ sung cho việc giải quyết dependency và patch.

---

**Liên quan**

- [`<Suspense>` API reference](/api/built-in-components#suspense)
