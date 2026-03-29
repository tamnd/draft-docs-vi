---
outline: deep
---

# Suspense {#suspense}

:::warning Tính năng thử nghiệm
`<Suspense>` hiện vẫn là một tính năng thử nghiệm. Chưa có gì đảm bảo nó sẽ đạt trạng thái ổn định, và API có thể còn thay đổi trước khi tới giai đoạn đó.
:::

`<Suspense>` là một component dựng sẵn để điều phối các dependency async trong một cây component. Nó có thể hiển thị trạng thái đang tải trong lúc chờ nhiều dependency async lồng nhau trong cây component được resolve xong.

## Dependency async {#async-dependencies}

Để giải thích vấn đề mà `<Suspense>` muốn giải quyết và cách nó tương tác với các dependency async này, hãy tưởng tượng một cấu trúc component như sau:

```
<Suspense>
└─ <Dashboard>
   ├─ <Profile>
   │  └─ <FriendStatus> (component có async setup())
   └─ <Content>
      ├─ <ActivityFeed> (async component)
      └─ <Stats> (async component)
```

Trong cây component này có nhiều component lồng nhau mà việc render của chúng phụ thuộc vào việc một tài nguyên async nào đó phải resolve xong trước. Nếu không có `<Suspense>`, từng component sẽ phải tự xử lý trạng thái đang tải / lỗi / đã tải xong của riêng nó. Trong tình huống xấu nhất, ta có thể thấy ba loading spinner trên cùng một trang, và nội dung xuất hiện ở các thời điểm khác nhau.

`<Suspense>` cho phép ta hiển thị một trạng thái đang tải / lỗi ở mức cao hơn trong lúc chờ những dependency async lồng nhau này resolve xong.

Có hai loại dependency async mà `<Suspense>` có thể chờ:

1. Component có hook `setup()` bất đồng bộ. Điều này bao gồm cả component dùng `<script setup>` với biểu thức `await` ở cấp cao nhất.
2. [Async Component](/guide/components/async).

### `async setup()` {#async-setup}

Hook `setup()` của một component Composition API có thể là async:

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

Nếu dùng `<script setup>`, sự hiện diện của biểu thức `await` ở cấp cao nhất sẽ tự động khiến component trở thành một dependency async:

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

Async component mặc định là **"suspensible"**. Điều này có nghĩa là nếu trong chuỗi component cha của nó có `<Suspense>`, nó sẽ được xem như một dependency async của `<Suspense>` đó. Trong trường hợp này, trạng thái đang tải sẽ do `<Suspense>` điều khiển, còn các option loading, error, delay và timeout riêng của component sẽ bị bỏ qua.

Async component có thể từ chối để `Suspense` điều khiển và luôn tự quản lý trạng thái đang tải của chính nó bằng cách chỉ định `suspensible: false` trong option của component.

## Trạng thái đang tải {#loading-state}

`<Suspense>` có hai slot: `#default` và `#fallback`. Cả hai slot này chỉ cho phép **một** node con trực tiếp. Node trong slot mặc định sẽ được hiển thị nếu có thể. Nếu không, node trong slot fallback sẽ được hiển thị thay thế.

```vue-html
<Suspense>
  <!-- component có dependency async lồng nhau -->
  <Dashboard />

  <!-- trạng thái đang tải qua slot #fallback -->
  <template #fallback>
    Đang tải...
  </template>
</Suspense>
```

Ở lần render đầu tiên, `<Suspense>` sẽ render nội dung của slot mặc định trong bộ nhớ. Nếu trong quá trình đó gặp dependency async nào, nó sẽ đi vào trạng thái **pending**. Trong trạng thái pending, nội dung fallback sẽ được hiển thị. Khi tất cả dependency async đã gặp đều resolve xong, `<Suspense>` sẽ chuyển sang trạng thái **resolved** và hiển thị nội dung slot mặc định đã được resolve.

Nếu không gặp dependency async nào ở lần render đầu, `<Suspense>` sẽ đi thẳng vào trạng thái resolved.

Khi đã ở trạng thái resolved, `<Suspense>` chỉ quay lại trạng thái pending nếu node gốc của slot `#default` bị thay thế. Những dependency async mới được lồng sâu hơn trong cây sẽ **không** làm `<Suspense>` quay lại trạng thái pending.

Khi việc quay lại đó xảy ra, nội dung fallback sẽ không hiện ra ngay lập tức. Thay vào đó, `<Suspense>` sẽ tiếp tục hiển thị nội dung `#default` trước đó trong lúc chờ nội dung mới cùng các dependency async của nó resolve xong. Hành vi này có thể được cấu hình bằng prop `timeout`: `<Suspense>` sẽ chuyển sang nội dung fallback nếu việc render nội dung mặc định mới mất quá `timeout` mili giây. Giá trị `timeout` bằng `0` sẽ khiến fallback được hiển thị ngay khi nội dung mặc định bị thay thế.

