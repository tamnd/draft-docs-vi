# Render Function APIs {#render-function-apis}

## h() {#h}

Tạo các virtual DOM node (vnode).

- **Kiểu**

  ```ts
  // signature đầy đủ
  function h(
    type: string | Component,
    props?: object | null,
    children?: Children | Slot | Slots
  ): VNode

  // bỏ qua props
  function h(type: string | Component, children?: Children | Slot): VNode

  type Children = string | number | boolean | VNode | null | Children[]

  type Slot = () => Children

  type Slots = { [name: string]: Slot }
  ```

  > Kiểu được đơn giản hóa để dễ đọc.

- **Chi tiết**

  Đối số đầu tiên có thể là chuỗi (cho native element) hoặc định nghĩa Vue component. Đối số thứ hai là props được truyền vào, và đối số thứ ba là children.

  Khi tạo component vnode, children phải được truyền dưới dạng hàm slot. Một hàm slot đơn có thể được truyền nếu component chỉ cần default slot. Ngược lại, các slot phải được truyền dưới dạng object của các hàm slot.

  Để tiện lợi, đối số props có thể được bỏ qua khi children không phải là object slots.

- **Ví dụ**

  Tạo native element:

  ```js
  import { h } from 'vue'

  // tất cả đối số trừ type là tùy chọn
  h('div')
  h('div', { id: 'foo' })

  // cả attribute và property đều có thể dùng trong props
  // Vue tự động chọn cách đúng để gán
  h('div', { class: 'bar', innerHTML: 'hello' })

  // class và style hỗ trợ cùng object / array
  // giống như trong template
  h('div', { class: [foo, { bar }], style: { color: 'red' } })

  // event listener nên được truyền là onXxx
  h('div', { onClick: () => {} })

  // children có thể là chuỗi
  h('div', { id: 'foo' }, 'hello')

  // props có thể bỏ qua khi không có props
  h('div', 'hello')
  h('div', [h('span', 'hello')])

  // mảng children có thể chứa cả vnode và chuỗi
  h('div', ['hello', h('span', 'hello')])
  ```

  Tạo component:

  ```js
  import Foo from './Foo.vue'

  // truyền props
  h(Foo, {
    // tương đương some-prop="hello"
    someProp: 'hello',
    // tương đương @update="() => {}"
    onUpdate: () => {}
  })

  // truyền default slot đơn
  h(Foo, () => 'default slot')

  // truyền named slot
  // lưu ý `null` là bắt buộc để tránh
  // object slots bị coi là props
  h(MyComponent, null, {
    default: () => 'default slot',
    foo: () => h('div', 'foo'),
    bar: () => [h('span', 'one'), h('span', 'two')]
  })
  ```

