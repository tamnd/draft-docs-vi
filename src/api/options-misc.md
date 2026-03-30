# Options: Misc {#options-misc}

## name {#name}

Khai báo rõ ràng tên hiển thị cho component.

- **Kiểu**

  ```ts
  interface ComponentOptions {
    name?: string
  }
  ```

- **Chi tiết**

  Tên của component được dùng cho:

  - Tự tham chiếu đệ quy trong template riêng của component
  - Hiển thị trong cây kiểm tra component của Vue DevTools
  - Hiển thị trong trace cảnh báo component

  Khi dùng Single-File Component, component đã tự suy luận tên từ tên file. Ví dụ, file có tên `MyComponent.vue` sẽ có tên hiển thị được suy luận là "MyComponent".

  Trường hợp khác là khi component được đăng ký toàn cục bằng [`app.component`](/api/application#app-component), ID toàn cục được tự động đặt làm tên của nó.

  Tùy chọn `name` cho phép bạn ghi đè tên được suy luận, hoặc cung cấp rõ ràng một tên khi không thể suy luận được tên (ví dụ: khi không dùng build tool, hoặc component non-SFC inline).

  Có một trường hợp `name` cần thiết rõ ràng: khi so khớp với các component có thể cache trong [`<KeepAlive>`](/guide/built-ins/keep-alive) qua các prop `include / exclude` của nó.

  :::tip
  Từ phiên bản 3.2.34, một single-file component dùng `<script setup>` sẽ tự động suy luận tùy chọn `name` của nó dựa trên tên file, không cần khai báo tên thủ công ngay cả khi dùng với `<KeepAlive>`.
  :::

## inheritAttrs {#inheritattrs}

Kiểm soát việc hành vi fallthrough thuộc tính component mặc định có được bật hay không.

- **Kiểu**

  ```ts
  interface ComponentOptions {
    inheritAttrs?: boolean // mặc định: true
  }
  ```

- **Chi tiết**

  Mặc định, các ràng buộc thuộc tính phạm vi cha không được nhận diện là props sẽ "kế thừa qua". Điều này có nghĩa là khi ta có component có root đơn, các ràng buộc này sẽ được áp dụng vào element gốc của component con như các thuộc tính HTML thông thường. Khi viết component bọc một element mục tiêu hoặc component khác, đây có thể không phải hành vi mong muốn. Bằng cách đặt `inheritAttrs` thành `false`, hành vi mặc định này có thể bị tắt. Các thuộc tính có sẵn qua thuộc tính instance `$attrs` và có thể được ràng buộc rõ ràng vào element không phải root bằng `v-bind`.

- **Ví dụ**

  <div class="options-api">

  ```vue
  <script>
  export default {
    inheritAttrs: false,
    props: ['label', 'value'],
    emits: ['input']
  }
  </script>

  <template>
    <label>
      {{ label }}
      <input
        v-bind="$attrs"
        v-bind:value="value"
        v-on:input="$emit('input', $event.target.value)"
      />
    </label>
  </template>
  ```

  </div>
  <div class="composition-api">

  Khi khai báo tùy chọn này trong component dùng `<script setup>`, bạn có thể dùng macro [`defineOptions`](/api/sfc-script-setup#defineoptions):

  ```vue
  <script setup>
  defineProps(['label', 'value'])
  defineEmits(['input'])
  defineOptions({
    inheritAttrs: false
  })
  </script>

  <template>
    <label>
      {{ label }}
      <input
        v-bind="$attrs"
        v-bind:value="value"
        v-on:input="$emit('input', $event.target.value)"
      />
    </label>
  </template>
  ```

  </div>

- **Xem thêm**

  - [Thuộc tính Kế thừa](/guide/components/attrs)
  <div class="composition-api">

  - [Dùng `inheritAttrs` trong `<script>` thông thường](/api/sfc-script-setup.html#usage-alongside-normal-script)
  </div>

## components {#components}

Một object đăng ký các component để có sẵn cho instance component.

- **Kiểu**

  ```ts
  interface ComponentOptions {
    components?: { [key: string]: Component }
  }
  ```

- **Ví dụ**

  ```js
  import Foo from './Foo.vue'
  import Bar from './Bar.vue'

  export default {
    components: {
      // cú pháp rút gọn
      Foo,
      // đăng ký với tên khác
      RenamedBar: Bar
    }
  }
  ```

- **Xem thêm** [Đăng ký Component](/guide/components/registration)

## directives {#directives}

Một object đăng ký các directive để có sẵn cho instance component.

- **Kiểu**

  ```ts
  interface ComponentOptions {
    directives?: { [key: string]: Directive }
  }
  ```

- **Ví dụ**

  ```js
  export default {
    directives: {
      // bật v-focus trong template
      focus: {
        mounted(el) {
          el.focus()
        }
      }
    }
  }
  ```

  ```vue-html
  <input v-focus>
  ```

- **Xem thêm** [Directive Tùy chỉnh](/guide/reusability/custom-directives)
