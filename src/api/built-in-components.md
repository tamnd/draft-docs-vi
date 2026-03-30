---
pageClass: api
---

# Component Dựng sẵn {#built-in-components}

:::info Đăng ký và Sử dụng
Các component dựng sẵn có thể được dùng trực tiếp trong template mà không cần đăng ký. Chúng cũng hỗ trợ tree-shaking: chỉ được đưa vào bản build khi được dùng đến.

Khi dùng trong [render function](/guide/extras/render-function), chúng cần được import rõ ràng. Ví dụ:

```js
import { h, Transition } from 'vue'

h(Transition, {
  /* props */
})
```

:::

## `<Transition>` {#transition}

Cung cấp hiệu ứng chuyển cảnh cho **một** element hoặc component đơn.

- **Props**

  ```ts
  interface TransitionProps {
    /**
     * Dùng để tự động sinh tên class CSS cho transition.
     * Ví dụ: `name: 'fade'` sẽ tự mở rộng thành `.fade-enter`,
     * `.fade-enter-active`, v.v.
     */
    name?: string
    /**
     * Có áp dụng các class CSS transition hay không.
     * Mặc định: true
     */
    css?: boolean
    /**
     * Chỉ định loại sự kiện transition cần chờ để
     * xác định thời điểm kết thúc transition.
     * Hành vi mặc định là tự phát hiện loại có
     * thời gian dài hơn.
     */
    type?: 'transition' | 'animation'
    /**
     * Chỉ định thời lượng rõ ràng của transition.
     * Hành vi mặc định là chờ sự kiện `transitionend`
     * hoặc `animationend` đầu tiên trên element gốc của transition.
     */
    duration?: number | { enter: number; leave: number }
    /**
     * Kiểm soát thứ tự thời gian của transition rời/vào.
     * Hành vi mặc định là đồng thời.
     */
    mode?: 'in-out' | 'out-in' | 'default'
    /**
     * Có áp dụng transition khi render lần đầu hay không.
     * Mặc định: false
     */
    appear?: boolean

    /**
     * Props để tùy chỉnh các class CSS transition.
     * Dùng kebab-case trong template, ví dụ: enter-from-class="xxx"
     */
    enterFromClass?: string
    enterActiveClass?: string
    enterToClass?: string
    appearFromClass?: string
    appearActiveClass?: string
    appearToClass?: string
    leaveFromClass?: string
    leaveActiveClass?: string
    leaveToClass?: string
  }
  ```

- **Sự kiện**

  - `@before-enter`
  - `@before-leave`
  - `@enter`
  - `@leave`
  - `@appear`
  - `@after-enter`
  - `@after-leave`
  - `@after-appear`
  - `@enter-cancelled`
  - `@leave-cancelled` (chỉ `v-show`)
  - `@appear-cancelled`

- **Ví dụ**

  Element đơn giản:

  ```vue-html
  <Transition>
    <div v-if="ok">nội dung được chuyển đổi</div>
  </Transition>
  ```

  Buộc transition bằng cách thay đổi thuộc tính `key`:

  ```vue-html
  <Transition>
    <div :key="text">{{ text }}</div>
  </Transition>
  ```

  Component động, với transition mode + animate khi xuất hiện:

  ```vue-html
  <Transition name="fade" mode="out-in" appear>
    <component :is="view"></component>
  </Transition>
  ```

  Lắng nghe sự kiện transition:

  ```vue-html
  <Transition @after-enter="onTransitionComplete">
    <div v-show="ok">nội dung được chuyển đổi</div>
  </Transition>
  ```

- **Xem thêm** [Hướng dẫn - Transition](/guide/built-ins/transition)

## `<TransitionGroup>` {#transitiongroup}

Cung cấp hiệu ứng transition cho **nhiều** element hoặc component trong một danh sách.

- **Props**

  `<TransitionGroup>` chấp nhận các props giống `<Transition>` ngoại trừ `mode`, cộng thêm hai props bổ sung:

  ```ts
  interface TransitionGroupProps extends Omit<TransitionProps, 'mode'> {
    /**
     * Nếu không được định nghĩa, render dưới dạng fragment.
     */
    tag?: string
    /**
     * Để tùy chỉnh class CSS áp dụng trong quá trình move transition.
     * Dùng kebab-case trong template, ví dụ: move-class="xxx"
     */
    moveClass?: string
  }
  ```

- **Sự kiện**

  `<TransitionGroup>` phát ra các sự kiện giống `<Transition>`.

