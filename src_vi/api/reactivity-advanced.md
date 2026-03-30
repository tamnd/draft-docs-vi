# Reactivity API: Advanced {#reactivity-api-advanced}

## shallowRef() {#shallowref}

Phiên bản shallow của [`ref()`](./reactivity-core#ref).

- **Kiểu**

  ```ts
  function shallowRef<T>(value: T): ShallowRef<T>

  interface ShallowRef<T> {
    value: T
  }
  ```

- **Chi tiết**

  Khác với `ref()`, giá trị bên trong của shallow ref được lưu trữ và truy cập nguyên vẹn, và sẽ không được làm phản ứng sâu. Chỉ có truy cập `.value` là reactive.

  `shallowRef()` thường được dùng để tối ưu hiệu suất cho các cấu trúc dữ liệu lớn, hoặc tích hợp với các hệ thống quản lý state bên ngoài.

- **Ví dụ**

  ```js
  const state = shallowRef({ count: 1 })

  // KHÔNG kích hoạt thay đổi
  state.value.count = 2

  // kích hoạt thay đổi
  state.value = { count: 2 }
  ```

- **Xem thêm**
  - [Hướng dẫn - Giảm Overhead Reactivity cho Cấu trúc Bất biến Lớn](/guide/best-practices/performance#reduce-reactivity-overhead-for-large-immutable-structures)
  - [Hướng dẫn - Tích hợp với Hệ thống State Bên ngoài](/guide/extras/reactivity-in-depth#integration-with-external-state-systems)

## triggerRef() {#triggerref}

Buộc kích hoạt các effect phụ thuộc vào một [shallow ref](#shallowref). Thường được dùng sau khi thực hiện các thay đổi sâu cho giá trị bên trong của shallow ref.

- **Kiểu**

  ```ts
  function triggerRef(ref: ShallowRef): void
  ```

- **Ví dụ**

  ```js
  const shallow = shallowRef({
    greet: 'Hello, world'
  })

  // In "Hello, world" một lần trong lần chạy đầu tiên
  watchEffect(() => {
    console.log(shallow.value.greet)
  })

  // Điều này sẽ không kích hoạt effect vì ref là shallow
  shallow.value.greet = 'Hello, universe'

  // In "Hello, universe"
  triggerRef(shallow)
  ```

## customRef() {#customref}

Tạo một ref tùy chỉnh với quyền kiểm soát rõ ràng việc theo dõi dependency và kích hoạt cập nhật của nó.

- **Kiểu**

  ```ts
  function customRef<T>(factory: CustomRefFactory<T>): Ref<T>

  type CustomRefFactory<T> = (
    track: () => void,
    trigger: () => void
  ) => {
    get: () => T
    set: (value: T) => void
  }
  ```

- **Chi tiết**

  `customRef()` nhận một factory function, nhận các hàm `track` và `trigger` làm đối số và nên trả về một object với các method `get` và `set`.

  Thông thường, `track()` nên được gọi bên trong `get()`, và `trigger()` nên được gọi bên trong `set()`. Tuy nhiên, bạn có toàn quyền kiểm soát khi nào chúng nên được gọi, hoặc có nên gọi hay không.

- **Ví dụ**

  Tạo một debounced ref chỉ cập nhật giá trị sau một khoảng thời gian nhất định sau lần gọi set cuối cùng:

  ```js
  import { customRef } from 'vue'

  export function useDebouncedRef(value, delay = 200) {
    let timeout
    return customRef((track, trigger) => {
      return {
        get() {
          track()
          return value
        },
        set(newValue) {
          clearTimeout(timeout)
          timeout = setTimeout(() => {
            value = newValue
            trigger()
          }, delay)
        }
      }
    })
  }
  ```

  Dùng trong component:

  ```vue
  <script setup>
  import { useDebouncedRef } from './debouncedRef'
  const text = useDebouncedRef('hello')
  </script>

  <template>
    <input v-model="text" />
  </template>
  ```

  [Thử trong Playground](https://play.vuejs.org/#eNplUkFugzAQ/MqKC1SiIekxIpEq9QVV1BMXCguhBdsyaxqE/PcuGAhNfYGd3Z0ZDwzeq1K7zqB39OI205UiaJGMOieiapTUBAOYFt/wUxqRYf6OBVgotGzA30X5Bt59tX4iMilaAsIbwelxMfCvWNfSD+Gw3++fEhFHTpLFuCBsVJ0ScgUQjw6Az+VatY5PiroHo3IeaeHANlkrh7Qg1NBL43cILUmlMAfqVSXK40QUOSYmHAZHZO0KVkIZgu65kTnWp8Qb+4kHEXfjaDXkhd7DTTmuNZ7MsGyzDYbz5CgSgbdppOBFqqT4l0eX1gZDYOm057heOBQYRl81coZVg9LQWGr+IlrchYKAdJp9h0C6KkvUT3A6u8V1dq4ASqRgZnVnWg04/QWYNyYzC2rD5Y3/hkDgz8fY/cOT1ZjqizMZzGY3rDPC12KGZYyd3J26M8ny1KKx7c3X25q1c1wrZN3L9LCMWs/+AmeG6xI=)

  :::warning Dùng thận trọng
  Khi dùng customRef, nên thận trọng với giá trị trả về của getter, đặc biệt khi tạo kiểu dữ liệu object mới mỗi lần getter chạy. Điều này ảnh hưởng đến mối quan hệ giữa component cha và con, khi customRef như vậy được truyền làm prop.

  Hàm render của component cha có thể bị kích hoạt bởi các thay đổi của state reactive khác. Trong quá trình render lại, giá trị của customRef được tính lại, trả về kiểu dữ liệu object mới làm prop cho component con. Prop này được so sánh với giá trị trước đó trong component con, và vì chúng khác nhau, các reactive dependency của customRef sẽ bị kích hoạt trong component con. Trong khi đó, các reactive dependency trong component cha không chạy vì setter của customRef không được gọi, và do đó các dependency của nó cũng không bị kích hoạt.

  [Xem trong Playground](https://play.vuejs.org/#eNqFVEtP3DAQ/itTS9Vm1ZCt1J6WBZUiDvTQIsoNcwiOkzU4tmU7+9Aq/71jO1mCWuhlN/PyfPP45kAujCk2HSdLsnLMCuPBcd+Zc6pEa7T1cADWOa/bW17nYMPPtvRsDT3UVrcww+DZ0flStybpKSkWQQqPU0IVVUwr58FYvdvDWXgpu6ek1pqSHL0fS0vJw/z0xbN1jUPHY/Ys87Zkzzl4K5qG2zmcnUN2oAqg4T6bQ/wENKNXNk+CxWKsSlmLTSk7XlhedYxnWclYDiK+MkQCoK4wnVtnIiBJuuEJNA2qPof7hzkEoc8DXgg9yzYTBBFgNr4xyY4FbaK2p6qfI0iqFgtgulOe27HyQRy69Dk1JXY9C03JIeQ6wg4xWvJCqFpnlNytOcyC2wzYulQNr0Ao+Mhw0KnTTEttl/CIaIJiMz8NGBHFtYetVrPwa58/IL48Zag4N0ssquNYLYBoW16J0vOkC3VQtVqk7cG9QcHz1kj0QAlgVYkNMFk6d0bJ1pbGYKUkmtD42HmvFfi94WhOEiXwjUnBnlEz9OLTJwy5qCo44D4O7en71SIFjI/F9VuG4jEy/GHQKq5hQrJAKOc4uNVighBF5/cygS0GgOMoK+HQb7+EWvLdMM7weVIJy5kXWi0Rj+xaNRhLKRp1IvB9hxYegA6WJ1xkUe9PcF4e9a+suA3YwYiC5MQ79KlFUzw5rZCZEUtoRWuE5PaXCXmxtuWIkpJSSr39EXXHQcWYNWfP/9A/uV3QUXJjueN2E1ZhtPnSIqGS+er3T77D76Ox1VUn0fsd4y3HfewCxuT2vVMVwp74RbTX8WQI1dy5qx12xI1Fpa1K5AreeEHCCN8q/QXul+LrSC3s4nh93jltkVPDIYt5KJkcIKStCReo4rVQ/CZI6dyEzToCCJu7hAtry/1QH/qXncQB400KJwqPxZHxEyona0xS/E3rt1m9Ld1rZl+uhaxecRtP3EjtgddCyimtXyj9H/Ii3eId7uOGTkyk/wOEbQ9h)

  :::

## shallowReactive() {#shallowreactive}

Phiên bản shallow của [`reactive()`](./reactivity-core#reactive).

- **Kiểu**

  ```ts
  function shallowReactive<T extends object>(target: T): T
  ```

- **Chi tiết**

  Khác với `reactive()`, không có chuyển đổi sâu: chỉ các thuộc tính cấp gốc mới là reactive cho một shallow reactive object. Giá trị thuộc tính được lưu trữ và truy cập nguyên vẹn - điều này cũng có nghĩa là các thuộc tính có giá trị ref sẽ **không** được tự động unwrap.

  :::warning Dùng thận trọng
  Cấu trúc dữ liệu shallow chỉ nên dùng cho state cấp gốc trong component. Tránh lồng nó vào trong một reactive object sâu vì tạo ra cây với hành vi reactivity không nhất quán, khó hiểu và debug.
  :::

- **Ví dụ**

  ```js
  const state = shallowReactive({
    foo: 1,
    nested: {
      bar: 2
    }
  })

  // thay đổi thuộc tính riêng của state là reactive
  state.foo++

  // ...nhưng không chuyển đổi các object lồng nhau
  isReactive(state.nested) // false

  // KHÔNG reactive
  state.nested.bar++
  ```

## shallowReadonly() {#shallowreadonly}

Phiên bản shallow của [`readonly()`](./reactivity-core#readonly).

- **Kiểu**

  ```ts
  function shallowReadonly<T extends object>(target: T): Readonly<T>
  ```

- **Chi tiết**

  Khác với `readonly()`, không có chuyển đổi sâu: chỉ các thuộc tính cấp gốc mới được làm readonly. Giá trị thuộc tính được lưu trữ và truy cập nguyên vẹn - điều này cũng có nghĩa là các thuộc tính có giá trị ref sẽ **không** được tự động unwrap.

  :::warning Dùng thận trọng
  Cấu trúc dữ liệu shallow chỉ nên dùng cho state cấp gốc trong component. Tránh lồng nó vào trong một reactive object sâu vì tạo ra cây với hành vi reactivity không nhất quán, khó hiểu và debug.
  :::

- **Ví dụ**

  ```js
  const state = shallowReadonly({
    foo: 1,
    nested: {
      bar: 2
    }
  })

  // thay đổi thuộc tính riêng của state sẽ thất bại
  state.foo++

  // ...nhưng hoạt động với các object lồng nhau
  isReadonly(state.nested) // false

  // hoạt động
  state.nested.bar++
  ```

## toRaw() {#toraw}

Trả về object gốc, nguyên bản của một proxy do Vue tạo ra.

- **Kiểu**

  ```ts
  function toRaw<T>(proxy: T): T
  ```

- **Chi tiết**

  `toRaw()` có thể trả về object gốc từ các proxy được tạo bởi [`reactive()`](./reactivity-core#reactive), [`readonly()`](./reactivity-core#readonly), [`shallowReactive()`](#shallowreactive) hoặc [`shallowReadonly()`](#shallowreadonly).

  Đây là một lối thoát có thể dùng để tạm thời đọc mà không phát sinh overhead truy cập proxy / theo dõi hoặc ghi mà không kích hoạt thay đổi. **Không** khuyến nghị giữ một tham chiếu bền vững đến object gốc. Dùng thận trọng.

- **Ví dụ**

  ```js
  const foo = {}
  const reactiveFoo = reactive(foo)

  console.log(toRaw(reactiveFoo) === foo) // true
  ```

## markRaw() {#markraw}

Đánh dấu một object để nó sẽ không bao giờ được chuyển đổi thành proxy. Trả về chính object đó.

- **Kiểu**

  ```ts
  function markRaw<T extends object>(value: T): T
  ```

- **Ví dụ**

  ```js
  const foo = markRaw({})
  console.log(isReactive(reactive(foo))) // false

  // cũng hoạt động khi lồng bên trong các reactive object khác
  const bar = reactive({ foo })
  console.log(isReactive(bar.foo)) // false
  ```

  :::warning Dùng thận trọng
  `markRaw()` và các shallow API như `shallowReactive()` cho phép bạn chọn không dùng chuyển đổi reactive/readonly sâu mặc định và nhúng các object thô, không được proxy vào graph state của bạn. Chúng có thể dùng vì nhiều lý do:

  - Một số giá trị đơn giản không nên được làm reactive, ví dụ: instance class phức tạp của bên thứ ba, hoặc object component Vue.

  - Bỏ qua chuyển đổi proxy có thể cải thiện hiệu suất khi render các danh sách lớn với nguồn dữ liệu bất biến.

  Chúng được coi là nâng cao vì việc chọn không dùng raw chỉ ở cấp gốc, vì vậy nếu bạn đặt một nested, non-marked raw object vào reactive object rồi truy cập lại, bạn sẽ nhận được phiên bản proxied. Điều này có thể dẫn đến **identity hazard** - tức là thực hiện thao tác dựa trên định danh object nhưng dùng cả phiên bản raw và proxied của cùng một object:

  ```js
  const foo = markRaw({
    nested: {}
  })

  const bar = reactive({
    // mặc dù `foo` được đánh dấu raw, foo.nested thì không.
    nested: foo.nested
  })

  console.log(foo.nested === bar.nested) // false
  ```

  Identity hazard thường hiếm gặp. Tuy nhiên, để sử dụng đúng các API này trong khi tránh an toàn identity hazard cần hiểu vững cách hệ thống reactivity hoạt động.

  :::

## effectScope() {#effectscope}

Tạo một effect scope object có thể bắt các reactive effect (tức là computed và watcher) được tạo bên trong nó để các effect này có thể được hủy bỏ cùng nhau. Để biết các trường hợp sử dụng chi tiết của API này, vui lòng tham khảo [RFC](https://github.com/vuejs/rfcs/blob/master/active-rfcs/0041-reactivity-effect-scope.md) tương ứng.

- **Kiểu**

  ```ts
  function effectScope(detached?: boolean): EffectScope

  interface EffectScope {
    run<T>(fn: () => T): T | undefined // undefined nếu scope không hoạt động
    stop(): void
  }
  ```

- **Ví dụ**

  ```js
  const scope = effectScope()

  scope.run(() => {
    const doubled = computed(() => counter.value * 2)

    watch(doubled, () => console.log(doubled.value))

    watchEffect(() => console.log('Count: ', doubled.value))
  })

  // để hủy bỏ tất cả effect trong scope
  scope.stop()
  ```

## getCurrentScope() {#getcurrentscope}

Trả về [effect scope](#effectscope) đang hoạt động hiện tại nếu có.

- **Kiểu**

  ```ts
  function getCurrentScope(): EffectScope | undefined
  ```

## onScopeDispose() {#onscopedispose}

Đăng ký một dispose callback trên [effect scope](#effectscope) đang hoạt động hiện tại. Callback sẽ được gọi khi effect scope liên quan bị dừng.

Method này có thể dùng như một thay thế cho `onUnmounted` không gắn với component trong các hàm composable có thể tái sử dụng, vì hàm `setup()` của mỗi Vue component cũng được gọi trong một effect scope.

Một cảnh báo sẽ được ném ra nếu hàm này được gọi mà không có effect scope đang hoạt động. Trong 3.5+, cảnh báo này có thể được bỏ qua bằng cách truyền `true` làm đối số thứ hai.

- **Kiểu**

  ```ts
  function onScopeDispose(fn: () => void, failSilently?: boolean): void
  ```
