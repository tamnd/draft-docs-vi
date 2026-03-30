# Composition API: <br>Tiêm Phụ Thuộc {#composition-api-dependency-injection}

## provide() {#provide}

Cung cấp một giá trị có thể được inject bởi các component con.

- **Kiểu**

  ```ts
  function provide<T>(key: InjectionKey<T> | string, value: T): void
  ```

- **Chi tiết**

  `provide()` nhận hai đối số: key có thể là string hoặc symbol, và giá trị được inject.

  Khi dùng TypeScript, key có thể là symbol được cast thành `InjectionKey` - một kiểu tiện ích do Vue cung cấp, kế thừa `Symbol`, có thể dùng để đồng bộ kiểu giá trị giữa `provide()` và `inject()`.

  Tương tự như các API đăng ký hook vòng đời, `provide()` phải được gọi đồng bộ trong giai đoạn `setup()` của component.

- **Ví dụ**

  ```vue
  <script setup>
  import { ref, provide } from 'vue'
  import { countSymbol } from './injectionSymbols'

  // cung cấp giá trị tĩnh
  provide('path', '/project/')

  // cung cấp giá trị có tính phản ứng
  const count = ref(0)
  provide('count', count)

  // cung cấp với Symbol key
  provide(countSymbol, count)
  </script>
  ```

- **Xem thêm**
  - [Hướng dẫn - Provide / Inject](/guide/components/provide-inject)
  - [Hướng dẫn - Khai báo Kiểu cho Provide / Inject](/guide/typescript/composition-api#typing-provide-inject) <sup class="vt-badge ts" />

## inject() {#inject}

Inject một giá trị được cung cấp bởi component tổ tiên hoặc ứng dụng (qua `app.provide()`).

- **Kiểu**

  ```ts
  // không có giá trị mặc định
  function inject<T>(key: InjectionKey<T> | string): T | undefined

  // có giá trị mặc định
  function inject<T>(key: InjectionKey<T> | string, defaultValue: T): T

  // với factory
  function inject<T>(
    key: InjectionKey<T> | string,
    defaultValue: () => T,
    treatDefaultAsFactory: true
  ): T
  ```

- **Chi tiết**

  Đối số đầu tiên là injection key. Vue sẽ duyệt ngược chuỗi cha để tìm giá trị được cung cấp với key khớp. Nếu nhiều component trong chuỗi cha cung cấp cùng một key, component gần nhất với component đang inject sẽ "che khuất" các component cao hơn trong chuỗi và giá trị của nó sẽ được dùng. Nếu không tìm thấy giá trị với key khớp, `inject()` trả về `undefined` trừ khi có giá trị mặc định.

  Đối số thứ hai là tùy chọn và là giá trị mặc định được dùng khi không tìm thấy giá trị khớp.

  Đối số thứ hai cũng có thể là hàm factory trả về các giá trị tốn kém để tạo. Trong trường hợp đó, phải truyền `true` là đối số thứ ba để chỉ ra rằng hàm nên được dùng như factory thay vì là giá trị.

  Tương tự như các API đăng ký hook vòng đời, `inject()` phải được gọi đồng bộ trong giai đoạn `setup()` của component.

  Khi dùng TypeScript, key có thể có kiểu `InjectionKey` - một kiểu tiện ích do Vue cung cấp, kế thừa `Symbol`, có thể dùng để đồng bộ kiểu giá trị giữa `provide()` và `inject()`.

- **Ví dụ**

  Giả sử component cha đã cung cấp các giá trị như trong ví dụ `provide()` ở trên:

  ```vue
  <script setup>
  import { inject } from 'vue'
  import { countSymbol } from './injectionSymbols'

  // inject giá trị tĩnh không có mặc định
  const path = inject('path')

  // inject giá trị có tính phản ứng
  const count = inject('count')

  // inject với Symbol key
  const count2 = inject(countSymbol)

  // inject với giá trị mặc định
  const bar = inject('path', '/default-path')

  // inject với hàm giá trị mặc định
  const fn = inject('function', () => {})

  // inject với factory giá trị mặc định
  const baz = inject('factory', () => new ExpensiveObject(), true)
  </script>
  ```

- **Xem thêm**
  - [Hướng dẫn - Provide / Inject](/guide/components/provide-inject)
  - [Hướng dẫn - Khai báo Kiểu cho Provide / Inject](/guide/typescript/composition-api#typing-provide-inject) <sup class="vt-badge ts" />

## hasInjectionContext() {#has-injection-context}

- Chỉ hỗ trợ từ 3.3+

Trả về true nếu [inject()](#inject) có thể được dùng mà không có cảnh báo về việc gọi sai vị trí (ví dụ: bên ngoài `setup()`). Phương thức này được thiết kế để các thư viện muốn dùng `inject()` nội bộ mà không kích hoạt cảnh báo cho người dùng cuối.

- **Kiểu**

  ```ts
  function hasInjectionContext(): boolean
  ```