## Sự kiện {#events}

`<Suspense>` phát ra 3 sự kiện: `pending`, `resolve` và `fallback`. Sự kiện `pending` xảy ra khi component đi vào trạng thái pending. Sự kiện `resolve` được phát khi nội dung mới trong slot `default` đã resolve xong. Sự kiện `fallback` được phát khi nội dung của slot `fallback` được hiển thị.

Những sự kiện này có thể được dùng, chẳng hạn, để hiển thị một loading indicator trước DOM cũ trong lúc component mới đang được tải.

## Xử lý lỗi {#error-handling}

Hiện tại, `<Suspense>` chưa tự cung cấp cơ chế xử lý lỗi trong chính component đó. Tuy nhiên, bạn có thể dùng option [`errorCaptured`](/api/options-lifecycle#errorcaptured) hoặc hook [`onErrorCaptured()`](/api/composition-api-lifecycle#onerrorcaptured) để bắt và xử lý lỗi async trong component cha của `<Suspense>`.

## Kết hợp với component khác {#combining-with-other-components}

Rất thường gặp nhu cầu dùng `<Suspense>` cùng với [`<Transition>`](./transition) và [`<KeepAlive>`](./keep-alive). Thứ tự lồng nhau của các component này là rất quan trọng nếu muốn chúng cùng hoạt động đúng.

Ngoài ra, các component này cũng thường được dùng chung với component `<RouterView>` của [Vue Router](https://router.vuejs.org/).

Ví dụ sau cho thấy cách lồng các component này để chúng đều hoạt động như mong đợi. Nếu chỉ cần một tổ hợp đơn giản hơn, bạn có thể bỏ đi những component không dùng:

```vue-html
<RouterView v-slot="{ Component }">
  <template v-if="Component">
    <Transition mode="out-in">
      <KeepAlive>
        <Suspense>
          <!-- nội dung chính -->
          <component :is="Component"></component>

          <!-- trạng thái đang tải -->
          <template #fallback>
            Đang tải...
          </template>
        </Suspense>
      </KeepAlive>
    </Transition>
  </template>
</RouterView>
```

Vue Router có hỗ trợ sẵn việc [tải component theo kiểu lazy](https://router.vuejs.org/guide/advanced/lazy-loading.html) bằng dynamic import. Cơ chế này khác với async component, và hiện tại sẽ không kích hoạt `<Suspense>`. Tuy vậy, chúng vẫn có thể chứa async component ở bên dưới, và những component đó vẫn sẽ kích hoạt `<Suspense>` như bình thường.

## Suspense lồng nhau {#nested-suspense}

- Chỉ được hỗ trợ từ 3.3+

Khi ta có nhiều async component, điều này khá phổ biến với route lồng nhau hoặc route theo layout, như sau:

```vue-html
<Suspense>
  <component :is="DynamicAsyncOuter">
    <component :is="DynamicAsyncInner" />
  </component>
</Suspense>
```

`<Suspense>` sẽ tạo ra một ranh giới để resolve toàn bộ async component ở phía dưới cây, đúng như mong đợi. Tuy nhiên, khi ta đổi `DynamicAsyncOuter`, `<Suspense>` sẽ chờ nó đúng cách. Nhưng khi đổi `DynamicAsyncInner`, component `DynamicAsyncInner` lồng bên trong sẽ render ra một node rỗng cho tới khi nó resolve xong, thay vì hiển thị component cũ hoặc fallback slot.

Để giải quyết việc này, ta có thể dùng một suspense lồng nhau để xử lý việc patch cho component lồng bên trong, như sau:

```vue-html
<Suspense>
  <component :is="DynamicAsyncOuter">
    <Suspense suspensible> <!-- phần này -->
      <component :is="DynamicAsyncInner" />
    </Suspense>
  </component>
</Suspense>
```

Nếu bạn không đặt prop `suspensible`, `<Suspense>` bên trong sẽ bị component cha xem như một component đồng bộ. Điều đó có nghĩa là nó sẽ có fallback slot riêng, và nếu cả hai component `Dynamic` cùng thay đổi một lúc, có thể sẽ xuất hiện node rỗng và nhiều chu kỳ patch trong lúc `<Suspense>` con đang tải cây dependency riêng của nó. Điều này có thể không phải điều bạn mong muốn. Khi đặt prop này, toàn bộ việc xử lý dependency async sẽ được giao cho `<Suspense>` cha, bao gồm cả những sự kiện được phát ra, còn `<Suspense>` con chỉ đóng vai trò như một ranh giới bổ sung cho việc resolve dependency và patch.

---

**Liên quan**

- [API reference của `<Suspense>`](/api/built-in-components#suspense)
