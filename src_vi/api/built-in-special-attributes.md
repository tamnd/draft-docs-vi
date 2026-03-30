# Thuộc tính Đặc biệt Dựng sẵn {#built-in-special-attributes}

## key {#key}

Thuộc tính đặc biệt `key` chủ yếu được dùng như gợi ý cho thuật toán virtual DOM của Vue để xác định vnode khi so sánh danh sách node mới với danh sách cũ.

- **Nhận:** `number | string | symbol`

- **Chi tiết**

  Không có key, Vue dùng thuật toán giảm thiểu di chuyển element và cố gắng vá/tái sử dụng các element cùng loại tại chỗ nhiều nhất có thể. Với key, nó sẽ sắp xếp lại các element dựa trên sự thay đổi thứ tự của key, và các element có key không còn hiện diện sẽ luôn bị xóa/hủy.

  Các con của cùng một cha chung phải có **key duy nhất**. Key trùng lặp sẽ gây lỗi render.

  Trường hợp sử dụng phổ biến nhất là kết hợp với `v-for`:

  ```vue-html
  <ul>
    <li v-for="item in items" :key="item.id">...</li>
  </ul>
  ```

  Nó cũng có thể dùng để buộc thay thế một element/component thay vì tái sử dụng nó. Điều này hữu ích khi bạn muốn:

  - Kích hoạt đúng các hook vòng đời của component
  - Kích hoạt transition

  Ví dụ:

  ```vue-html
  <transition>
    <span :key="text">{{ text }}</span>
  </transition>
  ```

  Khi `text` thay đổi, `<span>` sẽ luôn được thay thế thay vì được vá, vì vậy một transition sẽ được kích hoạt.

- **Xem thêm** [Hướng dẫn - Kết xuất Danh sách - Duy trì Trạng thái với `key`](/guide/essentials/list#maintaining-state-with-key)

## ref {#ref}

Biểu thị một [template ref](/guide/essentials/template-refs).

- **Nhận:** `string | Function`

- **Chi tiết**

  `ref` được dùng để đăng ký tham chiếu đến một element hoặc component con.

  Trong Options API, tham chiếu sẽ được đăng ký dưới object `this.$refs` của component:

  ```vue-html
  <!-- lưu trữ như this.$refs.p -->
  <p ref="p">hello</p>
  ```

  Trong Composition API, tham chiếu sẽ được lưu trữ trong một ref có tên khớp:

  ```vue
  <script setup>
  import { useTemplateRef } from 'vue'

  const pRef = useTemplateRef('p')
  </script>

  <template>
    <p ref="p">hello</p>
  </template>
  ```

  Nếu dùng trên một element DOM thuần, tham chiếu sẽ là element đó; nếu dùng trên component con, tham chiếu sẽ là instance component con.

  Ngoài ra, `ref` có thể nhận giá trị hàm cung cấp toàn quyền kiểm soát nơi lưu tham chiếu:

  ```vue-html
  <ChildComponent :ref="(el) => child = el" />
  ```

  Một lưu ý quan trọng về thời điểm đăng ký ref: vì bản thân các ref được tạo ra như kết quả của render function, bạn phải chờ đến khi component được mount trước khi truy cập chúng.

  `this.$refs` cũng không có tính phản ứng, vì vậy bạn không nên cố dùng nó trong template cho data-binding.

- **Xem thêm**
  - [Hướng dẫn - Template Ref](/guide/essentials/template-refs)
  - [Hướng dẫn - Khai báo Kiểu cho Template Ref](/guide/typescript/composition-api#typing-template-refs) <sup class="vt-badge ts" />
  - [Hướng dẫn - Khai báo Kiểu cho Component Template Ref](/guide/typescript/composition-api#typing-component-template-refs) <sup class="vt-badge ts" />

## is {#is}

Dùng để ràng buộc [component động](/guide/essentials/component-basics#dynamic-components).

- **Nhận:** `string | Component`

- **Dùng trên element native**

  - Chỉ hỗ trợ từ 3.1+

  Khi thuộc tính `is` được dùng trên một element HTML native, nó sẽ được hiểu là [Customized built-in element](https://html.spec.whatwg.org/multipage/custom-elements.html#custom-elements-customized-builtin-example), là một tính năng native của nền tảng web.

  Tuy nhiên, có trường hợp sử dụng khi bạn cần Vue thay thế một element native bằng một Vue component, như giải thích trong [Lưu ý phân tích Template in-DOM](/guide/essentials/component-basics#in-dom-template-parsing-caveats). Bạn có thể thêm tiền tố `vue:` vào giá trị của thuộc tính `is` để Vue render element đó như một Vue component:

  ```vue-html
  <table>
    <tr is="vue:my-row-component"></tr>
  </table>
  ```

- **Xem thêm**

  - [Element Đặc biệt Dựng sẵn - `<component>`](/api/built-in-special-elements#component)
  - [Component Động](/guide/essentials/component-basics#dynamic-components)
