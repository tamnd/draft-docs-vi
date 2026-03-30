# Options: Lifecycle {#options-lifecycle}

:::info Xem thêm
Để biết cách dùng chung của hook vòng đời, xem [Hướng dẫn - Hook Vòng đời](/guide/essentials/lifecycle)
:::

## beforeCreate {#beforecreate}

Được gọi khi instance được khởi tạo.

- **Kiểu**

  ```ts
  interface ComponentOptions {
    beforeCreate?(this: ComponentPublicInstance): void
  }
  ```

- **Chi tiết**

  Được gọi ngay khi instance được khởi tạo và props được resolve.

  Sau đó props sẽ được định nghĩa là các thuộc tính phản ứng và state như `data()` hoặc `computed` sẽ được thiết lập.

  Lưu ý hook `setup()` của Composition API được gọi trước bất kỳ hook Options API nào, kể cả `beforeCreate()`.

## created {#created}

Được gọi sau khi instance đã xử lý xong tất cả các option liên quan đến state.

- **Kiểu**

  ```ts
  interface ComponentOptions {
    created?(this: ComponentPublicInstance): void
  }
  ```

- **Chi tiết**

  Khi hook này được gọi, những thứ sau đã được thiết lập: data phản ứng, thuộc tính computed, method và watcher. Tuy nhiên, giai đoạn mount chưa bắt đầu và thuộc tính `$el` chưa có sẵn.

## beforeMount {#beforemount}

Được gọi ngay trước khi component được mount.

- **Kiểu**

  ```ts
  interface ComponentOptions {
    beforeMount?(this: ComponentPublicInstance): void
  }
  ```

- **Chi tiết**

  Khi hook này được gọi, component đã hoàn thành việc thiết lập state phản ứng, nhưng chưa có DOM node nào được tạo. Nó sắp thực thi render effect DOM lần đầu tiên.

  **Hook này không được gọi trong quá trình kết xuất phía máy chủ.**

## mounted {#mounted}

Được gọi sau khi component được mount.

- **Kiểu**

  ```ts
  interface ComponentOptions {
    mounted?(this: ComponentPublicInstance): void
  }
  ```

- **Chi tiết**

  Một component được coi là đã mount sau khi:

  - Tất cả component con đồng bộ của nó đã được mount (không bao gồm component async hoặc component bên trong cây `<Suspense>`).

  - Cây DOM của chính nó đã được tạo và chèn vào container cha. Lưu ý điều này chỉ đảm bảo rằng cây DOM của component trong document nếu container gốc của ứng dụng cũng trong document.

  Hook này thường được dùng để thực hiện các side effect cần truy cập DOM đã render của component, hoặc để giới hạn code liên quan đến DOM ở phía client trong [ứng dụng kết xuất phía máy chủ](/guide/scaling-up/ssr).

  **Hook này không được gọi trong quá trình kết xuất phía máy chủ.**

## beforeUpdate {#beforeupdate}

Được gọi ngay trước khi component sắp cập nhật cây DOM do thay đổi state phản ứng.

- **Kiểu**

  ```ts
  interface ComponentOptions {
    beforeUpdate?(this: ComponentPublicInstance): void
  }
  ```

- **Chi tiết**

  Hook này có thể dùng để truy cập trạng thái DOM trước khi Vue cập nhật DOM. Cũng an toàn khi thay đổi state component bên trong hook này.

  **Hook này không được gọi trong quá trình kết xuất phía máy chủ.**

## updated {#updated}

Được gọi sau khi component đã cập nhật cây DOM do thay đổi state phản ứng.

- **Kiểu**

  ```ts
  interface ComponentOptions {
    updated?(this: ComponentPublicInstance): void
  }
  ```

