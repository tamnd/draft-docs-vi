# Global API: Tổng quát {#global-api-general}

## version {#version}

Phiên bản hiện tại của Vue.

- **Kiểu:** `string`

- **Ví dụ**

  ```js
  import { version } from 'vue'

  console.log(version)
  ```

## nextTick() {#nexttick}

Tiện ích để chờ lần flush cập nhật DOM tiếp theo.

- **Kiểu**

  ```ts
  function nextTick(callback?: () => void): Promise<void>
  ```

- **Chi tiết**

  Khi bạn thay đổi state phản ứng trong Vue, các cập nhật DOM kết quả không được áp dụng đồng bộ. Thay vào đó, Vue đệm chúng cho đến "next tick" để đảm bảo mỗi component chỉ cập nhật một lần dù bạn đã thực hiện bao nhiêu thay đổi state.

  `nextTick()` có thể được dùng ngay sau khi thay đổi state để chờ cập nhật DOM hoàn tất. Bạn có thể truyền callback làm đối số, hoặc await Promise được trả về.

- **Ví dụ**

  <div class="composition-api">

  ```vue
  <script setup>
  import { ref, nextTick } from 'vue'

  const count = ref(0)

  async function increment() {
    count.value++

    // DOM chưa được cập nhật
    console.log(document.getElementById('counter').textContent) // 0

    await nextTick()
    // DOM đã được cập nhật
    console.log(document.getElementById('counter').textContent) // 1
  }
  </script>

  <template>
    <button id="counter" @click="increment">{{ count }}</button>
  </template>
  ```

  </div>
  <div class="options-api">

  ```vue
  <script>
  import { nextTick } from 'vue'

  export default {
    data() {
      return {
        count: 0
      }
    },
    methods: {
      async increment() {
        this.count++

        // DOM chưa được cập nhật
        console.log(document.getElementById('counter').textContent) // 0

        await nextTick()
        // DOM đã được cập nhật
        console.log(document.getElementById('counter').textContent) // 1
      }
    }
  }
  </script>

  <template>
    <button id="counter" @click="increment">{{ count }}</button>
  </template>
  ```

  </div>

- **Xem thêm** [`this.$nextTick()`](/api/component-instance#nexttick)

## defineComponent() {#definecomponent}

Tiện ích kiểu để định nghĩa Vue component với suy luận kiểu.

- **Kiểu**

  ```ts
  // cú pháp options
  function defineComponent(
    component: ComponentOptions
  ): ComponentConstructor

  // cú pháp hàm (yêu cầu 3.3+)
  function defineComponent(
    setup: ComponentOptions['setup'],
    extraOptions?: ComponentOptions
  ): () => any
  ```

  > Kiểu được đơn giản hóa để dễ đọc.

- **Chi tiết**

  Đối số đầu tiên nhận một component options object. Giá trị trả về sẽ là cùng options object đó, vì hàm về cơ bản là no-op lúc chạy chỉ dành cho mục đích suy luận kiểu.

  Lưu ý kiểu trả về hơi đặc biệt: nó sẽ là một kiểu constructor có kiểu instance là kiểu instance component được suy luận dựa trên các options. Điều này được dùng để suy luận kiểu khi kiểu được trả về dùng làm tag trong TSX.

  Bạn có thể trích xuất kiểu instance của một component (tương đương với kiểu của `this` trong options của nó) từ kiểu trả về của `defineComponent()` như sau:

  ```ts
  const Foo = defineComponent(/* ... */)

  type FooInstance = InstanceType<typeof Foo>
  ```

  ### Chữ ký Hàm {#function-signature}

  - Chỉ hỗ trợ từ 3.3+

  `defineComponent()` cũng có chữ ký thay thế được thiết kế để dùng với Composition API và [render function hoặc JSX](/guide/extras/render-function.html).

  Thay vì truyền vào options object, một hàm được nhận thay thế. Hàm này hoạt động giống hàm [`setup()`](/api/composition-api-setup.html#composition-api-setup) của Composition API: nó nhận props và setup context. Giá trị trả về phải là render function - cả `h()` lẫn JSX đều được hỗ trợ:

  ```js
  import { ref, h } from 'vue'

  const Comp = defineComponent(
    (props) => {
      // dùng Composition API ở đây giống trong <script setup>
      const count = ref(0)

      return () => {
        // render function hoặc JSX
        return h('div', count.value)
      }
    },
    // options bổ sung, ví dụ: khai báo props và emits
    {
      props: {
        /* ... */
      }
    }
  )
  ```

  Trường hợp sử dụng chính cho chữ ký này là với TypeScript (và đặc biệt là với TSX), vì nó hỗ trợ generic:

  ```tsx
  const Comp = defineComponent(
    <T extends string | number>(props: { msg: T; list: T[] }) => {
      // dùng Composition API ở đây giống trong <script setup>
      const count = ref(0)

      return () => {
        // render function hoặc JSX
        return <div>{count.value}</div>
      }
    },
    // hiện tại vẫn cần khai báo runtime props thủ công.
    {
      props: ['msg', 'list']
    }
  )
  ```

  Trong tương lai, chúng tôi có kế hoạch cung cấp Babel plugin tự động suy luận và inject runtime props (giống như `defineProps` trong SFC) để có thể bỏ qua khai báo runtime props.

  ### Lưu ý về Treeshaking trong webpack {#note-on-webpack-treeshaking}

  Vì `defineComponent()` là một lời gọi hàm, nó có thể trông như tạo ra side effect với một số build tool, ví dụ webpack. Điều này sẽ ngăn component bị tree-shaken ngay cả khi component không bao giờ được dùng.

  Để nói với webpack rằng lời gọi hàm này an toàn để tree-shaken, bạn có thể thêm chú thích `/*#__PURE__*/` trước lời gọi hàm:

  ```js
  export default /*#__PURE__*/ defineComponent(/* ... */)
  ```

  Lưu ý điều này không cần thiết nếu bạn đang dùng Vite, vì Rollup (bundler production nền tảng được Vite sử dụng) đủ thông minh để xác định rằng `defineComponent()` thực sự không có side effect mà không cần chú thích thủ công.

- **Xem thêm** [Hướng dẫn - Dùng Vue với TypeScript](/guide/typescript/overview#general-usage-notes)

## defineAsyncComponent() {#defineasynccomponent}

Định nghĩa một async component được lazy load chỉ khi nó được render. Đối số có thể là hàm loader, hoặc options object để kiểm soát nâng cao hơn về hành vi tải.

- **Kiểu**

  ```ts
  function defineAsyncComponent(
    source: AsyncComponentLoader | AsyncComponentOptions
  ): Component

  type AsyncComponentLoader = () => Promise<Component>

  interface AsyncComponentOptions {
    loader: AsyncComponentLoader
    loadingComponent?: Component
    errorComponent?: Component
    delay?: number
    timeout?: number
    suspensible?: boolean
    onError?: (
      error: Error,
      retry: () => void,
      fail: () => void,
      attempts: number
    ) => any
  }
  ```

- **Xem thêm** [Hướng dẫn - Async Component](/guide/components/async)
