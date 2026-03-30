# Reactivity API: Core {#reactivity-api-core}

:::info Xem thêm
Để hiểu rõ hơn về Reactivity API, nên đọc các chương sau trong hướng dẫn:

- [Cơ bản về Tính Phản ứng](/guide/essentials/reactivity-fundamentals) (với tùy chọn API được đặt là Composition API)
- [Tính Phản ứng Chuyên sâu](/guide/extras/reactivity-in-depth)
  :::

## ref() {#ref}

Nhận một giá trị và trả về một ref object phản ứng có thể thay đổi, với một thuộc tính duy nhất `.value` trỏ đến giá trị bên trong.

- **Kiểu**

  ```ts
  function ref<T>(value: T): Ref<UnwrapRef<T>>

  interface Ref<T> {
    value: T
  }
  ```

- **Chi tiết**

  Ref object có thể thay đổi - tức là bạn có thể gán giá trị mới cho `.value`. Nó cũng có tính phản ứng - tức là mọi thao tác đọc `.value` đều được theo dõi, và thao tác ghi sẽ kích hoạt các effect liên quan.

  Nếu một object được gán làm giá trị của ref, object đó sẽ được làm phản ứng sâu với [reactive()](#reactive). Điều này cũng có nghĩa là nếu object chứa các ref lồng nhau, chúng sẽ được unwrap sâu.

  Để tránh chuyển đổi sâu, dùng [`shallowRef()`](./reactivity-advanced#shallowref) thay thế.

- **Ví dụ**

  ```js
  const count = ref(0)
  console.log(count.value) // 0

  count.value = 1
  console.log(count.value) // 1
  ```

- **Xem thêm**
  - [Hướng dẫn - Cơ bản về Tính Phản ứng với `ref()`](/guide/essentials/reactivity-fundamentals#ref)
  - [Hướng dẫn - Typing `ref()`](/guide/typescript/composition-api#typing-ref) <sup class="vt-badge ts" />

## computed() {#computed}

Nhận một [getter function](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/get#description) và trả về một [ref](#ref) phản ứng readonly cho giá trị được trả về từ getter. Nó cũng có thể nhận một object với các hàm `get` và `set` để tạo ra một ref object có thể ghi.

- **Kiểu**

  ```ts
  // read-only
  function computed<T>(
    getter: (oldValue: T | undefined) => T,
    // xem link "Computed Debugging" bên dưới
    debuggerOptions?: DebuggerOptions
  ): Readonly<Ref<Readonly<T>>>

  // writable
  function computed<T>(
    options: {
      get: (oldValue: T | undefined) => T
      set: (value: T) => void
    },
    debuggerOptions?: DebuggerOptions
  ): Ref<T>
  ```

- **Ví dụ**

  Tạo computed ref chỉ đọc:

  ```js
  const count = ref(1)
  const plusOne = computed(() => count.value + 1)

  console.log(plusOne.value) // 2

  plusOne.value++ // lỗi
  ```

  Tạo computed ref có thể ghi:

  ```js
  const count = ref(1)
  const plusOne = computed({
    get: () => count.value + 1,
    set: (val) => {
      count.value = val - 1
    }
  })

  plusOne.value = 1
  console.log(count.value) // 0
  ```

  Debug:

  ```js
  const plusOne = computed(() => count.value + 1, {
    onTrack(e) {
      debugger
    },
    onTrigger(e) {
      debugger
    }
  })
  ```

- **Xem thêm**
  - [Hướng dẫn - Thuộc tính Computed](/guide/essentials/computed)
  - [Hướng dẫn - Debug Computed](/guide/extras/reactivity-in-depth#computed-debugging)
  - [Hướng dẫn - Typing `computed()`](/guide/typescript/composition-api#typing-computed) <sup class="vt-badge ts" />
  - [Hướng dẫn - Hiệu suất - Tính ổn định của Computed](/guide/best-practices/performance#computed-stability)

## reactive() {#reactive}

Trả về một reactive proxy của object.

- **Kiểu**

  ```ts
  function reactive<T extends object>(target: T): UnwrapNestedRefs<T>
  ```

- **Chi tiết**

  Chuyển đổi reactive là "sâu": nó ảnh hưởng đến tất cả các thuộc tính lồng nhau. Một reactive object cũng unwrap sâu bất kỳ thuộc tính nào là [ref](#ref) trong khi vẫn duy trì tính phản ứng.

  Cần lưu ý rằng không có unwrap ref nào được thực hiện khi ref được truy cập như một phần tử của reactive array hoặc kiểu collection gốc như `Map`.

  Để tránh chuyển đổi sâu và chỉ giữ tính phản ứng ở cấp gốc, dùng [shallowReactive()](./reactivity-advanced#shallowreactive) thay thế.

  Object được trả về và các object lồng nhau của nó được bọc bởi [ES Proxy](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy) và **không** bằng với các object gốc. Khuyến nghị làm việc hoàn toàn với reactive proxy và tránh phụ thuộc vào object gốc.

- **Ví dụ**

  Tạo một reactive object:

  ```js
  const obj = reactive({ count: 0 })
  obj.count++
  ```

  Unwrap ref:

  ```ts
  const count = ref(1)
  const obj = reactive({ count })

  // ref sẽ được unwrap
  console.log(obj.count === count.value) // true

  // sẽ cập nhật `obj.count`
  count.value++
  console.log(count.value) // 2
  console.log(obj.count) // 2

  // cũng sẽ cập nhật ref `count`
  obj.count++
  console.log(obj.count) // 3
  console.log(count.value) // 3
  ```

  Lưu ý các ref **không** được unwrap khi truy cập dưới dạng phần tử array hoặc collection:

  ```js
  const books = reactive([ref('Vue 3 Guide')])
  // cần .value ở đây
  console.log(books[0].value)

  const map = reactive(new Map([['count', ref(0)]]))
  // cần .value ở đây
  console.log(map.get('count').value)
  ```

  Khi gán một [ref](#ref) cho thuộc tính `reactive`, ref đó cũng sẽ được tự động unwrap:

  ```ts
  const count = ref(1)
  const obj = reactive({})

  obj.count = count

  console.log(obj.count) // 1
  console.log(obj.count === count.value) // true
  ```

- **Xem thêm**
  - [Hướng dẫn - Cơ bản về Tính Phản ứng](/guide/essentials/reactivity-fundamentals)
  - [Hướng dẫn - Typing `reactive()`](/guide/typescript/composition-api#typing-reactive) <sup class="vt-badge ts" />

## readonly() {#readonly}

Nhận một object (reactive hoặc plain) hoặc một [ref](#ref) và trả về một readonly proxy đến bản gốc.

- **Kiểu**

  ```ts
  function readonly<T extends object>(
    target: T
  ): DeepReadonly<UnwrapNestedRefs<T>>
  ```

- **Chi tiết**

  Readonly proxy là sâu: bất kỳ thuộc tính lồng nhau nào được truy cập cũng sẽ là readonly. Nó cũng có hành vi unwrap ref giống như `reactive()`, ngoại trừ các giá trị được unwrap cũng sẽ được làm readonly.

  Để tránh chuyển đổi sâu, dùng [shallowReadonly()](./reactivity-advanced#shallowreadonly) thay thế.

- **Ví dụ**

  ```js
  const original = reactive({ count: 0 })

  const copy = readonly(original)

  watchEffect(() => {
    // hoạt động để theo dõi tính phản ứng
    console.log(copy.count)
  })

  // thay đổi original sẽ kích hoạt watcher dựa trên copy
  original.count++

  // thay đổi copy sẽ thất bại và dẫn đến cảnh báo
  copy.count++ // cảnh báo!
  ```

## watchEffect() {#watcheffect}

Chạy một hàm ngay lập tức trong khi theo dõi reactive các dependency của nó và chạy lại khi các dependency thay đổi.

- **Kiểu**

  ```ts
  function watchEffect(
    effect: (onCleanup: OnCleanup) => void,
    options?: WatchEffectOptions
  ): WatchHandle

  type OnCleanup = (cleanupFn: () => void) => void

  interface WatchEffectOptions {
    flush?: 'pre' | 'post' | 'sync' // mặc định: 'pre'
    onTrack?: (event: DebuggerEvent) => void
    onTrigger?: (event: DebuggerEvent) => void
  }

  interface WatchHandle {
    (): void // có thể gọi, giống như `stop`
    pause: () => void
    resume: () => void
    stop: () => void
  }
  ```

- **Chi tiết**

  Đối số đầu tiên là hàm effect cần chạy. Hàm effect nhận một hàm có thể dùng để đăng ký cleanup callback. Cleanup callback sẽ được gọi ngay trước lần chạy lại tiếp theo của effect, và có thể dùng để dọn dẹp các side effect đã bị vô hiệu hóa, ví dụ: một async request đang chờ (xem ví dụ bên dưới).

  Đối số thứ hai là object tùy chọn tùy chọn có thể dùng để điều chỉnh thời điểm flush của effect hoặc để debug các dependency của effect.

  Mặc định, watcher sẽ chạy ngay trước khi component render. Đặt `flush: 'post'` sẽ trì hoãn watcher cho đến sau khi component render. Xem [Thời điểm Flush Callback](/guide/essentials/watchers#callback-flush-timing) để biết thêm thông tin. Trong một số trường hợp hiếm, có thể cần kích hoạt watcher ngay khi reactive dependency thay đổi, ví dụ như để làm vô hiệu hóa cache. Điều này có thể đạt được bằng `flush: 'sync'`. Tuy nhiên, cài đặt này nên dùng thận trọng vì có thể dẫn đến vấn đề về hiệu suất và tính nhất quán dữ liệu nếu nhiều thuộc tính được cập nhật cùng lúc.

  Giá trị trả về là một hàm handle có thể gọi để dừng effect khỏi chạy lại.

- **Ví dụ**

  ```js
  const count = ref(0)

  watchEffect(() => console.log(count.value))
  // -> in 0

  count.value++
  // -> in 1
  ```

  Dừng watcher:

  ```js
  const stop = watchEffect(() => {})

  // khi watcher không còn cần thiết:
  stop()
  ```

  Tạm dừng / tiếp tục watcher: <sup class="vt-badge" data-text="3.5+" />

  ```js
  const { stop, pause, resume } = watchEffect(() => {})

  // tạm dừng watcher
  pause()

  // tiếp tục sau
  resume()

  // dừng
  stop()
  ```

  Dọn dẹp side effect:

  ```js
  watchEffect(async (onCleanup) => {
    const { response, cancel } = doAsyncWork(newId)
    // `cancel` sẽ được gọi nếu `id` thay đổi, hủy
    // request trước đó nếu nó chưa hoàn thành
    onCleanup(cancel)
    data.value = await response
  })
  ```

  Dọn dẹp side effect trong 3.5+:

  ```js
  import { onWatcherCleanup } from 'vue'

  watchEffect(async () => {
    const { response, cancel } = doAsyncWork(newId)
    // `cancel` sẽ được gọi nếu `id` thay đổi, hủy
    // request trước đó nếu nó chưa hoàn thành
    onWatcherCleanup(cancel)
    data.value = await response
  })
  ```

  Tùy chọn:

  ```js
  watchEffect(() => {}, {
    flush: 'post',
    onTrack(e) {
      debugger
    },
    onTrigger(e) {
      debugger
    }
  })
  ```

- **Xem thêm**
  - [Hướng dẫn - Watcher](/guide/essentials/watchers#watcheffect)
  - [Hướng dẫn - Debug Watcher](/guide/extras/reactivity-in-depth#watcher-debugging)

## watchPostEffect() {#watchposteffect}

Bí danh của [`watchEffect()`](#watcheffect) với tùy chọn `flush: 'post'`.

## watchSyncEffect() {#watchsynceffect}

Bí danh của [`watchEffect()`](#watcheffect) với tùy chọn `flush: 'sync'`.

## watch() {#watch}

Theo dõi một hoặc nhiều nguồn dữ liệu reactive và gọi một hàm callback khi các nguồn thay đổi.

- **Kiểu**

  ```ts
  // theo dõi nguồn đơn
  function watch<T>(
    source: WatchSource<T>,
    callback: WatchCallback<T>,
    options?: WatchOptions
  ): WatchHandle

  // theo dõi nhiều nguồn
  function watch<T>(
    sources: WatchSource<T>[],
    callback: WatchCallback<T[]>,
    options?: WatchOptions
  ): WatchHandle

  type WatchCallback<T> = (
    value: T,
    oldValue: T,
    onCleanup: (cleanupFn: () => void) => void
  ) => void

  type WatchSource<T> =
    | Ref<T> // ref
    | (() => T) // getter
    | (T extends object ? T : never) // reactive object

  interface WatchOptions extends WatchEffectOptions {
    immediate?: boolean // mặc định: false
    deep?: boolean | number // mặc định: false
    flush?: 'pre' | 'post' | 'sync' // mặc định: 'pre'
    onTrack?: (event: DebuggerEvent) => void
    onTrigger?: (event: DebuggerEvent) => void
    once?: boolean // mặc định: false (3.4+)
  }

  interface WatchHandle {
    (): void // có thể gọi, giống như `stop`
    pause: () => void
    resume: () => void
    stop: () => void
  }
  ```

  > Kiểu được đơn giản hóa để dễ đọc.

- **Chi tiết**

  `watch()` là lazy theo mặc định - tức là callback chỉ được gọi khi nguồn được theo dõi thay đổi.

  Đối số đầu tiên là **nguồn** của watcher. Nguồn có thể là một trong các dạng sau:

  - Hàm getter trả về một giá trị
  - Một ref
  - Một reactive object
  - ...hoặc một mảng các dạng trên.

  Đối số thứ hai là callback sẽ được gọi khi nguồn thay đổi. Callback nhận ba đối số: giá trị mới, giá trị cũ, và hàm để đăng ký side effect cleanup callback. Cleanup callback sẽ được gọi ngay trước lần chạy lại tiếp theo của effect, và có thể dùng để dọn dẹp các side effect đã bị vô hiệu hóa, ví dụ: một async request đang chờ.

  Khi theo dõi nhiều nguồn, callback nhận hai mảng chứa giá trị mới/cũ tương ứng với mảng nguồn.

  Đối số thứ ba tùy chọn là object tùy chọn hỗ trợ các tùy chọn sau:

  - **`immediate`**: kích hoạt callback ngay khi watcher được tạo. Giá trị cũ sẽ là `undefined` trong lần gọi đầu tiên.
  - **`deep`**: buộc duyệt sâu nguồn nếu là object, để callback kích hoạt trên các thay đổi sâu. Trong 3.5+, cũng có thể là số chỉ định độ sâu duyệt tối đa. Xem [Deep Watcher](/guide/essentials/watchers#deep-watchers).
  - **`flush`**: điều chỉnh thời điểm flush của callback. Xem [Thời điểm Flush Callback](/guide/essentials/watchers#callback-flush-timing) và [`watchEffect()`](/api/reactivity-core#watcheffect).
  - **`onTrack / onTrigger`**: debug các dependency của watcher. Xem [Debug Watcher](/guide/extras/reactivity-in-depth#watcher-debugging).
  - **`once`**: (3.4+) chỉ chạy callback một lần. Watcher tự động dừng sau lần callback đầu tiên chạy.

  So với [`watchEffect()`](#watcheffect), `watch()` cho phép chúng ta:

  - Thực hiện side effect một cách lazy;
  - Chỉ định rõ ràng hơn state nào sẽ kích hoạt watcher chạy lại;
  - Truy cập cả giá trị trước và hiện tại của state được theo dõi.

- **Ví dụ**

  Theo dõi một getter:

  ```js
  const state = reactive({ count: 0 })
  watch(
    () => state.count,
    (count, prevCount) => {
      /* ... */
    }
  )
  ```

  Theo dõi một ref:

  ```js
  const count = ref(0)
  watch(count, (count, prevCount) => {
    /* ... */
  })
  ```

  Khi theo dõi nhiều nguồn, callback nhận các mảng chứa giá trị mới/cũ tương ứng với mảng nguồn:

  ```js
  watch([fooRef, barRef], ([foo, bar], [prevFoo, prevBar]) => {
    /* ... */
  })
  ```

  Khi dùng nguồn getter, watcher chỉ kích hoạt nếu giá trị trả về của getter thay đổi. Nếu muốn callback kích hoạt ngay cả khi có thay đổi sâu, cần buộc watcher vào chế độ deep với `{ deep: true }`. Lưu ý trong chế độ deep, giá trị mới và cũ sẽ là cùng một object nếu callback được kích hoạt bởi thay đổi sâu:

  ```js
  const state = reactive({ count: 0 })
  watch(
    () => state,
    (newValue, oldValue) => {
      // newValue === oldValue
    },
    { deep: true }
  )
  ```

  Khi theo dõi trực tiếp một reactive object, watcher tự động ở chế độ deep:

  ```js
  const state = reactive({ count: 0 })
  watch(state, () => {
    /* kích hoạt khi thay đổi sâu trong state */
  })
  ```

  `watch()` chia sẻ cùng thời điểm flush và tùy chọn debug với [`watchEffect()`](#watcheffect):

  ```js
  watch(source, callback, {
    flush: 'post',
    onTrack(e) {
      debugger
    },
    onTrigger(e) {
      debugger
    }
  })
  ```

  Dừng watcher:

  ```js
  const stop = watch(source, callback)

  // khi watcher không còn cần thiết:
  stop()
  ```

  Tạm dừng / tiếp tục watcher: <sup class="vt-badge" data-text="3.5+" />

  ```js
  const { stop, pause, resume } = watch(() => {})

  // tạm dừng watcher
  pause()

  // tiếp tục sau
  resume()

  // dừng
  stop()
  ```

  Dọn dẹp side effect:

  ```js
  watch(id, async (newId, oldId, onCleanup) => {
    const { response, cancel } = doAsyncWork(newId)
    // `cancel` sẽ được gọi nếu `id` thay đổi, hủy
    // request trước đó nếu nó chưa hoàn thành
    onCleanup(cancel)
    data.value = await response
  })
  ```

  Dọn dẹp side effect trong 3.5+:

  ```js
  import { onWatcherCleanup } from 'vue'

  watch(id, async (newId) => {
    const { response, cancel } = doAsyncWork(newId)
    onWatcherCleanup(cancel)
    data.value = await response
  })
  ```

- **Xem thêm**

  - [Hướng dẫn - Watcher](/guide/essentials/watchers)
  - [Hướng dẫn - Debug Watcher](/guide/extras/reactivity-in-depth#watcher-debugging)

## onWatcherCleanup() <sup class="vt-badge" data-text="3.5+" /> {#onwatchercleanup}

Đăng ký một hàm cleanup để thực thi khi watcher hiện tại sắp chạy lại. Chỉ có thể gọi trong quá trình thực thi đồng bộ của hàm effect `watchEffect` hoặc hàm callback `watch` (tức là không thể gọi sau câu lệnh `await` trong hàm async).

- **Kiểu**

  ```ts
  function onWatcherCleanup(
    cleanupFn: () => void,
    failSilently?: boolean
  ): void
  ```

- **Ví dụ**

  ```ts
  import { watch, onWatcherCleanup } from 'vue'

  watch(id, (newId) => {
    const { response, cancel } = doAsyncWork(newId)
    // `cancel` sẽ được gọi nếu `id` thay đổi, hủy
    // request trước đó nếu nó chưa hoàn thành
    onWatcherCleanup(cancel)
  })
  ```
