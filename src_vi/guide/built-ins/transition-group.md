<script setup>
import ListBasic from './transition-demos/ListBasic.vue'
import ListMove from './transition-demos/ListMove.vue'
import ListStagger from './transition-demos/ListStagger.vue'
</script>

# TransitionGroup {#transitiongroup}

`<TransitionGroup>` là một component dựng sẵn được thiết kế để tạo hiệu ứng cho việc chèn, xóa và thay đổi thứ tự của các phần tử hoặc component được render trong một danh sách.

## Khác gì với `<Transition>` {#differences-from-transition}

`<TransitionGroup>` hỗ trợ cùng bộ prop, class transition CSS và listener hook JavaScript như `<Transition>`, nhưng có những điểm khác sau:

- Mặc định, nó không render ra một phần tử bọc. Tuy nhiên, bạn có thể chỉ định phần tử cần render bằng prop `tag`.

- [Chế độ transition](./transition#transition-modes) không khả dụng, vì lúc này ta không còn luân phiên giữa các phần tử loại trừ lẫn nhau nữa.

- Các phần tử bên trong **luôn bắt buộc** phải có thuộc tính `key` duy nhất.

- Class transition CSS sẽ được áp dụng lên từng phần tử trong danh sách, **không phải** lên chính nhóm / container.

:::tip
Khi dùng trong [template viết trực tiếp trong DOM](/guide/essentials/component-basics#in-dom-template-parsing-caveats), nó nên được viết là `<transition-group>`.
:::

## Transition khi vào / rời khỏi {#enter-leave-transitions}

Đây là ví dụ áp dụng transition khi vào / rời khỏi cho một danh sách `v-for` bằng `<TransitionGroup>`:

```vue-html
<TransitionGroup name="list" tag="ul">
  <li v-for="item in items" :key="item">
    {{ item }}
  </li>
</TransitionGroup>
```

```css
.list-enter-active,
.list-leave-active {
  transition: all 0.5s ease;
}
.list-enter-from,
.list-leave-to {
  opacity: 0;
  transform: translateX(30px);
}
```

<ListBasic />

## Transition khi di chuyển {#move-transitions}

Demo bên trên có một nhược điểm rõ ràng: khi một mục được thêm vào hoặc bị xóa đi, các mục xung quanh sẽ "nhảy" ngay vào vị trí mới thay vì di chuyển mượt mà. Ta có thể khắc phục bằng cách thêm vài quy tắc CSS nữa:

```css{1,13-17}
.list-move, /* áp dụng transition cho phần tử đang di chuyển */
.list-enter-active,
.list-leave-active {
  transition: all 0.5s ease;
}

.list-enter-from,
.list-leave-to {
  opacity: 0;
  transform: translateX(30px);
}

/* bảo đảm phần tử đang rời đi được đưa ra khỏi luồng bố cục để
   việc tính toán animation di chuyển được chính xác. */
.list-leave-active {
  position: absolute;
}
```

Lúc này kết quả trông tốt hơn nhiều, kể cả khi toàn bộ danh sách bị xáo trộn:

<ListMove />

[Ví dụ đầy đủ](/examples/#list-transition)

### Class tùy chỉnh cho TransitionGroup {#custom-transitiongroup-classes}

Bạn cũng có thể chỉ định class transition tùy chỉnh cho phần tử đang di chuyển bằng cách truyền prop `moveClass` vào `<TransitionGroup>`, tương tự như [class transition tùy chỉnh trên `<Transition>`](/guide/built-ins/transition.html#custom-transition-classes).

## Tạo hiệu ứng lệch nhịp cho transition của danh sách {#staggering-list-transitions}

Bằng cách giao tiếp với JavaScript transition thông qua thuộc tính `data-*`, ta cũng có thể tạo hiệu ứng lệch nhịp cho transition trong danh sách. Trước hết, ta render chỉ số của từng mục thành một thuộc tính `data-*` trên phần tử DOM:

```vue-html{11}
<TransitionGroup
  tag="ul"
  :css="false"
  @before-enter="onBeforeEnter"
  @enter="onEnter"
  @leave="onLeave"
>
  <li
    v-for="(item, index) in computedList"
    :key="item.msg"
    :data-index="index"
  >
    {{ item.msg }}
  </li>
</TransitionGroup>
```

Sau đó, trong các hook JavaScript, ta tạo hiệu ứng cho phần tử với độ trễ dựa trên thuộc tính đó. Ví dụ này dùng [thư viện GSAP](https://gsap.com/) để thực hiện animation:

```js{5}
function onEnter(el, done) {
  gsap.to(el, {
    opacity: 1,
    height: '1.6em',
    delay: el.dataset.index * 0.15,
    onComplete: done
  })
}
```

<ListStagger />

<div class="composition-api">

[Ví dụ đầy đủ trên Playground](https://play.vuejs.org/#eNqlVMuu0zAQ/ZVRNklRm7QLWETtBW4FSFCxYkdYmGSSmjp28KNQVfl3xk7SFyvEponPGc+cOTPNOXrbdenRYZRHa1Nq3lkwaF33VEjedkpbOIPGeg6lajtnsYIeaq1aiOlSfAlqDOtG3L8SUchSSWNBcPrZwNdCAqVqTZND/KxdibBDjKGf3xIfWXngCNs9k4/Udu/KA3xWWnPz1zW0sOOP6CcnG3jv9ImIQn67SvrpUJ9IE/WVxPHsSkw97gbN0zFJZrB5grNPrskcLUNXac2FRZ0k3GIbIvxLSsVTq3bqF+otM5jMUi5L4So0SSicHplwOKOyfShdO1lariQo+Yy10vhO+qwoZkNFFKmxJ4Gp6ljJrRe+vMP3yJu910swNXqXcco1h0pJHDP6CZHEAAcAYMydwypYCDAkJRdX6Sts4xGtUDAKotIVs9Scpd4q/A0vYJmuXo5BSm7JOIEW81DVo77VR207ZEf8F23LB23T+X9VrbNh82nn6UAz7ASzSCeANZe0AnBctIqqbIoojLCIIBvoL5pJw31DH7Ry3VDKsoYinSii4ZyXxhBQM2Fwwt58D7NeoB8QkXfDvwRd2XtceOsCHkwc8KCINAk+vADJppQUFjZ0DsGVGT3uFn1KSjoPeKLoaYtvCO/rIlz3vH9O5FiU/nXny/pDT6YGKZngg0/Zg1GErrMbp6N5NHxJFi3N/4dRkj5IYf5ULxCmiPJpI4rIr4kHimhvbWfyLHOyOzQpNZZ57jXNy4nRGFLTR/0fWBqe7w==)

</div>
<div class="options-api">

[Ví dụ đầy đủ trên Playground](https://play.vuejs.org/#eNqtVE2P0zAQ/SujXNqgNmkPcIjaBbYCJKg4cSMcTDJNTB07+KNsVfW/M3aabNpyQltViT1vPPP8Zian6H3bJgeHURatTKF5ax9yyZtWaQuVYS3stGpg4peTXOayUNJYEJwea/ieS4ATNKbKYPKoXYGwRZzAeTYGPrNizxE2NZO30KZ2xR6+Kq25uTuGFrb81vrFyQo+On0kIJc/PCV8CmxL3DEnLJy8e8ksm8bdGkCjdVr2O4DfDvWRgtGN/JYC0SOkKVTTOotl1jv3hi3d+DngENILkey4sKinU26xiWH9AH6REN/Eqq36g3rDDE7jhMtCuBLN1NbcJIFEHN9RaNDWqjQDAyUfcac0fpA+CYoRCRSJsUeBiWpZwe2RSrK4w2rkVe2rdYG6LD5uH3EGpZI4iuurTdwDNBjpRJclg+UlhP914UnMZfIGm8kIKVEwciYivhoGLQlQ4hO8gkWyfD1yVHJDKgu0mAUmPXLuxRkYb5Ed8H8YL/7BeGx7Oa6hkLmk/yodBoo21BKtYBZpB7DikroKDvNGUeZ1HoVmyCNIO/ibZtJwy5X8pJVru9CWVeTpRB51+6wwhgw7Jgz2tnc/Q6/M0ZeWwKvmGZye0Wu78PIGexC6swdGxEnw/q6HOYUkt9DwMwhKxfS6GpY+KPHc45G8+6EYAV7reTjucf/uwUtSmvvTME1wDuISlVTwTqf0RiiyrtKR0tEs6r5l84b645dRkr5zoT8oXwBMHg2Tlke+jbwhj2prW5OlqZPtvkroYqnH3lK9nLgI46scnf8Cn22kBA==)

</div>

---

**Liên quan**

- [Tài liệu API của `<TransitionGroup>`](/api/built-in-components#transitiongroup)