- **Chi tiết**

  Mặc định, `<TransitionGroup>` không render một wrapper DOM element, nhưng có thể định nghĩa qua prop `tag`.

  Lưu ý rằng mọi phần tử con trong `<transition-group>` phải có [**key duy nhất**](/guide/essentials/list#maintaining-state-with-key) để animation hoạt động đúng.

  `<TransitionGroup>` hỗ trợ move transition qua CSS transform. Khi vị trí của một phần tử con trên màn hình thay đổi sau khi cập nhật, nó sẽ được áp dụng một class CSS di chuyển (được tự sinh từ thuộc tính `name` hoặc cấu hình qua prop `move-class`). Nếu thuộc tính CSS `transform` có thể "transition" khi class di chuyển được áp dụng, element sẽ được animate mượt mà đến đích bằng [kỹ thuật FLIP](https://aerotwist.com/blog/flip-your-animations/).

- **Ví dụ**

  ```vue-html
  <TransitionGroup tag="ul" name="slide">
    <li v-for="item in items" :key="item.id">
      {{ item.text }}
    </li>
  </TransitionGroup>
  ```

- **Xem thêm** [Hướng dẫn - TransitionGroup](/guide/built-ins/transition-group)

## `<KeepAlive>` {#keepalive}

Cache các component động được bọc bên trong.

- **Props**

  ```ts
  interface KeepAliveProps {
    /**
     * Nếu được chỉ định, chỉ các component có tên khớp với
     * `include` mới được cache.
     */
    include?: MatchPattern
    /**
     * Bất kỳ component nào có tên khớp với `exclude` sẽ
     * không được cache.
     */
    exclude?: MatchPattern
    /**
     * Số lượng tối đa các instance component được cache.
     */
    max?: number | string
  }

  type MatchPattern = string | RegExp | (string | RegExp)[]
  ```

- **Chi tiết**

  Khi bọc xung quanh một component động, `<KeepAlive>` cache các instance component không active mà không hủy chúng.

  Chỉ có thể có một instance component active là phần tử con trực tiếp của `<KeepAlive>` tại bất kỳ thời điểm nào.

  Khi một component được chuyển đổi bên trong `<KeepAlive>`, các hook vòng đời `activated` và `deactivated` của nó sẽ được gọi tương ứng, thay thế cho `mounted` và `unmounted` vốn không được gọi. Điều này áp dụng cho cả phần tử con trực tiếp của `<KeepAlive>` lẫn tất cả các component con của nó.

- **Ví dụ**

  Sử dụng cơ bản:

  ```vue-html
  <KeepAlive>
    <component :is="view"></component>
  </KeepAlive>
  ```

  Khi dùng với các nhánh `v-if` / `v-else`, chỉ có thể render một component tại một thời điểm:

  ```vue-html
  <KeepAlive>
    <comp-a v-if="a > 1"></comp-a>
    <comp-b v-else></comp-b>
  </KeepAlive>
  ```

  Dùng cùng với `<Transition>`:

  ```vue-html
  <Transition>
    <KeepAlive>
      <component :is="view"></component>
    </KeepAlive>
  </Transition>
  ```

  Dùng `include` / `exclude`:

  ```vue-html
  <!-- chuỗi phân cách bằng dấu phẩy -->
  <KeepAlive include="a,b">
    <component :is="view"></component>
  </KeepAlive>

  <!-- regex (dùng `v-bind`) -->
  <KeepAlive :include="/a|b/">
    <component :is="view"></component>
  </KeepAlive>

  <!-- Mảng (dùng `v-bind`) -->
  <KeepAlive :include="['a', 'b']">
    <component :is="view"></component>
  </KeepAlive>
  ```

  Dùng với `max`:

  ```vue-html
  <KeepAlive :max="10">
    <component :is="view"></component>
  </KeepAlive>
  ```

- **Xem thêm** [Hướng dẫn - KeepAlive](/guide/built-ins/keep-alive)

## `<Teleport>` {#teleport}

Render nội dung slot của nó đến một phần khác của DOM.

- **Props**

  ```ts
  interface TeleportProps {
    /**
     * Bắt buộc. Chỉ định container đích.
     * Có thể là selector hoặc element thực.
     */
    to: string | HTMLElement
    /**
     * Khi `true`, nội dung sẽ giữ nguyên vị trí gốc
     * thay vì được di chuyển vào container đích.
     * Có thể thay đổi động.
     */
    disabled?: boolean
    /**
     * Khi `true`, Teleport sẽ trì hoãn đến khi các phần
     * khác của ứng dụng được mount trước khi resolve đích. (3.5+)
     */
    defer?: boolean
  }
  ```

- **Ví dụ**

  Chỉ định container đích:

  ```vue-html
  <Teleport to="#some-id" />
  <Teleport to=".some-class" />
  <Teleport to="[data-teleport]" />
  ```

  Vô hiệu hóa có điều kiện:

  ```vue-html
  <Teleport to="#popup" :disabled="displayVideoInline">
    <video src="./my-movie.mp4">
  </Teleport>
  ```

  Trì hoãn resolve đích <sup class="vt-badge" data-text="3.5+" />:

  ```vue-html
  <Teleport defer to="#late-div">...</Teleport>

  <!-- ở đâu đó sau trong template -->
  <div id="late-div"></div>
  ```

- **Xem thêm** [Hướng dẫn - Teleport](/guide/built-ins/teleport)

## `<Suspense>` <sup class="vt-badge experimental" /> {#suspense}

Dùng để điều phối các dependency async lồng nhau trong cây component.

- **Props**

  ```ts
  interface SuspenseProps {
    timeout?: string | number
    suspensible?: boolean
  }
  ```

- **Sự kiện**

  - `@resolve`
  - `@pending`
  - `@fallback`

- **Chi tiết**

  `<Suspense>` chấp nhận hai slot: slot `#default` và slot `#fallback`. Nó sẽ hiển thị nội dung của fallback slot trong khi render default slot trong bộ nhớ.

  Nếu gặp các dependency async ([Component async](/guide/components/async) và component có [`async setup()`](/guide/built-ins/suspense#async-setup)) khi render default slot, nó sẽ chờ cho đến khi tất cả được resolve trước khi hiển thị default slot.

  Khi đặt Suspense là `suspensible`, tất cả việc xử lý dependency async sẽ do Suspense cha đảm nhiệm. Xem [chi tiết triển khai](https://github.com/vuejs/core/pull/6736)

- **Xem thêm** [Hướng dẫn - Suspense](/guide/built-ins/suspense)
