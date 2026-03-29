# Custom Elements API {#custom-elements-api}

## defineCustomElement() {#definecustomelement}

Method này nhận cùng đối số như [`defineComponent`](#definecomponent), nhưng thay vào đó trả về một constructor class [Custom Element](https://developer.mozilla.org/en-US/docs/Web/Web_Components/Using_custom_elements) native.

- **Kiểu**

  ```ts
  function defineCustomElement(
    component:
      | (ComponentOptions & CustomElementsOptions)
      | ComponentOptions['setup'],
    options?: CustomElementsOptions
  ): {
    new (props?: object): HTMLElement
  }

  interface CustomElementsOptions {
    styles?: string[]

    // các tùy chọn sau là 3.5+
    configureApp?: (app: App) => void
    shadowRoot?: boolean
    nonce?: string
  }
  ```

  > Kiểu được đơn giản hóa để dễ đọc.

- **Chi tiết**

  Ngoài các component option thông thường, `defineCustomElement()` cũng hỗ trợ một số tùy chọn đặc thù cho custom element:

  - **`styles`**: một mảng các chuỗi CSS inline để cung cấp CSS nên được inject vào shadow root của element.

  - **`configureApp`** <sup class="vt-badge" data-text="3.5+"/>: hàm có thể dùng để cấu hình Vue app instance cho custom element.

  - **`shadowRoot`** <sup class="vt-badge" data-text="3.5+"/>: `boolean`, mặc định là `true`. Đặt thành `false` để render custom element mà không có shadow root. Điều này có nghĩa là `<style>` trong custom element SFC sẽ không còn được đóng gói nữa.

  - **`nonce`** <sup class="vt-badge" data-text="3.5+"/>: `string`, nếu được cung cấp, sẽ được đặt làm thuộc tính `nonce` trên các style tag được inject vào shadow root.

  Lưu ý rằng thay vì truyền như một phần của component, các tùy chọn này cũng có thể được truyền qua đối số thứ hai:

  ```js
  import Element from './MyElement.ce.vue'

  defineCustomElement(Element, {
    configureApp(app) {
      // ...
    }
  })
  ```

  Giá trị trả về là một custom element constructor có thể được đăng ký bằng [`customElements.define()`](https://developer.mozilla.org/en-US/docs/Web/API/CustomElementRegistry/define).

- **Ví dụ**

  ```js
  import { defineCustomElement } from 'vue'

  const MyVueElement = defineCustomElement({
    /* tùy chọn component */
  })

  // Đăng ký custom element.
  customElements.define('my-vue-element', MyVueElement)
  ```

- **Xem thêm**

  - [Hướng dẫn - Xây dựng Custom Element với Vue](/guide/extras/web-components#building-custom-elements-with-vue)

  - Cũng lưu ý rằng `defineCustomElement()` yêu cầu [cấu hình đặc biệt](/guide/extras/web-components#sfc-as-custom-element) khi dùng với Single-File Component.

## useHost() <sup class="vt-badge" data-text="3.5+"/> {#usehost}

Một Composition API helper trả về host element của Vue custom element hiện tại.

## useShadowRoot() <sup class="vt-badge" data-text="3.5+"/> {#useshadowroot}

Một Composition API helper trả về shadow root của Vue custom element hiện tại.

## this.$host <sup class="vt-badge" data-text="3.5+"/> {#this-host}

Một thuộc tính Options API expose host element của Vue custom element hiện tại.
