# Composition API: Hook Vòng đời {#composition-api-lifecycle-hooks}

:::info Lưu ý Sử dụng
Tất cả API liệt kê trên trang này phải được gọi đồng bộ trong giai đoạn `setup()` của component. Xem [Hướng dẫn - Hook Vòng đời](/guide/essentials/lifecycle) để biết thêm chi tiết.
:::

## onMounted() {#onmounted}

Đăng ký callback được gọi sau khi component được mount.

- **Kiểu**

  ```ts
  function onMounted(callback: () => void, target?: ComponentInternalInstance | null): void
  ```

- **Chi tiết**

  Một component được coi là đã mount sau khi:

  - Tất cả component con đồng bộ của nó đã được mount (không bao gồm component async hoặc component bên trong cây `<Suspense>`).

  - Cây DOM của chính nó đã được tạo và chèn vào container cha. Lưu ý điều này chỉ đảm bảo rằng cây DOM của component trong document nếu container gốc của ứng dụng cũng trong document.

  Hook này thường được dùng để thực hiện các side effect cần truy cập DOM đã render của component, hoặc để giới hạn code liên quan đến DOM ở phía client trong [ứng dụng kết xuất phía máy chủ](/guide/scaling-up/ssr).

  **Hook này không được gọi trong quá trình kết xuất phía máy chủ.**

- **Ví dụ**

  Truy cập element qua template ref:

  ```vue
  <script setup>
  import { ref, onMounted } from 'vue'

  const el = ref()

  onMounted(() => {
    el.value // <div>
  })
  </script>

  <template>
    <div ref="el"></div>
  </template>
  ```

## onUpdated() {#onupdated}

Đăng ký callback được gọi sau khi component đã cập nhật cây DOM do thay đổi state phản ứng.

- **Kiểu**

  ```ts
  function onUpdated(callback: () => void, target?: ComponentInternalInstance | null): void
  ```

