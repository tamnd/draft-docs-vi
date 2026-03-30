# Component Instance {#component-instance}

:::info
Trang này ghi lại các thuộc tính và phương thức dựng sẵn được expose trên instance công khai của component, tức là `this`.

Tất cả thuộc tính liệt kê trên trang này đều là readonly (ngoại trừ các thuộc tính lồng nhau trong `$data`).
:::

## $data {#data}

Object được trả về từ tùy chọn [`data`](./options-state#data), được component làm cho có tính phản ứng. Instance component ủy quyền truy cập đến các thuộc tính trên data object của nó.

- **Kiểu**

  ```ts
  interface ComponentPublicInstance {
    $data: object
  }
  ```

## $props {#props}

Một object đại diện cho các prop hiện tại đã được resolve của component.

- **Kiểu**

  ```ts
  interface ComponentPublicInstance {
    $props: object
  }
  ```

- **Chi tiết**

  Chỉ các prop được khai báo qua tùy chọn [`props`](./options-state#props) mới được đưa vào. Instance component ủy quyền truy cập đến các thuộc tính trên props object của nó.

## $el {#el}

DOM node gốc mà instance component đang quản lý.

- **Kiểu**

  ```ts
  interface ComponentPublicInstance {
    $el: any
  }
  ```

- **Chi tiết**

  `$el` sẽ là `undefined` cho đến khi component được [mount](./options-lifecycle#mounted).

  - Với component có một element gốc đơn, `$el` sẽ trỏ đến element đó.
  - Với component có text gốc, `$el` sẽ trỏ đến text node.
  - Với component có nhiều node gốc, `$el` sẽ là DOM node placeholder mà Vue dùng để theo dõi vị trí của component trong DOM (một text node, hoặc comment node ở chế độ SSR hydration).

  :::tip
  Để nhất quán, nên dùng [template ref](/guide/essentials/template-refs) để truy cập trực tiếp vào element thay vì dựa vào `$el`.
  :::

## $options {#options}

Các tùy chọn component đã được resolve dùng để khởi tạo instance component hiện tại.

- **Kiểu**

  ```ts
  interface ComponentPublicInstance {
    $options: ComponentOptions
  }
  ```

- **Chi tiết**

  Object `$options` expose các tùy chọn đã được resolve cho component hiện tại và là kết quả hợp nhất từ các nguồn có thể có:

  - Global mixin
  - Base `extends` của component
  - Mixin của component

  Thường được dùng để hỗ trợ các tùy chọn component tùy chỉnh:

  ```js
  const app = createApp({
    customOption: 'foo',
    created() {
      console.log(this.$options.customOption) // => 'foo'
    }
  })
  ```

- **Xem thêm** [`app.config.optionMergeStrategies`](/api/application#app-config-optionmergestrategies)

## $parent {#parent}

Instance cha, nếu instance hiện tại có cha. Sẽ là `null` cho chính instance gốc.

- **Kiểu**

  ```ts
  interface ComponentPublicInstance {
    $parent: ComponentPublicInstance | null
  }
  ```

## $root {#root}

Instance component gốc của cây component hiện tại. Nếu instance hiện tại không có cha, giá trị này sẽ là chính nó.

- **Kiểu**

  ```ts
  interface ComponentPublicInstance {
    $root: ComponentPublicInstance
  }
  ```

## $slots {#slots}

Một object đại diện cho các [slot](/guide/components/slots) được truyền bởi component cha.

- **Kiểu**

  ```ts
  interface ComponentPublicInstance {
    $slots: { [name: string]: Slot }
  }

  type Slot = (...args: any[]) => VNode[]
  ```

- **Chi tiết**

  Thường được dùng khi viết thủ công [render function](/guide/extras/render-function), nhưng cũng có thể dùng để phát hiện xem một slot có hiện diện không.

  Mỗi slot được expose trên `this.$slots` như một hàm trả về mảng vnode theo key tương ứng với tên slot đó. Default slot được expose là `this.$slots.default`.

  Nếu một slot là [scoped slot](/guide/components/slots#scoped-slots), các đối số truyền vào hàm slot có sẵn trong slot như slot props của nó.

- **Xem thêm** [Render Function - Render Slot](/guide/extras/render-function#rendering-slots)

## $refs {#refs}

Một object chứa các DOM element và instance component, được đăng ký qua [template ref](/guide/essentials/template-refs).

- **Kiểu**

  ```ts
  interface ComponentPublicInstance {
    $refs: { [name: string]: Element | ComponentPublicInstance | null }
  }
  ```

- **Xem thêm**

  - [Template ref](/guide/essentials/template-refs)
  - [Thuộc tính Đặc biệt - ref](./built-in-special-attributes.md#ref)

## $attrs {#attrs}

Một object chứa các thuộc tính kế thừa của component.

- **Kiểu**

  ```ts
  interface ComponentPublicInstance {
    $attrs: object
  }
  ```

- **Chi tiết**

  [Thuộc tính kế thừa](/guide/components/attrs) là các thuộc tính và event handler được truyền bởi component cha, nhưng không được khai báo là prop hay sự kiện được emit bởi component con.

  Mặc định, tất cả trong `$attrs` sẽ được tự động kế thừa trên element gốc của component nếu chỉ có một element gốc. Hành vi này bị tắt nếu component có nhiều node gốc, và có thể tắt rõ ràng bằng tùy chọn [`inheritAttrs`](./options-misc#inheritattrs).

- **Xem thêm**

  - [Thuộc tính Kế thừa](/guide/components/attrs)

## $watch() {#watch}

API mệnh lệnh (Imperative API) để tạo watcher.

- **Kiểu**

  ```ts
  interface ComponentPublicInstance {
    $watch(
      source: string | (() => any),
      callback: WatchCallback,
      options?: WatchOptions
    ): StopHandle
  }

  type WatchCallback<T> = (
    value: T,
    oldValue: T,
    onCleanup: (cleanupFn: () => void) => void
  ) => void

  interface WatchOptions {
    immediate?: boolean // mặc định: false
    deep?: boolean // mặc định: false
    flush?: 'pre' | 'post' | 'sync' // mặc định: 'pre'
    onTrack?: (event: DebuggerEvent) => void
    onTrigger?: (event: DebuggerEvent) => void
  }

  type StopHandle = () => void
  ```

- **Chi tiết**

  Đối số đầu tiên là nguồn watch. Nó có thể là chuỗi tên thuộc tính component, chuỗi đường dẫn phân cách bằng dấu chấm đơn giản, hoặc một [getter function](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/get#description).

  Đối số thứ hai là hàm callback. Callback nhận giá trị mới và giá trị cũ của nguồn được watch.

  - **`immediate`**: kích hoạt callback ngay lập tức khi tạo watcher. Giá trị cũ sẽ là `undefined` ở lần gọi đầu tiên.
  - **`deep`**: buộc duyệt sâu nguồn nếu nó là object, để callback được kích hoạt khi có thay đổi sâu. Xem [Deep Watcher](/guide/essentials/watchers#deep-watchers).
  - **`flush`**: điều chỉnh thời điểm flush của callback. Xem [Thời điểm Flush Callback](/guide/essentials/watchers#callback-flush-timing) và [`watchEffect()`](/api/reactivity-core#watcheffect).
  - **`onTrack / onTrigger`**: debug các dependency của watcher. Xem [Debug Watcher](/guide/extras/reactivity-in-depth#watcher-debugging).

- **Ví dụ**

  Watch tên thuộc tính:

  ```js
  this.$watch('a', (newVal, oldVal) => {})
  ```

  Watch đường dẫn phân cách bằng dấu chấm:

  ```js
  this.$watch('a.b', (newVal, oldVal) => {})
  ```

  Dùng getter cho các biểu thức phức tạp hơn:

  ```js
  this.$watch(
    // mỗi khi biểu thức `this.a + this.b` cho ra
    // kết quả khác nhau, handler sẽ được gọi.
    // Giống như ta đang watch một thuộc tính computed
    // mà không cần định nghĩa chính thuộc tính computed đó.
    () => this.a + this.b,
    (newVal, oldVal) => {}
  )
  ```

  Dừng watcher:

  ```js
  const unwatch = this.$watch('a', cb)

  // sau đó...
  unwatch()
  ```

- **Xem thêm**
  - [Tùy chọn - `watch`](/api/options-state#watch)
  - [Hướng dẫn - Watcher](/guide/essentials/watchers)

## $emit() {#emit}

Kích hoạt một custom event trên instance hiện tại. Bất kỳ đối số bổ sung nào sẽ được truyền vào hàm callback của listener.

- **Kiểu**

  ```ts
  interface ComponentPublicInstance {
    $emit(event: string, ...args: any[]): void
  }
  ```

- **Ví dụ**

  ```js
  export default {
    created() {
      // chỉ sự kiện
      this.$emit('foo')
      // với đối số bổ sung
      this.$emit('bar', 1, 2, 3)
    }
  }
  ```

- **Xem thêm**

  - [Component - Sự kiện](/guide/components/events)
  - [Tùy chọn `emits`](./options-state#emits)

## $forceUpdate() {#forceupdate}

Buộc instance component re-render.

- **Kiểu**

  ```ts
  interface ComponentPublicInstance {
    $forceUpdate(): void
  }
  ```

- **Chi tiết**

  Hiếm khi cần đến tính năng này khi đã có hệ thống tính phản ứng tự động hoàn toàn của Vue. Trường hợp duy nhất bạn có thể cần nó là khi bạn đã tạo rõ ràng state component không có tính phản ứng bằng các API tính phản ứng nâng cao.

## $nextTick() {#nexttick}

Phiên bản ràng buộc với instance của global [`nextTick()`](./general#nexttick).

- **Kiểu**

  ```ts
  interface ComponentPublicInstance {
    $nextTick(callback?: (this: ComponentPublicInstance) => void): Promise<void>
  }
  ```

- **Chi tiết**

  Điểm khác biệt duy nhất so với phiên bản global của `nextTick()` là callback truyền vào `this.$nextTick()` sẽ có context `this` được ràng buộc với instance component hiện tại.

- **Xem thêm** [`nextTick()`](./general#nexttick)
