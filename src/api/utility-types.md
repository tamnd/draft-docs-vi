# Utility Types {#utility-types}

:::info
Trang này chỉ liệt kê một số utility type thường dùng có thể cần giải thích về cách sử dụng. Để xem danh sách đầy đủ các type được xuất, xem [mã nguồn](https://github.com/vuejs/core/blob/main/packages/runtime-core/src/index.ts#L131).
:::

## PropType\<T> {#proptype-t}

Dùng để chú thích một prop với kiểu nâng cao hơn khi dùng khai báo props runtime.

- **Ví dụ**

  ```ts
  import type { PropType } from 'vue'

  interface Book {
    title: string
    author: string
    year: number
  }

  export default {
    props: {
      book: {
        // cung cấp kiểu cụ thể hơn cho `Object`
        type: Object as PropType<Book>,
        required: true
      }
    }
  }
  ```

- **Xem thêm** [Hướng dẫn - Typing Props của Component](/guide/typescript/options-api#typing-component-props)

## MaybeRef\<T> {#mayberef}

- Chỉ hỗ trợ trong 3.3+

Bí danh cho `T | Ref<T>`. Hữu ích để chú thích các đối số của [Composable](/guide/reusability/composables.html).

## MaybeRefOrGetter\<T> {#maybereforgetter}

- Chỉ hỗ trợ trong 3.3+

Bí danh cho `T | Ref<T> | (() => T)`. Hữu ích để chú thích các đối số của [Composable](/guide/reusability/composables.html).

## ExtractPropTypes\<T> {#extractproptypes}

Trích xuất kiểu props từ một options object props runtime. Các kiểu được trích xuất là hướng nội - tức là các props đã resolve mà component nhận. Điều này có nghĩa là boolean props và props với giá trị mặc định luôn được định nghĩa, ngay cả khi chúng không bắt buộc.

Để trích xuất props hướng công khai, tức là các props mà cha được phép truyền, dùng [`ExtractPublicPropTypes`](#extractpublicproptypes).

- **Ví dụ**

  ```ts
  const propsOptions = {
    foo: String,
    bar: Boolean,
    baz: {
      type: Number,
      required: true
    },
    qux: {
      type: Number,
      default: 1
    }
  } as const

  type Props = ExtractPropTypes<typeof propsOptions>
  // {
  //   foo?: string,
  //   bar: boolean,
  //   baz: number,
  //   qux: number
  // }
  ```

## ExtractPublicPropTypes\<T> {#extractpublicproptypes}

- Chỉ hỗ trợ trong 3.3+

Trích xuất kiểu props từ một options object props runtime. Các kiểu được trích xuất là hướng công khai - tức là các props mà cha được phép truyền.

- **Ví dụ**

  ```ts
  const propsOptions = {
    foo: String,
    bar: Boolean,
    baz: {
      type: Number,
      required: true
    },
    qux: {
      type: Number,
      default: 1
    }
  } as const

  type Props = ExtractPublicPropTypes<typeof propsOptions>
  // {
  //   foo?: string,
  //   bar?: boolean,
  //   baz: number,
  //   qux?: number
  // }
  ```

## ComponentCustomProperties {#componentcustomproperties}

Dùng để mở rộng kiểu component instance để hỗ trợ các global property tùy chỉnh.

- **Ví dụ**

  ```ts
  import axios from 'axios'

  declare module 'vue' {
    interface ComponentCustomProperties {
      $http: typeof axios
      $translate: (key: string) => string
    }
  }
  ```

  :::tip
  Các mở rộng phải được đặt trong file module `.ts` hoặc `.d.ts`. Xem [Vị trí Mở rộng Kiểu](/guide/typescript/options-api#augmenting-global-properties) để biết thêm chi tiết.
  :::

- **Xem thêm** [Hướng dẫn - Mở rộng Global Property](/guide/typescript/options-api#augmenting-global-properties)

## ComponentCustomOptions {#componentcustomoptions}

Dùng để mở rộng kiểu component option để hỗ trợ các tùy chọn tùy chỉnh.

- **Ví dụ**

  ```ts
  import { Route } from 'vue-router'

  declare module 'vue' {
    interface ComponentCustomOptions {
      beforeRouteEnter?(to: any, from: any, next: () => void): void
    }
  }
  ```

  :::tip
  Các mở rộng phải được đặt trong file module `.ts` hoặc `.d.ts`. Xem [Vị trí Mở rộng Kiểu](/guide/typescript/options-api#augmenting-global-properties) để biết thêm chi tiết.
  :::

- **Xem thêm** [Hướng dẫn - Mở rộng Tùy chọn Tùy chỉnh](/guide/typescript/options-api#augmenting-custom-options)

## ComponentCustomProps {#componentcustomprops}

Dùng để mở rộng các TSX prop được phép để dùng các prop không được khai báo trên các TSX element.

- **Ví dụ**

  ```ts
  declare module 'vue' {
    interface ComponentCustomProps {
      hello?: string
    }
  }

  export {}
  ```

  ```tsx
  // bây giờ hoạt động ngay cả khi hello không phải là prop được khai báo
  <MyComponent hello="world" />
  ```

  :::tip
  Các mở rộng phải được đặt trong file module `.ts` hoặc `.d.ts`. Xem [Vị trí Mở rộng Kiểu](/guide/typescript/options-api#augmenting-global-properties) để biết thêm chi tiết.
  :::

## CSSProperties {#cssproperties}

Dùng để mở rộng các giá trị được phép trong các binding thuộc tính style.

- **Ví dụ**

  Cho phép bất kỳ CSS property tùy chỉnh nào

  ```ts
  declare module 'vue' {
    interface CSSProperties {
      [key: `--${string}`]: string
    }
  }
  ```

  ```tsx
  <div style={ { '--bg-color': 'blue' } }>
  ```

  ```html
  <div :style="{ '--bg-color': 'blue' }"></div>
  ```

:::tip
Các mở rộng phải được đặt trong file module `.ts` hoặc `.d.ts`. Xem [Vị trí Mở rộng Kiểu](/guide/typescript/options-api#augmenting-global-properties) để biết thêm chi tiết.
:::

:::info Xem thêm
Tag `<style>` trong SFC hỗ trợ liên kết các giá trị CSS với dynamic component state bằng cách dùng hàm CSS `v-bind`. Điều này cho phép custom property mà không cần mở rộng kiểu.

- [v-bind() trong CSS](/api/sfc-css-features#v-bind-in-css)
  :::