- **Xem thêm** [Hướng dẫn - Render Function - Tạo VNode](/guide/extras/render-function#creating-vnodes)

## mergeProps() {#mergeprops}

Hợp nhất nhiều props object với xử lý đặc biệt cho một số props nhất định.

- **Kiểu**

  ```ts
  function mergeProps(...args: object[]): object
  ```

- **Chi tiết**

  `mergeProps()` hỗ trợ hợp nhất nhiều props object với xử lý đặc biệt cho các props sau:

  - `class`
  - `style`
  - `onXxx` event listener - nhiều listener cùng tên sẽ được hợp nhất thành một mảng.

  Nếu bạn không cần hành vi hợp nhất và muốn ghi đè đơn giản, có thể dùng native object spread thay thế.

- **Ví dụ**

  ```js
  import { mergeProps } from 'vue'

  const one = {
    class: 'foo',
    onClick: handlerA
  }

  const two = {
    class: { bar: true },
    onClick: handlerB
  }

  const merged = mergeProps(one, two)
  /**
   {
     class: 'foo bar',
     onClick: [handlerA, handlerB]
   }
   */
  ```

## cloneVNode() {#clonevnode}

Nhân bản một vnode.

- **Kiểu**

  ```ts
  function cloneVNode(vnode: VNode, extraProps?: object): VNode
  ```

- **Chi tiết**

  Trả về một vnode được nhân bản, tùy chọn với extra props để hợp nhất với bản gốc.

  Vnode nên được coi là bất biến sau khi tạo, và bạn không nên thay đổi props của vnode hiện có. Thay vào đó, nhân bản nó với props khác / bổ sung.

  Vnode có các thuộc tính nội bộ đặc biệt, vì vậy nhân bản chúng không đơn giản như object spread. `cloneVNode()` xử lý hầu hết logic nội bộ.

- **Ví dụ**

  ```js
  import { h, cloneVNode } from 'vue'

  const original = h('div')
  const cloned = cloneVNode(original, { id: 'foo' })
  ```

## isVNode() {#isvnode}

Kiểm tra xem một giá trị có phải là vnode hay không.

- **Kiểu**

  ```ts
  function isVNode(value: unknown): boolean
  ```

## resolveComponent() {#resolvecomponent}

Để giải quyết thủ công một component đã đăng ký theo tên.

- **Kiểu**

  ```ts
  function resolveComponent(name: string): Component | string
  ```

- **Chi tiết**

  **Lưu ý: bạn không cần cái này nếu có thể import trực tiếp component.**

  `resolveComponent()` phải được gọi bên trong<span class="composition-api"> `setup()` hoặc</span> render function để giải quyết từ context component đúng.

  Nếu không tìm thấy component, một runtime warning sẽ được phát ra và chuỗi tên được trả về.

- **Ví dụ**

  <div class="composition-api">

  ```js
  import { h, resolveComponent } from 'vue'

  export default {
    setup() {
      const ButtonCounter = resolveComponent('ButtonCounter')

      return () => {
        return h(ButtonCounter)
      }
    }
  }
  ```

  </div>
  <div class="options-api">

  ```js
  import { h, resolveComponent } from 'vue'

  export default {
    render() {
      const ButtonCounter = resolveComponent('ButtonCounter')
      return h(ButtonCounter)
    }
  }
  ```

  </div>

- **Xem thêm** [Hướng dẫn - Render Function - Component](/guide/extras/render-function#components)

## resolveDirective() {#resolvedirective}

Để giải quyết thủ công một directive đã đăng ký theo tên.

- **Kiểu**

  ```ts
  function resolveDirective(name: string): Directive | undefined
  ```

- **Chi tiết**

  **Lưu ý: bạn không cần cái này nếu có thể import trực tiếp directive.**

  `resolveDirective()` phải được gọi bên trong<span class="composition-api"> `setup()` hoặc</span> render function để giải quyết từ context component đúng.

  Nếu không tìm thấy directive, một runtime warning sẽ được phát ra và hàm trả về `undefined`.

- **Xem thêm** [Hướng dẫn - Render Function - Directive Tùy chỉnh](/guide/extras/render-function#custom-directives)

## withDirectives() {#withdirectives}

Để thêm custom directive vào vnode.

- **Kiểu**

  ```ts
  function withDirectives(
    vnode: VNode,
    directives: DirectiveArguments
  ): VNode

  // [Directive, value, argument, modifiers]
  type DirectiveArguments = Array<
    | [Directive]
    | [Directive, any]
    | [Directive, any, string]
    | [Directive, any, string, DirectiveModifiers]
  >
  ```

- **Chi tiết**

  Bọc một vnode hiện có với các custom directive. Đối số thứ hai là mảng các custom directive. Mỗi custom directive cũng được biểu diễn dưới dạng mảng theo hình thức `[Directive, value, argument, modifiers]`. Các phần tử cuối của mảng có thể được bỏ qua nếu không cần.

- **Ví dụ**

  ```js
  import { h, withDirectives } from 'vue'

  // một custom directive
  const pin = {
    mounted() {
      /* ... */
    },
    updated() {
      /* ... */
    }
  }

  // <div v-pin:top.animate="200"></div>
  const vnode = withDirectives(h('div'), [
    [pin, 200, 'top', { animate: true }]
  ])
  ```

- **Xem thêm** [Hướng dẫn - Render Function - Directive Tùy chỉnh](/guide/extras/render-function#custom-directives)

## withModifiers() {#withmodifiers}

Để thêm các [`v-on` modifier](/guide/essentials/event-handling#event-modifiers) có sẵn vào hàm event handler.

- **Kiểu**

  ```ts
  function withModifiers(fn: Function, modifiers: ModifierGuardsKeys[]): Function
  ```

- **Ví dụ**

  ```js
  import { h, withModifiers } from 'vue'

  const vnode = h('button', {
    // tương đương v-on:click.stop.prevent
    onClick: withModifiers(() => {
      // ...
    }, ['stop', 'prevent'])
  })
  ```

- **Xem thêm** [Hướng dẫn - Render Function - Event Modifier](/guide/extras/render-function#event-modifiers)
