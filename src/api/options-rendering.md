# Options: Rendering {#options-rendering}

## template {#template}

Một string template cho component.

- **Kiểu**

  ```ts
  interface ComponentOptions {
    template?: string
  }
  ```

- **Chi tiết**

  Template được cung cấp qua tùy chọn `template` sẽ được biên dịch on-the-fly lúc chạy. Nó chỉ được hỗ trợ khi dùng bản build Vue có bao gồm trình biên dịch template. Trình biên dịch template **KHÔNG** được bao gồm trong các bản build Vue có chữ `runtime` trong tên, ví dụ `vue.runtime.esm-bundler.js`. Xem [hướng dẫn dist file](https://github.com/vuejs/core/tree/main/packages/vue#which-dist-file-to-use) để biết thêm chi tiết về các bản build khác nhau.

  Nếu chuỗi bắt đầu bằng `#`, nó sẽ được dùng như `querySelector` và dùng `innerHTML` của element được chọn làm chuỗi template. Điều này cho phép template nguồn được soạn bằng các element `<template>` native.

  Nếu tùy chọn `render` cũng có trong cùng component, `template` sẽ bị bỏ qua.

  Nếu component gốc của ứng dụng không có tùy chọn `template` hay `render`, Vue sẽ cố dùng `innerHTML` của element được mount làm template thay thế.

  :::warning Lưu ý Bảo mật
  Chỉ dùng các nguồn template mà bạn có thể tin tưởng. Đừng dùng nội dung do người dùng cung cấp làm template của bạn. Xem [Hướng dẫn Bảo mật](/guide/best-practices/security#rule-no-1-never-use-non-trusted-templates) để biết thêm chi tiết.
  :::

## render {#render}

Một hàm lập trình trả về cây virtual DOM của component.

- **Kiểu**

  ```ts
  interface ComponentOptions {
    render?(this: ComponentPublicInstance) => VNodeChild
  }

  type VNodeChild = VNodeChildAtom | VNodeArrayChildren

  type VNodeChildAtom =
    | VNode
    | string
    | number
    | boolean
    | null
    | undefined
    | void

  type VNodeArrayChildren = (VNodeArrayChildren | VNodeChildAtom)[]
  ```

- **Chi tiết**

  `render` là thay thế cho string template cho phép bạn tận dụng toàn bộ sức mạnh lập trình của JavaScript để khai báo output render của component.

  Template được pre-compile, ví dụ trong Single-File Component, được biên dịch thành tùy chọn `render` lúc build. Nếu cả `render` và `template` đều có trong một component, `render` sẽ có độ ưu tiên cao hơn.

- **Xem thêm**
  - [Cơ chế Render](/guide/extras/rendering-mechanism)
  - [Render Function](/guide/extras/render-function)

## compilerOptions {#compileroptions}

Cấu hình các tùy chọn runtime compiler cho template của component.

- **Kiểu**

  ```ts
  interface ComponentOptions {
    compilerOptions?: {
      isCustomElement?: (tag: string) => boolean
      whitespace?: 'condense' | 'preserve' // mặc định: 'condense'
      delimiters?: [string, string] // mặc định: ['{{', '}}']
      comments?: boolean // mặc định: false
    }
  }
  ```

- **Chi tiết**

  Tùy chọn cấu hình này chỉ có hiệu lực khi dùng bản build đầy đủ (tức là `vue.js` độc lập có thể biên dịch template trong trình duyệt). Nó hỗ trợ các tùy chọn giống như [app.config.compilerOptions](/api/application#app-config-compileroptions) ở cấp ứng dụng, và có độ ưu tiên cao hơn cho component hiện tại.

- **Xem thêm** [app.config.compilerOptions](/api/application#app-config-compileroptions)

## slots<sup class="vt-badge ts"/> {#slots}

- Chỉ hỗ trợ từ 3.3+

Một tùy chọn để hỗ trợ suy luận kiểu khi dùng slot theo kiểu lập trình trong render function.

- **Chi tiết**

  Giá trị runtime của tùy chọn này không được dùng. Các kiểu thực sự nên được khai báo qua ép kiểu bằng helper kiểu `SlotsType`:

  ```ts
  import { SlotsType } from 'vue'

  defineComponent({
    slots: Object as SlotsType<{
      default: { foo: string; bar: number }
      item: { data: number }
    }>,
    setup(props, { slots }) {
      expectType<
        undefined | ((scope: { foo: string; bar: number }) => any)
      >(slots.default)
      expectType<undefined | ((scope: { data: number }) => any)>(
        slots.item
      )
    }
  })
  ```