- **Chi tiết**

  Hook updated của component cha được gọi sau hook của các component con của nó.

  Hook này được gọi sau bất kỳ cập nhật DOM nào của component, có thể do các thay đổi state khác nhau. Nếu bạn cần truy cập DOM đã cập nhật sau một thay đổi state cụ thể, hãy dùng [nextTick()](/api/general#nexttick) thay thế.

  **Hook này không được gọi trong quá trình kết xuất phía máy chủ.**

  :::warning
  Đừng thay đổi state component trong hook updated - điều này có thể dẫn đến vòng lặp cập nhật vô hạn!
  :::

## beforeUnmount {#beforeunmount}

Được gọi ngay trước khi instance component sắp được unmount.

- **Kiểu**

  ```ts
  interface ComponentOptions {
    beforeUnmount?(this: ComponentPublicInstance): void
  }
  ```

- **Chi tiết**

  Khi hook này được gọi, instance component vẫn còn hoạt động hoàn toàn.

  **Hook này không được gọi trong quá trình kết xuất phía máy chủ.**

## unmounted {#unmounted}

Được gọi sau khi component được unmount.

- **Kiểu**

  ```ts
  interface ComponentOptions {
    unmounted?(this: ComponentPublicInstance): void
  }
  ```

- **Chi tiết**

  Một component được coi là đã unmount sau khi:

  - Tất cả component con của nó đã được unmount.

  - Tất cả reactive effect liên quan của nó (render effect và computed/watcher được tạo trong `setup()`) đã bị dừng.

  Dùng hook này để dọn dẹp các side effect được tạo thủ công như timer, DOM event listener hoặc kết nối server.

  **Hook này không được gọi trong quá trình kết xuất phía máy chủ.**

## errorCaptured {#errorcaptured}

Được gọi khi một lỗi truyền từ component con đã được bắt.

- **Kiểu**

  ```ts
  interface ComponentOptions {
    errorCaptured?(
      this: ComponentPublicInstance,
      err: unknown,
      instance: ComponentPublicInstance | null,
      info: string
    ): boolean | void
  }
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

  Bạn có thể thay đổi state component trong `errorCaptured()` để hiển thị trạng thái lỗi cho người dùng. Tuy nhiên, điều quan trọng là trạng thái lỗi không nên render nội dung gốc gây ra lỗi; nếu không component sẽ rơi vào vòng lặp render vô hạn.

  Hook có thể trả về `false` để ngăn lỗi tiếp tục truyền lên. Xem chi tiết về quy tắc truyền lỗi bên dưới.

  **Quy tắc Truyền Lỗi**

  - Mặc định, tất cả lỗi vẫn được gửi đến [`app.config.errorHandler`](/api/application#app-config-errorhandler) ở cấp ứng dụng nếu nó được định nghĩa, để các lỗi này vẫn có thể được báo cáo đến dịch vụ phân tích ở một nơi duy nhất.

  - Nếu có nhiều hook `errorCaptured` tồn tại trên chuỗi kế thừa hoặc chuỗi cha của component, tất cả chúng sẽ được gọi cho cùng một lỗi, theo thứ tự từ dưới lên trên. Điều này tương tự cơ chế nổi bọt của sự kiện DOM native.

  - Nếu chính hook `errorCaptured` ném một lỗi, cả lỗi này và lỗi được bắt ban đầu đều được gửi đến `app.config.errorHandler`.

  - Một hook `errorCaptured` có thể trả về `false` để ngăn lỗi tiếp tục truyền lên. Về cơ bản điều này nói "lỗi này đã được xử lý và nên bỏ qua." Nó sẽ ngăn bất kỳ hook `errorCaptured` bổ sung hay `app.config.errorHandler` nào được gọi cho lỗi này.

  **Lưu ý Bắt Lỗi**

  - Trong các component có hàm `setup()` async (có `await` cấp cao nhất), Vue **luôn luôn** cố render template component, ngay cả khi `setup()` ném lỗi. Điều này có thể gây thêm lỗi vì trong quá trình render, template component có thể cố truy cập các thuộc tính không tồn tại của context `setup()` bị lỗi. Khi bắt lỗi trong các component như vậy, hãy chuẩn bị xử lý lỗi từ cả `setup()` async bị lỗi (chúng luôn đến trước) lẫn quá trình render bị lỗi.

  - <sup class="vt-badge" data-text="SSR only"></sup> Thay thế component con bị lỗi trong component cha sâu bên trong `<Suspense>` sẽ gây sai lệch hydration trong SSR. Thay vào đó, hãy tách logic có thể ném lỗi từ `setup()` của component con vào hàm riêng biệt và thực thi nó trong `setup()` của component cha, nơi bạn có thể `try/catch` quá trình thực thi an toàn và thực hiện thay thế nếu cần trước khi render component con thực sự.

## renderTracked <sup class="vt-badge dev-only" /> {#rendertracked}

Được gọi khi một reactive dependency đã được theo dõi bởi render effect của component.

**Hook này chỉ dành cho chế độ development và không được gọi trong quá trình kết xuất phía máy chủ.**

- **Kiểu**

  ```ts
  interface ComponentOptions {
    renderTracked?(this: ComponentPublicInstance, e: DebuggerEvent): void
  }

  type DebuggerEvent = {
    effect: ReactiveEffect
    target: object
    type: TrackOpTypes /* 'get' | 'has' | 'iterate' */
    key: any
  }
  ```

- **Xem thêm** [Tính Phản ứng Chuyên sâu](/guide/extras/reactivity-in-depth)

## renderTriggered <sup class="vt-badge dev-only" /> {#rendertriggered}

Được gọi khi một reactive dependency kích hoạt render effect của component chạy lại.

**Hook này chỉ dành cho chế độ development và không được gọi trong quá trình kết xuất phía máy chủ.**

- **Kiểu**

  ```ts
  interface ComponentOptions {
    renderTriggered?(this: ComponentPublicInstance, e: DebuggerEvent): void
  }

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

## activated {#activated}

Được gọi sau khi instance component được chèn vào DOM như một phần của cây được cache bởi [`<KeepAlive>`](/api/built-in-components#keepalive).

**Hook này không được gọi trong quá trình kết xuất phía máy chủ.**

- **Kiểu**

  ```ts
  interface ComponentOptions {
    activated?(this: ComponentPublicInstance): void
  }
  ```

- **Xem thêm** [Hướng dẫn - Vòng đời của Instance Được Cache](/guide/built-ins/keep-alive#lifecycle-of-cached-instance)

## deactivated {#deactivated}

Được gọi sau khi instance component bị xóa khỏi DOM như một phần của cây được cache bởi [`<KeepAlive>`](/api/built-in-components#keepalive).

**Hook này không được gọi trong quá trình kết xuất phía máy chủ.**

- **Kiểu**

  ```ts
  interface ComponentOptions {
    deactivated?(this: ComponentPublicInstance): void
  }
  ```

- **Xem thêm** [Hướng dẫn - Vòng đời của Instance Được Cache](/guide/built-ins/keep-alive#lifecycle-of-cached-instance)

## serverPrefetch <sup class="vt-badge" data-text="SSR only" /> {#serverprefetch}

Hàm async được resolve trước khi instance component được render trên máy chủ.

- **Kiểu**

  ```ts
  interface ComponentOptions {
    serverPrefetch?(this: ComponentPublicInstance): Promise<any>
  }
  ```

- **Chi tiết**

  Nếu hook trả về Promise, server renderer sẽ chờ cho đến khi Promise được resolve trước khi render component.

  Hook này chỉ được gọi trong quá trình kết xuất phía máy chủ và có thể dùng để thực hiện lấy dữ liệu chỉ ở phía server.

- **Ví dụ**

  ```js
  export default {
    data() {
      return {
        data: null
      }
    },
    async serverPrefetch() {
      // component được render như một phần của request ban đầu
      // pre-fetch dữ liệu ở server vì nhanh hơn phía client
      this.data = await fetchOnServer(/* ... */)
    },
    async mounted() {
      if (!this.data) {
        // nếu data là null khi mount, nghĩa là component
        // được render động ở phía client. Thực hiện
        // lấy dữ liệu phía client thay thế.
        this.data = await fetchOnClient(/* ... */)
      }
    }
  }
  ```

- **Xem thêm** [Kết xuất Phía Máy chủ](/guide/scaling-up/ssr)