- **Chi tiết**

  Hook updated của component cha được gọi sau hook của các component con của nó.

  Hook này được gọi sau bất kỳ cập nhật DOM nào của component, có thể do các thay đổi state khác nhau, vì nhiều thay đổi state có thể được gộp thành một chu kỳ render đơn cho mục đích hiệu năng. Nếu bạn cần truy cập DOM đã cập nhật sau một thay đổi state cụ thể, hãy dùng [nextTick()](/api/general#nexttick) thay thế.

  **Hook này không được gọi trong quá trình kết xuất phía máy chủ.**

  :::warning
  Đừng thay đổi state component trong hook updated - điều này có thể dẫn đến vòng lặp cập nhật vô hạn!
  :::

- **Ví dụ**

  Truy cập DOM đã cập nhật:

  ```vue
  <script setup>
  import { ref, onUpdated } from 'vue'

  const count = ref(0)

  onUpdated(() => {
    // nội dung text nên giống với giá trị `count.value` hiện tại
    console.log(document.getElementById('count').textContent)
  })
  </script>

  <template>
    <button id="count" @click="count++">{{ count }}</button>
  </template>
  ```

## onUnmounted() {#onunmounted}

Đăng ký callback được gọi sau khi component đã được unmount.

- **Kiểu**

  ```ts
  function onUnmounted(callback: () => void, target?: ComponentInternalInstance | null): void
  ```

- **Chi tiết**

  Một component được coi là đã unmount sau khi:

  - Tất cả component con của nó đã được unmount.

  - Tất cả reactive effect liên quan của nó (render effect và computed/watcher được tạo trong `setup()`) đã bị dừng.

  Dùng hook này để dọn dẹp các side effect được tạo thủ công như timer, DOM event listener hoặc kết nối server.

  **Hook này không được gọi trong quá trình kết xuất phía máy chủ.**

- **Ví dụ**

  ```vue
  <script setup>
  import { onMounted, onUnmounted } from 'vue'

  let intervalId
  onMounted(() => {
    intervalId = setInterval(() => {
      // ...
    })
  })

  onUnmounted(() => clearInterval(intervalId))
  </script>
  ```

## onBeforeMount() {#onbeforemount}

Đăng ký hook được gọi ngay trước khi component được mount.

- **Kiểu**

  ```ts
  function onBeforeMount(callback: () => void, target?: ComponentInternalInstance | null): void
  ```

- **Chi tiết**

  Khi hook này được gọi, component đã hoàn thành việc thiết lập state phản ứng, nhưng chưa có DOM node nào được tạo. Nó sắp thực thi render effect DOM lần đầu tiên.

  **Hook này không được gọi trong quá trình kết xuất phía máy chủ.**

## onBeforeUpdate() {#onbeforeupdate}

Đăng ký hook được gọi ngay trước khi component sắp cập nhật cây DOM do thay đổi state phản ứng.

- **Kiểu**

  ```ts
  function onBeforeUpdate(callback: () => void, target?: ComponentInternalInstance | null): void
  ```

- **Chi tiết**

  Hook này có thể dùng để truy cập trạng thái DOM trước khi Vue cập nhật DOM. Cũng an toàn khi thay đổi state component bên trong hook này.

  **Hook này không được gọi trong quá trình kết xuất phía máy chủ.**

## onBeforeUnmount() {#onbeforeunmount}

Đăng ký hook được gọi ngay trước khi instance component sắp được unmount.

- **Kiểu**

  ```ts
  function onBeforeUnmount(callback: () => void, target?: ComponentInternalInstance | null): void
  ```

- **Chi tiết**

  Khi hook này được gọi, instance component vẫn còn hoạt động hoàn toàn.

  **Hook này không được gọi trong quá trình kết xuất phía máy chủ.**

## onErrorCaptured() {#onerrorcaptured}

Đăng ký hook được gọi khi một lỗi truyền từ component con đã được bắt.

- **Kiểu**

  ```ts
  function onErrorCaptured(callback: ErrorCapturedHook): void

  type ErrorCapturedHook = (
    err: unknown,
    instance: ComponentPublicInstance | null,
    info: string
  ) => boolean | void
  ```

- **Chi tiết**

  Lỗi có thể được bắt từ các nguồn sau:

  - Render component
  - Event handler
  - Hook vòng đời
  - Hàm `setup()`
  - Watcher
  - Hook directive tùy chỉnh
  - Hook transition

  Hook nhận ba đối số: lỗi, instance component đã kích hoạt lỗi, và chuỗi thông tin chỉ định loại nguồn lỗi.

  :::tip
  Trong production, đối số thứ 3 (`info`) sẽ là mã rút gọn thay vì chuỗi thông tin đầy đủ. Bạn có thể tra cứu bảng ánh xạ mã trong [Tài liệu tra cứu mã lỗi Production](/error-reference/#runtime-errors).
  :::

  Bạn có thể thay đổi state component trong `onErrorCaptured()` để hiển thị trạng thái lỗi cho người dùng. Tuy nhiên, điều quan trọng là trạng thái lỗi không nên render nội dung gốc gây ra lỗi; nếu không component sẽ rơi vào vòng lặp render vô hạn.

  Hook có thể trả về `false` để ngăn lỗi tiếp tục truyền lên. Xem chi tiết về quy tắc truyền lỗi bên dưới.

  **Quy tắc Truyền Lỗi**

  - Mặc định, tất cả lỗi vẫn được gửi đến [`app.config.errorHandler`](/api/application#app-config-errorhandler) ở cấp ứng dụng nếu nó được định nghĩa, để các lỗi này vẫn có thể được báo cáo đến dịch vụ phân tích ở một nơi duy nhất.

  - Nếu có nhiều hook `errorCaptured` tồn tại trên chuỗi kế thừa hoặc chuỗi cha của component, tất cả chúng sẽ được gọi cho cùng một lỗi, theo thứ tự từ dưới lên trên. Điều này tương tự cơ chế nổi bọt của sự kiện DOM native.

  - Nếu chính hook `errorCaptured` ném một lỗi, cả lỗi này và lỗi được bắt ban đầu đều được gửi đến `app.config.errorHandler`.

  - Một hook `errorCaptured` có thể trả về `false` để ngăn lỗi tiếp tục truyền lên. Về cơ bản điều này nói "lỗi này đã được xử lý và nên bỏ qua." Nó sẽ ngăn bất kỳ hook `errorCaptured` bổ sung hay `app.config.errorHandler` nào được gọi cho lỗi này.

## onRenderTracked() <sup class="vt-badge dev-only" /> {#onrendertracked}

Đăng ký debug hook được gọi khi một reactive dependency đã được theo dõi bởi render effect của component.

**Hook này chỉ dành cho chế độ development và không được gọi trong quá trình kết xuất phía máy chủ.**

- **Kiểu**

  ```ts
  function onRenderTracked(callback: DebuggerHook): void

  type DebuggerHook = (e: DebuggerEvent) => void

  type DebuggerEvent = {
    effect: ReactiveEffect
    target: object
    type: TrackOpTypes /* 'get' | 'has' | 'iterate' */
    key: any
  }
  ```

- **Xem thêm** [Tính Phản ứng Chuyên sâu](/guide/extras/reactivity-in-depth)

## onRenderTriggered() <sup class="vt-badge dev-only" /> {#onrendertriggered}

Đăng ký debug hook được gọi khi một reactive dependency kích hoạt render effect của component chạy lại.

**Hook này chỉ dành cho chế độ development và không được gọi trong quá trình kết xuất phía máy chủ.**

- **Kiểu**

  ```ts
  function onRenderTriggered(callback: DebuggerHook): void

  type DebuggerHook = (e: DebuggerEvent) => void

  type DebuggerEvent = {
    effect: ReactiveEffect
    target: object
    type: TriggerOpTypes /* 'set' | 'add' | 'delete' | 'clear' */
    key: any
    newValue?: any
    oldValue?: any
    oldTarget?: Map<any, any> | Set<any>
  }
  ```

- **Xem thêm** [Tính Phản ứng Chuyên sâu](/guide/extras/reactivity-in-depth)

## onActivated() {#onactivated}

Đăng ký callback được gọi sau khi instance component được chèn vào DOM như một phần của cây được cache bởi [`<KeepAlive>`](/api/built-in-components#keepalive).

**Hook này không được gọi trong quá trình kết xuất phía máy chủ.**

- **Kiểu**

  ```ts
  function onActivated(callback: () => void, target?: ComponentInternalInstance | null): void
  ```

- **Xem thêm** [Hướng dẫn - Vòng đời của Instance Được Cache](/guide/built-ins/keep-alive#lifecycle-of-cached-instance)

## onDeactivated() {#ondeactivated}

Đăng ký callback được gọi sau khi instance component bị xóa khỏi DOM như một phần của cây được cache bởi [`<KeepAlive>`](/api/built-in-components#keepalive).

**Hook này không được gọi trong quá trình kết xuất phía máy chủ.**

- **Kiểu**

  ```ts
  function onDeactivated(callback: () => void, target?: ComponentInternalInstance | null): void
  ```

- **Xem thêm** [Hướng dẫn - Vòng đời của Instance Được Cache](/guide/built-ins/keep-alive#lifecycle-of-cached-instance)

## onServerPrefetch() <sup class="vt-badge" data-text="SSR only" /> {#onserverprefetch}

Đăng ký một hàm async được resolve trước khi instance component được render trên máy chủ.

- **Kiểu**

  ```ts
  function onServerPrefetch(callback: () => Promise<any>): void
  ```

- **Chi tiết**

  Nếu callback trả về một Promise, server renderer sẽ chờ cho đến khi Promise được resolve trước khi render component.

  Hook này chỉ được gọi trong quá trình kết xuất phía máy chủ và có thể dùng để thực hiện lấy dữ liệu chỉ ở phía server.

- **Ví dụ**

  ```vue
  <script setup>
  import { ref, onServerPrefetch, onMounted } from 'vue'

  const data = ref(null)

  onServerPrefetch(async () => {
    // component được render như một phần của request ban đầu
    // pre-fetch dữ liệu ở server vì nhanh hơn phía client
    data.value = await fetchOnServer(/* ... */)
  })

  onMounted(async () => {
    if (!data.value) {
      // nếu data là null khi mount, nghĩa là component
      // được render động ở phía client. Thực hiện
      // lấy dữ liệu phía client thay thế.
      data.value = await fetchOnClient(/* ... */)
    }
  })
  </script>
  ```

- **Xem thêm** [Kết xuất Phía Máy chủ](/guide/scaling-up/ssr)
