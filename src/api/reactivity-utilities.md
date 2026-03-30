# Reactivity API: Utilities {#reactivity-api-utilities}

## isRef() {#isref}

Kiểm tra xem một giá trị có phải là ref object hay không.

- **Kiểu**

  ```ts
  function isRef<T>(r: Ref<T> | unknown): r is Ref<T>
  ```

  Lưu ý kiểu trả về là [type predicate](https://www.typescriptlang.org/docs/handbook/2/narrowing.html#using-type-predicates), nghĩa là `isRef` có thể dùng như type guard:

  ```ts
  let foo: unknown
  if (isRef(foo)) {
    // kiểu của foo được thu hẹp thành Ref<unknown>
    foo.value
  }
  ```

## unref() {#unref}

Trả về giá trị bên trong nếu đối số là ref, ngược lại trả về chính đối số đó. Đây là hàm đường tắt cho `val = isRef(val) ? val.value : val`.

- **Kiểu**

  ```ts
  function unref<T>(ref: T | Ref<T>): T
  ```

- **Ví dụ**

  ```ts
  function useFoo(x: number | Ref<number>) {
    const unwrapped = unref(x)
    // unwrapped được đảm bảo là number lúc này
  }
  ```

## toRef() {#toref}

Có thể dùng để chuẩn hóa các giá trị / ref / getter thành ref (3.3+).

Cũng có thể dùng để tạo một ref cho một thuộc tính trên reactive object nguồn. Ref được tạo ra sẽ đồng bộ với thuộc tính nguồn: thay đổi thuộc tính nguồn sẽ cập nhật ref, và ngược lại.

- **Kiểu**

  ```ts
  // signature chuẩn hóa (3.3+)
  function toRef<T>(
    value: T
  ): T extends () => infer R
    ? Readonly<Ref<R>>
    : T extends Ref
    ? T
    : Ref<UnwrapRef<T>>

  // signature thuộc tính object
  function toRef<T extends object, K extends keyof T>(
    object: T,
    key: K,
    defaultValue?: T[K]
  ): ToRef<T[K]>

  type ToRef<T> = T extends Ref ? T : Ref<T>
  ```

- **Ví dụ**

  Signature chuẩn hóa (3.3+):

  ```js
  // trả về ref hiện có nguyên vẹn
  toRef(existingRef)

  // tạo một readonly ref gọi getter khi truy cập .value
  toRef(() => props.foo)

  // tạo ref thông thường từ các giá trị không phải hàm
  // tương đương với ref(1)
  toRef(1)
  ```

  Signature thuộc tính object:

  ```js
  const state = reactive({
    foo: 1,
    bar: 2
  })

  // một ref hai chiều đồng bộ với thuộc tính gốc
  const fooRef = toRef(state, 'foo')

  // thay đổi ref cũng cập nhật bản gốc
  fooRef.value++
  console.log(state.foo) // 2

  // thay đổi bản gốc cũng cập nhật ref
  state.foo++
  console.log(fooRef.value) // 3
  ```

  Lưu ý đây khác với:

  ```js
  const fooRef = ref(state.foo)
  ```

  Ref trên **không** đồng bộ với `state.foo`, vì `ref()` nhận một giá trị number thông thường.

  `toRef()` hữu ích khi bạn muốn truyền ref của một prop vào một hàm composable:

  ```vue
  <script setup>
  import { toRef } from 'vue'

  const props = defineProps(/* ... */)

  // chuyển `props.foo` thành ref, rồi truyền vào
  // một composable
  useSomeFeature(toRef(props, 'foo'))

  // cú pháp getter - khuyến nghị trong 3.3+
  useSomeFeature(toRef(() => props.foo))
  </script>
  ```

  Khi `toRef` được dùng với props của component, các hạn chế thông thường về việc thay đổi props vẫn áp dụng. Cố gán giá trị mới cho ref tương đương với cố sửa đổi prop trực tiếp và không được phép. Trong trường hợp đó, bạn nên cân nhắc dùng [`computed`](./reactivity-core#computed) với `get` và `set` thay thế. Xem hướng dẫn [dùng `v-model` với component](/guide/components/v-model) để biết thêm thông tin.

  Khi dùng signature thuộc tính object, `toRef()` sẽ trả về một ref sử dụng được ngay cả khi thuộc tính nguồn chưa tồn tại. Điều này cho phép làm việc với các thuộc tính tùy chọn, vốn sẽ không được [`toRefs`](#torefs) xử lý.

## toValue() {#tovalue}

- Chỉ hỗ trợ từ 3.3+

Chuẩn hóa các giá trị / ref / getter thành giá trị. Tương tự như [unref()](#unref), ngoại trừ nó cũng chuẩn hóa getter. Nếu đối số là getter, nó sẽ được gọi và giá trị trả về sẽ được trả về.

Có thể dùng trong [Composable](/guide/reusability/composables.html) để chuẩn hóa một đối số có thể là giá trị, ref, hoặc getter.

- **Kiểu**

  ```ts
  function toValue<T>(source: T | Ref<T> | (() => T)): T
  ```

- **Ví dụ**

  ```js
  toValue(1) //       --> 1
  toValue(ref(1)) //  --> 1
  toValue(() => 1) // --> 1
  ```

  Chuẩn hóa đối số trong composable:

  ```ts
  import type { MaybeRefOrGetter } from 'vue'

  function useFeature(id: MaybeRefOrGetter<number>) {
    watch(() => toValue(id), id => {
      // phản ứng khi id thay đổi
    })
  }

  // composable này hỗ trợ tất cả các dạng sau:
  useFeature(1)
  useFeature(ref(1))
  useFeature(() => 1)
  ```

## toRefs() {#torefs}

Chuyển đổi một reactive object thành một plain object trong đó mỗi thuộc tính của object kết quả là một ref trỏ đến thuộc tính tương ứng của object gốc. Mỗi ref riêng lẻ được tạo bằng [`toRef()`](#toref).

- **Kiểu**

  ```ts
  function toRefs<T extends object>(
    object: T
  ): {
    [K in keyof T]: ToRef<T[K]>
  }

  type ToRef = T extends Ref ? T : Ref<T>
  ```

- **Ví dụ**

  ```js
  const state = reactive({
    foo: 1,
    bar: 2
  })

  const stateAsRefs = toRefs(state)
  /*
  Kiểu của stateAsRefs: {
    foo: Ref<number>,
    bar: Ref<number>
  }
  */

  // ref và thuộc tính gốc được "liên kết"
  state.foo++
  console.log(stateAsRefs.foo.value) // 2

  stateAsRefs.foo.value++
  console.log(state.foo) // 3
  ```

  `toRefs` hữu ích khi trả về một reactive object từ hàm composable để component sử dụng có thể destructure/spread object được trả về mà không mất tính phản ứng:

  ```js
  function useFeatureX() {
    const state = reactive({
      foo: 1,
      bar: 2
    })

    // ...logic hoạt động trên state

    // chuyển thành refs khi trả về
    return toRefs(state)
  }

  // có thể destructure mà không mất tính phản ứng
  const { foo, bar } = useFeatureX()
  ```

  `toRefs` chỉ tạo ref cho các thuộc tính có thể liệt kê trên object nguồn tại thời điểm gọi. Để tạo ref cho thuộc tính có thể chưa tồn tại, dùng [`toRef`](#toref) thay thế.

## isProxy() {#isproxy}

Kiểm tra xem một object có phải là proxy được tạo bởi [`reactive()`](./reactivity-core#reactive), [`readonly()`](./reactivity-core#readonly), [`shallowReactive()`](./reactivity-advanced#shallowreactive) hoặc [`shallowReadonly()`](./reactivity-advanced#shallowreadonly) hay không.

- **Kiểu**

  ```ts
  function isProxy(value: any): boolean
  ```

## isReactive() {#isreactive}

Kiểm tra xem một object có phải là proxy được tạo bởi [`reactive()`](./reactivity-core#reactive) hoặc [`shallowReactive()`](./reactivity-advanced#shallowreactive) hay không.

- **Kiểu**

  ```ts
  function isReactive(value: unknown): boolean
  ```

## isReadonly() {#isreadonly}

Kiểm tra xem giá trị được truyền vào có phải là object readonly hay không. Các thuộc tính của object readonly có thể thay đổi, nhưng không thể gán trực tiếp qua object được truyền vào.

Các proxy được tạo bởi [`readonly()`](./reactivity-core#readonly) và [`shallowReadonly()`](./reactivity-advanced#shallowreadonly) đều được coi là readonly, cũng như ref [`computed()`](./reactivity-core#computed) không có hàm `set`.

- **Kiểu**

  ```ts
  function isReadonly(value: unknown): boolean
  ```
