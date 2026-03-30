# Options: Composition {#options-composition}

## provide {#provide}

Cung cấp các giá trị có thể được inject bởi các component con.

- **Kiểu**

  ```ts
  interface ComponentOptions {
    provide?: object | ((this: ComponentPublicInstance) => object)
  }
  ```

- **Chi tiết**

  `provide` và [`inject`](#inject) được dùng cùng nhau để cho phép component tổ tiên đóng vai trò là dependency injector cho tất cả component con của nó, bất kể cây component sâu đến đâu, miễn là chúng cùng một chuỗi cha.

  Tùy chọn `provide` phải là một object hoặc hàm trả về object. Object này chứa các thuộc tính có sẵn để inject vào các component con. Bạn có thể dùng Symbol làm key trong object này.

- **Ví dụ**

  Sử dụng cơ bản:

  ```js
  const s = Symbol()

  export default {
    provide: {
      foo: 'foo',
      [s]: 'bar'
    }
  }
  ```

  Dùng hàm để cung cấp state theo từng component:

  ```js
  export default {
    data() {
      return {
        msg: 'foo'
      }
    }
    provide() {
      return {
        msg: this.msg
      }
    }
  }
  ```

  Lưu ý trong ví dụ trên, `msg` được cung cấp sẽ KHÔNG có tính phản ứng. Xem [Làm việc với Tính phản ứng](/guide/components/provide-inject#working-with-reactivity) để biết thêm chi tiết.

- **Xem thêm** [Provide / Inject](/guide/components/provide-inject)

## inject {#inject}

Khai báo các thuộc tính để inject vào component hiện tại bằng cách tìm chúng từ các provider tổ tiên.

- **Kiểu**

  ```ts
  interface ComponentOptions {
    inject?: ArrayInjectOptions | ObjectInjectOptions
  }

  type ArrayInjectOptions = string[]

  type ObjectInjectOptions = {
    [key: string | symbol]:
      | string
      | symbol
      | { from?: string | symbol; default?: any }
  }
  ```

- **Chi tiết**

  Tùy chọn `inject` phải là:

  - Một mảng chuỗi, hoặc
  - Một object trong đó key là tên binding cục bộ và giá trị là:
    - Key (chuỗi hoặc Symbol) để tìm trong các injection có sẵn, hoặc
    - Một object trong đó:
      - Thuộc tính `from` là key (chuỗi hoặc Symbol) để tìm trong các injection có sẵn, và
      - Thuộc tính `default` được dùng làm giá trị dự phòng. Tương tự giá trị mặc định của props, cần hàm factory cho các kiểu object để tránh chia sẻ giá trị giữa nhiều instance component.

  Thuộc tính được inject sẽ là `undefined` nếu không có thuộc tính khớp hay giá trị mặc định nào được cung cấp.

  Lưu ý các binding được inject KHÔNG có tính phản ứng. Đây là chủ ý. Tuy nhiên, nếu giá trị được inject là object phản ứng, các thuộc tính trên object đó vẫn có tính phản ứng. Xem [Làm việc với Tính phản ứng](/guide/components/provide-inject#working-with-reactivity) để biết thêm chi tiết.

- **Ví dụ**

  Sử dụng cơ bản:

  ```js
  export default {
    inject: ['foo'],
    created() {
      console.log(this.foo)
    }
  }
  ```

  Dùng giá trị được inject làm mặc định cho một prop:

  ```js
  const Child = {
    inject: ['foo'],
    props: {
      bar: {
        default() {
          return this.foo
        }
      }
    }
  }
  ```

  Dùng giá trị được inject như một data entry:

  ```js
  const Child = {
    inject: ['foo'],
    data() {
      return {
        bar: this.foo
      }
    }
  }
  ```

  Injection có thể tùy chọn với giá trị mặc định:

  ```js
  const Child = {
    inject: {
      foo: { default: 'foo' }
    }
  }
  ```

  Nếu cần inject từ thuộc tính có tên khác, dùng `from` để chỉ định thuộc tính nguồn:

  ```js
  const Child = {
    inject: {
      foo: {
        from: 'bar',
        default: 'foo'
      }
    }
  }
  ```

  Tương tự giá trị mặc định của props, bạn cần dùng hàm factory cho các giá trị không nguyên thủy:

  ```js
  const Child = {
    inject: {
      foo: {
        from: 'bar',
        default: () => [1, 2, 3]
      }
    }
  }
  ```

- **Xem thêm** [Provide / Inject](/guide/components/provide-inject)

## mixins {#mixins}

Một mảng các options object được hợp nhất vào component hiện tại.

- **Kiểu**

  ```ts
  interface ComponentOptions {
    mixins?: ComponentOptions[]
  }
  ```

- **Chi tiết**

  Tùy chọn `mixins` nhận một mảng mixin object. Các mixin object này có thể chứa các instance option giống như các instance object thông thường, và chúng sẽ được hợp nhất với các options cuối cùng bằng logic hợp nhất option nhất định. Ví dụ, nếu mixin có hook `created` và bản thân component cũng có một hook, cả hai hàm đều sẽ được gọi.

  Các hook mixin được gọi theo thứ tự chúng được cung cấp, và được gọi trước hook riêng của component.

  :::warning Không Còn Khuyến nghị
  Trong Vue 2, mixin là cơ chế chính để tạo ra các phần logic component có thể tái sử dụng. Trong khi mixin vẫn được hỗ trợ trong Vue 3, [hàm Composable dùng Composition API](/guide/reusability/composables) hiện là cách được ưu tiên để tái sử dụng code giữa các component.
  :::

- **Ví dụ**

  ```js
  const mixin = {
    created() {
      console.log(1)
    }
  }

  createApp({
    created() {
      console.log(2)
    },
    mixins: [mixin]
  })

  // => 1
  // => 2
  ```

## extends {#extends}

Một component "lớp cơ sở" để kế thừa.

- **Kiểu**

  ```ts
  interface ComponentOptions {
    extends?: ComponentOptions
  }
  ```

- **Chi tiết**

  Cho phép một component kế thừa từ một component khác, thừa hưởng các component option của nó.

  Từ góc độ triển khai, `extends` gần giống hệt `mixins`. Component được chỉ định bởi `extends` sẽ được coi như là mixin đầu tiên.

  Tuy nhiên, `extends` và `mixins` biểu đạt ý định khác nhau. Tùy chọn `mixins` chủ yếu được dùng để soạn các phần chức năng, trong khi `extends` chủ yếu liên quan đến kế thừa.

  Như với `mixins`, mọi option (ngoại trừ `setup()`) sẽ được hợp nhất bằng chiến lược hợp nhất liên quan.

- **Ví dụ**

  ```js
  const CompA = { ... }

  const CompB = {
    extends: CompA,
    ...
  }
  ```

  :::warning Không Khuyến nghị cho Composition API
  `extends` được thiết kế cho Options API và không xử lý việc hợp nhất hook `setup()`.

  Trong Composition API, mô hình tư duy ưu tiên để tái sử dụng logic là "compose" thay vì "kế thừa". Nếu bạn có logic từ một component cần được tái sử dụng ở component khác, hãy xem xét trích xuất logic liên quan vào một [Composable](/guide/reusability/composables#composables).

  Nếu bạn vẫn muốn "kế thừa" một component dùng Composition API, bạn có thể gọi `setup()` của component cơ sở trong `setup()` của component kế thừa:

  ```js
  import Base from './Base.js'
  export default {
    extends: Base,
    setup(props, ctx) {
      return {
        ...Base.setup(props, ctx),
        // binding cục bộ
      }
    }
  }
  ```
  :::
