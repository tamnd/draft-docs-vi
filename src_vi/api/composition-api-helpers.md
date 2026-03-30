# Composition API: Helpers {#composition-api-helpers}

## useAttrs() {#useattrs}

Trả về object `attrs` từ [Setup Context](/api/composition-api-setup#setup-context), bao gồm các [thuộc tính kế thừa](/guide/components/attrs#fallthrough-attributes) của component hiện tại. Tính năng này dùng trong `<script setup>` nơi không có setup context object.

- **Kiểu**

  ```ts
  function useAttrs(): Record<string, unknown>
  ```

## useSlots() {#useslots}

Trả về object `slots` từ [Setup Context](/api/composition-api-setup#setup-context), bao gồm các slot được truyền từ cha dưới dạng hàm có thể gọi trả về các Virtual DOM node. Tính năng này dùng trong `<script setup>` nơi không có setup context object.

Nếu dùng TypeScript, nên ưu tiên dùng [`defineSlots()`](/api/sfc-script-setup#defineslots) thay thế.

- **Kiểu**

  ```ts
  function useSlots(): Record<string, (...args: any[]) => VNode[]>
  ```

## useModel() {#usemodel}

Đây là helper nền tảng cho [`defineModel()`](/api/sfc-script-setup#definemodel). Nếu dùng `<script setup>`, nên ưu tiên dùng `defineModel()` thay thế.

- Chỉ có sẵn trong 3.4+

- **Kiểu**

  ```ts
  function useModel(
    props: Record<string, any>,
    key: string,
    options?: DefineModelOptions
  ): ModelRef

  type DefineModelOptions<T = any> = {
    get?: (v: T) => any
    set?: (v: T) => any
  }

  type ModelRef<T, M extends PropertyKey = string, G = T, S = T> = Ref<G, S> & [
    ModelRef<T, M, G, S>,
    Record<M, true | undefined>
  ]
  ```

- **Ví dụ**

  ```js
  export default {
    props: ['count'],
    emits: ['update:count'],
    setup(props) {
      const msg = useModel(props, 'count')
      msg.value = 1
    }
  }
  ```

- **Chi tiết**

  `useModel()` có thể dùng trong các component không phải SFC, ví dụ khi dùng hàm `setup()` thuần. Nó nhận object `props` là đối số đầu tiên, và tên model là đối số thứ hai. Đối số thứ ba tùy chọn có thể dùng để khai báo getter và setter tùy chỉnh cho model ref kết quả. Lưu ý khác với `defineModel()`, bạn tự chịu trách nhiệm khai báo props và emits.

## useTemplateRef() <sup class="vt-badge" data-text="3.5+" /> {#usetemplateref}

Trả về một shallow ref có giá trị được đồng bộ với template element hoặc component có thuộc tính ref khớp.

- **Kiểu**

  ```ts
  function useTemplateRef<T>(key: string): Readonly<ShallowRef<T | null>>
  ```

- **Ví dụ**

  ```vue
  <script setup>
  import { useTemplateRef, onMounted } from 'vue'

  const inputRef = useTemplateRef('input')

  onMounted(() => {
    inputRef.value.focus()
  })
  </script>

  <template>
    <input ref="input" />
  </template>
  ```

- **Xem thêm**
  - [Hướng dẫn - Template Ref](/guide/essentials/template-refs)
  - [Hướng dẫn - Khai báo Kiểu cho Template Ref](/guide/typescript/composition-api#typing-template-refs) <sup class="vt-badge ts" />
  - [Hướng dẫn - Khai báo Kiểu cho Component Template Ref](/guide/typescript/composition-api#typing-component-template-refs) <sup class="vt-badge ts" />

## useId() <sup class="vt-badge" data-text="3.5+" /> {#useid}

Dùng để sinh các ID duy nhất theo ứng dụng cho các thuộc tính khả năng tiếp cận hoặc các form element.

- **Kiểu**

  ```ts
  function useId(): string
  ```

- **Ví dụ**

  ```vue
  <script setup>
  import { useId } from 'vue'

  const id = useId()
  </script>

  <template>
    <form>
      <label :for="id">Tên:</label>
      <input :id="id" type="text" />
    </form>
  </template>
  ```

- **Chi tiết**

  Các ID được sinh bởi `useId()` là duy nhất theo ứng dụng. Nó có thể dùng để sinh ID cho form element và thuộc tính khả năng tiếp cận. Nhiều lời gọi trong cùng một component sẽ sinh các ID khác nhau; nhiều instance của cùng một component gọi `useId()` cũng sẽ có các ID khác nhau.

  Các ID được sinh bởi `useId()` cũng được đảm bảo ổn định giữa các lần render phía máy chủ và client, vì vậy chúng có thể dùng trong ứng dụng SSR mà không gây sai lệch hydration.

  Nếu bạn có nhiều hơn một Vue application instance trên cùng một trang, bạn có thể tránh xung đột ID bằng cách gán cho mỗi app một tiền tố ID qua [`app.config.idPrefix`](/api/application#app-config-idprefix).

  :::warning Lưu ý
  `useId()` không nên được gọi bên trong thuộc tính `computed()` vì nó có thể gây xung đột instance. Thay vào đó, hãy khai báo ID bên ngoài `computed()` và tham chiếu nó bên trong hàm computed.
  :::
