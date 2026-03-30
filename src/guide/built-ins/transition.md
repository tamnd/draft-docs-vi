<script setup>
import Basic from './transition-demos/Basic.vue'
import SlideFade from './transition-demos/SlideFade.vue'
import CssAnimation from './transition-demos/CssAnimation.vue'
import NestedTransitions from './transition-demos/NestedTransitions.vue'
import JsHooks from './transition-demos/JsHooks.vue'
import BetweenElements from './transition-demos/BetweenElements.vue'
import BetweenComponents from './transition-demos/BetweenComponents.vue'
</script>

# Transition {#transition}

Vue cung cấp hai component dựng sẵn giúp làm việc với transition và animation khi state thay đổi:

- `<Transition>` dùng để áp dụng animation khi một phần tử hoặc component đi vào và rời khỏi DOM. Đây là nội dung của trang này.

- `<TransitionGroup>` dùng để áp dụng animation khi một phần tử hoặc component được chèn vào, bị xóa khỏi, hoặc di chuyển bên trong một danh sách `v-for`. Nội dung này nằm ở [chương tiếp theo](/guide/built-ins/transition-group).

Ngoài hai component trên, ta cũng có thể áp dụng animation trong Vue bằng các kỹ thuật khác như bật / tắt class CSS hoặc animation được điều khiển bằng state thông qua style binding. Những kỹ thuật bổ sung này được trình bày trong chương [Các kỹ thuật animation](/guide/extras/animation).

## Component `<Transition>` {#the-transition-component}

`<Transition>` là một component dựng sẵn, nghĩa là nó khả dụng trong template của bất kỳ component nào mà không cần đăng ký. Nó có thể được dùng để áp dụng animation khi vào và rời khỏi cho phần tử hoặc component được truyền qua default slot. Việc đi vào hoặc rời khỏi có thể được kích hoạt bởi một trong các trường hợp sau:

- Kết xuất có điều kiện bằng `v-if`
- Kết xuất có điều kiện bằng `v-show`
- Chuyển đổi component động bằng phần tử đặc biệt `<component>`
- Thay đổi thuộc tính đặc biệt `key`

Đây là ví dụ cơ bản nhất:

```vue-html
<button @click="show = !show">Toggle</button>
<Transition>
  <p v-if="show">hello</p>
</Transition>
```

```css
/* các class này sẽ được giải thích ngay sau đây */
.v-enter-active,
.v-leave-active {
  transition: opacity 0.5s ease;
}

.v-enter-from,
.v-leave-to {
  opacity: 0;
}
```

<Basic />

<div class="composition-api">

[Thử trên Playground](https://play.vuejs.org/#eNpVkEFuwyAQRa8yZZNWqu1sunFJ1N4hSzYUjRNUDAjGVJHluxcCipIV/OG/pxEr+/a+TwuykfGogvYEEWnxR2H17F0gWCHgBBtMwc2wy9WdsMIqZ2OuXtwfHErhlcKCb8LyoVoynwPh7I0kzAmA/yxEzsKXMlr9HgRr9Es5BTue3PlskA+1VpFTkDZq0i3niYfU6anRmbqgMY4PZeH8OjwBfHhYIMdIV1OuferQEoZOKtIJ328TgzJhm8BabHR3jeC8VJqusO8/IqCM+CnsVqR3V/mfRxO5amnkCPuK5B+6rcG2fydshks=)

</div>
<div class="options-api">

[Thử trên Playground](https://play.vuejs.org/#eNpVkMFuAiEQhl9lyqlNuouXXrZo2nfwuBeKs0qKQGBAjfHdZZfVrAmB+f/M/2WGK/v1vs0JWcdEVEF72vQWz94Fgh0OMhmCa28BdpLk+0etAQJSCvahAOLBnTqgkLA6t/EpVzmCP7lFEB69kYRFAYi/ROQs/Cij1f+6ZyMG1vA2vj3bbN1+b1Dw2lYj2yBt1KRnXRwPudHDnC6pAxrjBPe1n78EBF8MUGSkixnLNjdoCUMjFemMn5NjUGacnboqPVkdOC+Vpgus2q8IKCN+T+suWENwxyWJXKXMyQ5WNVJ+aBqD3e6VSYoi)

</div>

:::tip
`<Transition>` chỉ hỗ trợ một phần tử hoặc một component duy nhất làm nội dung slot. Nếu nội dung đó là component, bản thân component đó cũng chỉ được có đúng một phần tử gốc.
:::

Khi một phần tử bên trong `<Transition>` được chèn vào hoặc bị xóa đi, đây là điều xảy ra:

1. Vue sẽ tự động dò xem phần tử đích có CSS transition hoặc animation hay không. Nếu có, một số [class transition CSS](#transition-classes) sẽ được thêm / gỡ ở những thời điểm thích hợp.

2. Nếu có listener cho [hook JavaScript](#javascript-hooks), các hook đó sẽ được gọi ở những thời điểm thích hợp.

3. Nếu không phát hiện CSS transition / animation và cũng không có hook JavaScript nào, thao tác DOM để chèn hoặc xóa sẽ được thực thi ở khung animation tiếp theo của trình duyệt.

## Transition Dựa Trên CSS {#css-based-transitions}

### Các Class Transition {#transition-classes}

Có sáu class được áp dụng cho transition khi vào / rời khỏi.

![Transition Diagram](./images/transition-classes.png)

<!-- https://www.figma.com/file/rlOv0ZKJFFNA9hYmzdZv3S/Transition-Classes -->

1. `v-enter-from`: trạng thái bắt đầu khi vào. Được thêm trước khi phần tử được chèn vào, và bị gỡ sau một frame kể từ lúc phần tử được chèn.

2. `v-enter-active`: trạng thái active khi vào. Được áp dụng trong toàn bộ giai đoạn đi vào. Được thêm trước khi phần tử được chèn vào và bị gỡ khi transition / animation kết thúc. Class này có thể được dùng để định nghĩa thời lượng, độ trễ và đường cong easing cho transition khi vào.

3. `v-enter-to`: trạng thái kết thúc khi vào. Được thêm sau một frame kể từ lúc phần tử được chèn vào (cùng lúc `v-enter-from` bị gỡ), và bị gỡ khi transition / animation kết thúc.

4. `v-leave-from`: trạng thái bắt đầu khi rời khỏi. Được thêm ngay khi transition rời khỏi được kích hoạt, và bị gỡ sau một frame.

5. `v-leave-active`: trạng thái active khi rời khỏi. Được áp dụng trong toàn bộ giai đoạn rời khỏi. Được thêm ngay khi transition rời khỏi được kích hoạt và bị gỡ khi transition / animation kết thúc. Class này có thể được dùng để định nghĩa thời lượng, độ trễ và đường cong easing cho transition rời khỏi.

6. `v-leave-to`: trạng thái kết thúc khi rời khỏi. Được thêm sau một frame kể từ lúc transition rời khỏi được kích hoạt (cùng lúc `v-leave-from` bị gỡ), và bị gỡ khi transition / animation kết thúc.

`v-enter-active` và `v-leave-active` cho phép ta chỉ định các đường cong easing khác nhau cho transition khi vào / rời khỏi, điều mà ta sẽ thấy trong các ví dụ tiếp theo.

### Transition Có Tên {#named-transitions}

Một transition có thể được đặt tên qua prop `name`:

```vue-html
<Transition name="fade">
  ...
</Transition>
```

Với transition có tên, các class transition sẽ được thêm tiền tố bằng tên đó thay vì `v`. Ví dụ, class áp dụng cho transition ở trên sẽ là `fade-enter-active` thay vì `v-enter-active`. CSS cho fade transition sẽ như sau:

```css
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.5s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}
```

### CSS Transition {#css-transitions}

`<Transition>` thường được dùng nhất khi kết hợp với [CSS transition gốc](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Transitions/Using_CSS_transitions), như trong ví dụ cơ bản ở trên. Thuộc tính CSS `transition` là một cú pháp rút gọn cho phép ta chỉ định nhiều khía cạnh của một transition, bao gồm các thuộc tính cần animate, thời lượng và [đường cong easing](https://developer.mozilla.org/en-US/docs/Web/CSS/easing-function).

Đây là một ví dụ nâng cao hơn, trong đó nhiều thuộc tính được chuyển tiếp với thời lượng và đường cong easing khác nhau cho lúc vào và lúc rời khỏi:

```vue-html
<Transition name="slide-fade">
  <p v-if="show">hello</p>
</Transition>
```

```css
/*
  Animation vào và rời khỏi có thể dùng
  thời lượng và timing function khác nhau.
*/
.slide-fade-enter-active {
  transition: all 0.3s ease-out;
}

.slide-fade-leave-active {
  transition: all 0.8s cubic-bezier(1, 0.5, 0.8, 1);
}

.slide-fade-enter-from,
.slide-fade-leave-to {
  transform: translateX(20px);
  opacity: 0;
}
```

<SlideFade />

<div class="composition-api">

[Thử trên Playground](https://play.vuejs.org/#eNqFkc9uwjAMxl/F6wXQKIVNk1AX0HbZC4zDDr2E4EK0NIkStxtDvPviFQ0OSFzyx/m+n+34kL16P+lazMpMRBW0J4hIrV9WVjfeBYIDBKzhCHVwDQySdFDZyipnY5Lu3BcsWDCk0OKosqLoKcmfLoSNN5KQbyTWLZGz8KKMVp+LKju573ivsuXKbbcG4d3oDcI9vMkNiqL3JD+AWAVpoyadGFY2yATW5nVSJj9rkspDl+v6hE/hHRrjRMEdpdfiDEkBUVxWaEWkveHj5AzO0RKGXCrSHcKBIfSPKEEaA9PJYwSUEXPX0nNlj8y6RBiUHd5AzCOodq1VvsYfjWE4G6fgEy/zMcxG17B9ZTyX8bV85C5y1S40ZX/kdj+GD1P/zVQA56XStC9h2idJI/z7huz4CxoVvE4=)

</div>
<div class="options-api">

[Thử trên Playground](https://play.vuejs.org/#eNqFkc1uwjAMgF/F6wk0SmHTJNQFtF32AuOwQy+hdSFamkSJ08EQ776EbMAkJKTIf7I/O/Y+ezVm3HvMyoy52gpDi0rh1mhL0GDLvSTYVwqg4cQHw2QDWCRv1Z8H4Db6qwSyHlPkEFUQ4bHixA0OYWckJ4wesZUn0gpeainqz3mVRQzM4S7qKlss9XotEd6laBDu4Y03yIpUE+oB2NJy5QSJwFC8w0iIuXkbMkN9moUZ6HPR/uJDeINSalaYxCjOkBBgxeWEijnayWiOz+AcFaHNeU2ix7QCOiFK4FLCZPzoALnDXHt6Pq7hP0Ii7/EGYuag9itR5yv8FmgH01EIPkUxG8F0eA2bJmut7kbX+pG+6NVq28WTBTN+92PwMDHbSAXQhteCdiVMUpNwwuMassMP8kfAJQ==)

</div>

### CSS Animation {#css-animations}

[CSS animation gốc](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Animations/Using_CSS_animations) được áp dụng tương tự như CSS transition, chỉ khác ở chỗ `*-enter-from` sẽ không bị gỡ ngay sau khi phần tử được chèn vào mà sẽ chờ sự kiện `animationend`.

Với hầu hết CSS animation, ta chỉ cần khai báo chúng trong các class `*-enter-active` và `*-leave-active`. Ví dụ:

```vue-html
<Transition name="bounce">
  <p v-if="show" style="text-align: center;">
    Hello here is some bouncy text!
  </p>
</Transition>
```

```css
.bounce-enter-active {
  animation: bounce-in 0.5s;
}
.bounce-leave-active {
  animation: bounce-in 0.5s reverse;
}
@keyframes bounce-in {
  0% {
    transform: scale(0);
  }
  50% {
    transform: scale(1.25);
  }
  100% {
    transform: scale(1);
  }
}
```

<CssAnimation />

<div class="composition-api">

[Thử trên Playground](https://play.vuejs.org/#eNqNksGOgjAQhl9lJNmoBwRNvCAa97YP4JFLbQZsLG3TDqzG+O47BaOezCYkpfB9/0wHbsm3c4u+w6RIyiC9cgQBqXO7yqjWWU9wA4813KH2toUpo9PKVEZaExg92V/YRmBGvsN5ZcpsTGGfN4St04Iw7qg8dkTWwF5qJc/bKnnYk7hWye5gm0ZjmY0YKwDlwQsTFCnWjGiRpaPtjETG43smHPSpqh9pVQKBrjpyrfCNMilZV8Aqd5cNEF4oFVo1pgCJhtBvnjEAP6i1hRN6BBUg2BZhKHUdvMmjWhYHE9dXY/ygzN4PasqhB75djM2mQ7FUSFI9wi0GCJ6uiHYxVsFUGcgX67CpzP0lahQ9/k/kj9CjDzgG7M94rT1PLLxhQ0D+Na4AFI9QW98WEKTQOMvnLAOwDrD+wC0Xq/Ubusw/sU+QL/45hskk9z8Bddbn)

</div>
<div class="options-api">

[Thử trên Playground](https://play.vuejs.org/#eNqNUs2OwiAQfpWxySZ66I8mXioa97YP4LEXrNNKpEBg2tUY330pqOvJmBBgyPczP1yTb2OyocekTJirrTC0qRSejbYEB2x4LwmulQI4cOLTWbwDWKTeqkcE4I76twSyPcaX23j4zS+WP3V9QNgZyQnHiNi+J9IKtrUU9WldJaMMrGEynlWy2em2lcjyCPMUALazXDlBwtMU79CT9rpXNXp4tGYGhlQ0d7UqAUcXOeI6bluhUtKmhEVhzisgPFPKpWhVCTUqQrt6ygD8oJQajmgRhAOnO4RgdQm8yd0tNzGv/D8x/8Dy10IVCzn4axaTTYNZymsSA8YuciU6PrLL6IKpUFBkS7cKXXwQJfIBPyP6IQ1oHUaB7QkvjfUdcy+wIFB8PeZIYwmNtl0JruYSp8XMk+/TXL7BzbPF8gU6L95hn8D4OUJnktsfM1vavg==)

</div>

### Class Transition Tùy Chỉnh {#custom-transition-classes}

Bạn cũng có thể chỉ định class transition tùy chỉnh bằng cách truyền các prop sau vào `<Transition>`:

- `enter-from-class`
- `enter-active-class`
- `enter-to-class`
- `leave-from-class`
- `leave-active-class`
- `leave-to-class`

Các prop này sẽ ghi đè tên class mặc định. Điều này đặc biệt hữu ích khi bạn muốn kết hợp hệ thống transition của Vue với một thư viện CSS animation có sẵn, chẳng hạn [Animate.css](https://daneden.github.io/animate.css/):

```vue-html
<!-- giả sử Animate.css đã được nạp vào trang -->
<Transition
  name="custom-classes"
  enter-active-class="animate__animated animate__tada"
  leave-active-class="animate__animated animate__bounceOutRight"
>
  <p v-if="show">hello</p>
</Transition>
```

<div class="composition-api">

[Thử trên Playground](https://play.vuejs.org/#eNqNUctuwjAQ/BXXF9oDsZB6ogbRL6hUcbSEjLMhpn7JXtNWiH/vhqS0R3zxPmbWM+szf02pOVXgSy6LyTYhK4A1rVWwPsWM7MwydOzCuhw9mxF0poIKJoZC0D5+stUAeMRc4UkFKcYpxKcEwSenEYYM5b4ixsA2xlnzsVJ8Yj8Mt+LrbTwcHEgxwojCmNxmHYpFG2kaoxO0B2KaWjD6uXG6FCiKj00ICHmuDdoTjD2CavJBCna7KWjZrYK61b9cB5pI93P3sQYDbxXf7aHHccpVMolO7DS33WSQjPXgXJRi2Cl1xZ8nKkjxf0dBFvx2Q7iZtq94j5jKUgjThmNpjIu17ZzO0JjohT7qL+HsvohJWWNKEc/NolncKt6Goar4y/V7rg/wyw9zrLOy)

</div>
<div class="options-api">

[Thử trên Playground](https://play.vuejs.org/#eNqNUcFuwjAM/RUvp+1Ao0k7sYDYF0yaOFZCJjU0LE2ixGFMiH9f2gDbcVKU2M9+tl98Fm8hNMdMYi5U0tEEXraOTsFHho52mC3DuXUAHTI+PlUbIBLn6G4eQOr91xw4ZqrIZXzKVY6S97rFYRqCRabRY7XNzN7BSlujPxetGMvAAh7GtxXLtd/vLSlZ0woFQK0jumTY+FJt7ORwoMLUObEfZtpiSpRaUYPkmOIMNZsj1VhJRWeGMsFmczU6uCOMHd64lrCQ/s/d+uw0vWf+MPuea5Vp5DJ0gOPM7K4Ci7CerPVKhipJ/moqgJJ//8ipxN92NFdmmLbSip45pLmUunOH1Gjrc7ezGKnRfpB4wJO0ZpvkdbJGpyRfmufm+Y4Mxo1oK16n9UwNxOUHwaK3iQ==)

</div>

### Dùng Transition Và Animation Cùng Lúc {#using-transitions-and-animations-together}

Vue cần gắn listener sự kiện để biết khi nào transition kết thúc. Sự kiện đó có thể là `transitionend` hoặc `animationend`, tùy theo loại quy tắc CSS đang được áp dụng. Nếu bạn chỉ dùng một trong hai loại, Vue có thể tự động phát hiện đúng loại.

Tuy nhiên, trong một số trường hợp bạn có thể muốn có cả hai trên cùng một phần tử, ví dụ một CSS animation được Vue kích hoạt đồng thời có thêm hiệu ứng CSS transition khi hover. Trong những trường hợp đó, bạn sẽ phải chỉ rõ loại mà Vue cần quan tâm bằng prop `type`, với giá trị là `animation` hoặc `transition`:

```vue-html
<Transition type="animation">...</Transition>
```

### Transition Lồng Nhau Và Thời Lượng Tường Minh {#nested-transitions-and-explicit-transition-durations}

Dù các class transition chỉ được áp dụng lên phần tử con trực tiếp trong `<Transition>`, ta vẫn có thể tạo transition cho phần tử lồng bên trong bằng bộ chọn CSS lồng nhau:

```vue-html
<Transition name="nested">
  <div v-if="show" class="outer">
    <div class="inner">
      Hello
    </div>
  </div>
</Transition>
```

```css
/* quy tắc nhắm vào phần tử lồng bên trong */
.nested-enter-active .inner,
.nested-leave-active .inner {
  transition: all 0.3s ease-in-out;
}

.nested-enter-from .inner,
.nested-leave-to .inner {
  transform: translateX(30px);
  opacity: 0;
}

/* ... lược bỏ các CSS cần thiết khác */
```

Ta thậm chí có thể thêm độ trễ cho phần tử lồng bên trong ở giai đoạn đi vào, từ đó tạo ra chuỗi animation đi vào lệch nhịp:

```css{3}
/* trì hoãn phần tử lồng bên trong khi vào để tạo hiệu ứng lệch nhịp */
.nested-enter-active .inner {
  transition-delay: 0.25s;
}
```

Tuy nhiên, điều này tạo ra một vấn đề nhỏ. Mặc định, component `<Transition>` sẽ cố tự suy ra thời điểm transition kết thúc bằng cách lắng nghe sự kiện `transitionend` hoặc `animationend` **đầu tiên** trên phần tử gốc đang transition. Với transition lồng nhau, điều ta mong muốn lại là chờ đến khi transition của toàn bộ phần tử con bên trong đều kết thúc.

Trong những trường hợp như vậy, bạn có thể chỉ định thời lượng transition tường minh (tính bằng mili giây) bằng prop `duration` trên component `<Transition>`. Tổng thời lượng này nên khớp với độ trễ cộng với thời lượng transition của phần tử bên trong:

```vue-html
<Transition :duration="550">...</Transition>
```

<NestedTransitions />

[Thử trên Playground](https://play.vuejs.org/#eNqVVd9v0zAQ/leO8LAfrE3HNKSFbgKmSYMHQNAHkPLiOtfEm2NHttN2mvq/c7bTNi1jgFop9t13d9995ziPyfumGc5bTLJkbLkRjQOLrm2uciXqRhsHj2BwBiuYGV3DAUEPcpUrrpUlaKUXcOkBh860eJSrcRqzUDxtHNaNZA5pBzCets5pBe+4FPz+Mk+66Bf+mSdXE12WEsdphMWQiWHKCicoLCtaw/yKIs/PR3kCitVIG4XWYUEJfATFFGIO84GYdRUIyCWzlra6dWg2wA66dgqlts7c+d8tSqk34JTQ6xqb9TjdUiTDOO21TFvrHqRfDkPpExiGKvBITjdl/L40ulVFBi8R8a3P17CiEKrM4GzULIOlFmpQoSgrl8HpKFpX3kFZu2y0BNhJxznvwaJCA1TEYcC4E3MkKp1VIptjZ43E3KajDJiUMBqeWUBmcUBUqJGYOT2GAiV7gJAA9Iy4GyoBKLH2z+N0W3q/CMC2yCCkyajM63Mbc+9z9mfvZD+b071MM23qLC69+j8PvX5HQUDdMC6cL7BOTtQXCJwpas/qHhWIBdYtWGgtDWNttWTmThu701pf1W6+v1Hd8Xbz+k+VQxmv8i7Fv1HZn+g/iv2nRkjzbd6npf/Rkz49DifQ3dLZBBYOJzC4rqgCwsUbmLYlCAUVU4XsCd1NrCeRHcYXb1IJC/RX2hEYCwJTvHYVMZoavbBI09FmU+LiFSzIh0AIXy1mqZiFKaKCmVhiEVJ7GftHZTganUZ56EYLL3FykjhL195MlMM7qxXdmEGDPOG6boRE86UJVPMki+p4H01WLz4Fm78hSdBo5xXy+yfsd3bpbXny1SA1M8c82fgcMyW66L75/hmXtN44a120ktDPOL+h1bL1HCPsA42DaPdwge3HcO/TOCb2ZumQJtA15Yl65Crg84S+BdfPtL6lezY8C3GkZ7L6Bc1zNR0=)

Nếu cần, bạn cũng có thể chỉ định riêng thời lượng cho lúc vào và lúc rời khỏi bằng một object:

```vue-html
<Transition :duration="{ enter: 500, leave: 800 }">...</Transition>
```

### Cân Nhắc Về Hiệu Năng {#performance-considerations}

Bạn có thể nhận thấy rằng các animation ở trên chủ yếu dùng những thuộc tính như `transform` và `opacity`. Những thuộc tính này animate hiệu quả vì:

1. Chúng không ảnh hưởng đến bố cục tài liệu trong quá trình animation, nên không kích hoạt phép tính layout CSS tốn kém ở mỗi frame.

2. Phần lớn trình duyệt hiện đại có thể tận dụng tăng tốc phần cứng của GPU khi animate `transform`.

Ngược lại, những thuộc tính như `height` hoặc `margin` sẽ kích hoạt layout CSS, nên việc animate chúng tốn kém hơn nhiều và cần được dùng cẩn trọng.

## Hook JavaScript {#javascript-hooks}

Bạn có thể can thiệp vào quá trình transition bằng JavaScript thông qua việc lắng nghe sự kiện trên component `<Transition>`:

```vue-html
<Transition
  @before-enter="onBeforeEnter"
  @enter="onEnter"
  @after-enter="onAfterEnter"
  @enter-cancelled="onEnterCancelled"
  @before-leave="onBeforeLeave"
  @leave="onLeave"
  @after-leave="onAfterLeave"
  @leave-cancelled="onLeaveCancelled"
>
  <!-- ... -->
</Transition>
```

<div class="composition-api">

```js
// được gọi trước khi phần tử được chèn vào DOM.
// dùng để thiết lập trạng thái "enter-from" cho phần tử
function onBeforeEnter(el) {}

// được gọi sau một frame kể từ khi phần tử được chèn vào.
// dùng để bắt đầu animation đi vào.
function onEnter(el, done) {
  // gọi callback done để báo transition kết thúc
  // là tùy chọn nếu dùng kèm CSS
  done()
}

// được gọi khi transition đi vào kết thúc.
function onAfterEnter(el) {}

// được gọi khi transition đi vào bị hủy trước khi hoàn tất.
function onEnterCancelled(el) {}

// được gọi trước leave hook.
// Đa phần trường hợp, bạn chỉ cần dùng leave hook
function onBeforeLeave(el) {}

// được gọi khi transition rời khỏi bắt đầu.
// dùng để bắt đầu animation rời khỏi.
function onLeave(el, done) {
  // gọi callback done để báo transition kết thúc
  // là tùy chọn nếu dùng kèm CSS
  done()
}

// được gọi khi transition rời khỏi kết thúc và
// phần tử đã bị xóa khỏi DOM.
function onAfterLeave(el) {}

// chỉ khả dụng với transition dùng v-show
function onLeaveCancelled(el) {}
```

</div>
<div class="options-api">

```js
export default {
  // ...
  methods: {
    // được gọi trước khi phần tử được chèn vào DOM.
    // dùng để thiết lập trạng thái "enter-from" cho phần tử
    onBeforeEnter(el) {},

    // được gọi sau một frame kể từ khi phần tử được chèn vào.
    // dùng để bắt đầu animation.
    onEnter(el, done) {
      // gọi callback done để báo transition kết thúc
      // là tùy chọn nếu dùng kèm CSS
      done()
    },

    // được gọi khi transition đi vào kết thúc.
    onAfterEnter(el) {},

    // được gọi khi transition đi vào bị hủy trước khi hoàn tất.
    onEnterCancelled(el) {},

    // được gọi trước leave hook.
    // Đa phần trường hợp, bạn chỉ cần dùng leave hook.
    onBeforeLeave(el) {},

    // được gọi khi transition rời khỏi bắt đầu.
    // dùng để bắt đầu animation rời khỏi.
    onLeave(el, done) {
      // gọi callback done để báo transition kết thúc
      // là tùy chọn nếu dùng kèm CSS
      done()
    },

    // được gọi khi transition rời khỏi kết thúc và
    // phần tử đã bị xóa khỏi DOM.
    onAfterLeave(el) {},

    // chỉ khả dụng với transition dùng v-show
    onLeaveCancelled(el) {}
  }
}
```

</div>

Các hook này có thể được dùng kết hợp với CSS transition / animation hoặc dùng riêng.

Khi dùng transition chỉ bằng JavaScript, thường nên thêm prop `:css="false"`. Điều này nói rõ với Vue rằng nó nên bỏ qua bước tự phát hiện CSS transition. Ngoài việc hiệu quả hơn một chút, nó còn ngăn các quy tắc CSS vô tình can thiệp vào transition:

```vue-html{3}
<Transition
  ...
  :css="false"
>
  ...
</Transition>
```

Với `:css="false"`, ta cũng hoàn toàn chịu trách nhiệm điều khiển thời điểm transition kết thúc. Trong trường hợp này, callback `done` là bắt buộc cho hook `@enter` và `@leave`. Nếu không, hook sẽ được gọi đồng bộ và transition sẽ kết thúc ngay lập tức.

Đây là một demo dùng [thư viện GSAP](https://gsap.com/) để thực hiện animation. Tất nhiên, bạn cũng có thể dùng thư viện animation khác như [Anime.js](https://animejs.com/) hoặc [Motion One](https://motion.dev/):

<JsHooks />

<div class="composition-api">

[Thử trên Playground](https://play.vuejs.org/#eNqNVMtu2zAQ/JUti8I2YD3i1GigKmnaorcCveTQArpQFCWzlkiCpBwHhv+9Sz1qKYckJ3FnlzvD2YVO5KvW4aHlJCGpZUZoB5a7Vt9lUjRaGQcnMLyEM5RGNbDA0sX/VGWpHnB/xEQmmZIWe+zUI9z6m0tnWr7ymbKVzAklQclvvFSG/5COmyWvV3DKJHTdQiRHZN0jAJbRmv9OIA432/UE+jODlKZMuKcErnx8RrazP8woR7I1FEryKaVTU8aiNdRfwWZTQtQwi1HAGF/YB4BTyxNY8JpaJ1go5K/WLTfhdg1Xq8V4SX5Xja65w0ovaCJ8Jvsnpwc+l525F2XH4ac3Cj8mcB3HbxE9qnvFMRzJ0K3APuhIjPefmTTyvWBAGvWbiDuIgeNYRh3HCCDNW+fQmHtWC7a/zciwaO/8NyN3D6qqap5GfVnXAC89GCqt8Bp77vu827+A+53AJrOFzMhQdMnO8dqPpMO74Yx4wqxFtKS1HbBOMdIX4gAMffVp71+Qq2NG4BCIcngBKk8jLOvfGF30IpBGEwcwtO6p9sdwbNXPIadsXxnVyiKB9x83+c3N9WePN9RUQgZO6QQ2sT524KMo3M5Pf4h3XFQ7NwFyZQpuAkML0doEtvEHhPvRDPRkTfq/QNDgRvy1SuIvpFOSDQmbkWTckf7hHsjIzjltkyhqpd5XIVNN5HNfGlW09eAcMp3J+R+pEn7L)

</div>
<div class="options-api">

[Thử trên Playground](https://play.vuejs.org/#eNqNVFFvmzAQ/is3pimNlABNF61iaddt2tukvfRhk/xiwIAXsJF9pKmq/PedDTSwh7ZSFLjvzvd9/nz4KfjatuGhE0ES7GxmZIu3TMmm1QahtLyFwugGFu51wRQAU+Lok7koeFcjPDk058gvlv07gBHYGTVGALbSDwmg6USPnNzjtHL/jcBK5zZxxQwZavVNFNqIHwqF8RUAWs2jn4IffCfqQz+mik5lKLWi3GT1hagHRU58aAUSshpV2YzX4ncCcbjZDp099GcG6ZZnEh8TuPR8S0/oTJhQjmQryLUSU0rUU8a8M9wtoWZTQtIwi0nAGJ/ZB0BwKxJYiJpblFko1a8OLzbhdgWXy8WzP99109YCqdIJmgifyfYuzmUzfFF2HH56o/BjAldx/BbRo7pXHKMjGbrl1IcciWn9fyaNfC8YsIueR5wCFFTGUVAEsEs7pOmDu6yW2f6GBW5o4QbeuScLbu91WdZiF/VlvgEtujdcWek09tx3qZ+/tXAzQU1mA8mCoeicneO1OxKP9yM+4ElmLaEFr+2AecVEn8sDZOSrSzv/1qk+sgAOa1kMOyDlu4jK+j1GZ70E7KKJAxRafKzdazi26s8h5dm+NLpTeQLvP27S6+urz/7T5aaUao26TWATt0cPPsgcK3f6Q1wJWVY4AVJtcmHWhueyo89+G38guD+agT5YBf39s25oIv5arehu8krYkLAs8BeG86DfuANYUCG2NomiTrX7Msx0E7ncl0bnXT04566M4PQPykWaWw==)

</div>

## Transition Có Thể Tái Sử Dụng {#reusable-transitions}

Transition có thể được tái sử dụng thông qua hệ thống component của Vue. Để tạo transition có thể tái sử dụng, ta có thể tạo một component bao bọc `<Transition>` rồi truyền tiếp nội dung slot xuống:

```vue [MyTransition.vue]
<script>
// logic JavaScript hooks...
</script>

<template>
  <!-- bọc component Transition dựng sẵn -->
  <Transition
    name="my-transition"
    @enter="onEnter"
    @leave="onLeave">
    <slot></slot> <!-- truyền tiếp nội dung slot -->
  </Transition>
</template>

<style>
/*
  CSS cần thiết...
  Lưu ý: tránh dùng <style scoped> ở đây vì
  nó không áp dụng lên nội dung slot.
*/
</style>
```

Giờ `MyTransition` có thể được import và dùng giống như phiên bản dựng sẵn:

```vue-html
<MyTransition>
  <div v-if="show">Hello</div>
</MyTransition>
```

## Transition Khi Xuất Hiện Ban Đầu {#transition-on-appear}

Nếu bạn cũng muốn áp dụng transition cho lần render đầu tiên của một node, hãy thêm prop `appear`:

```vue-html
<Transition appear>
  ...
</Transition>
```

## Transition Giữa Các Phần Tử {#transition-between-elements}

Ngoài việc bật / tắt một phần tử bằng `v-if` / `v-show`, ta cũng có thể tạo transition giữa hai phần tử bằng `v-if` / `v-else` / `v-else-if`, miễn là luôn bảo đảm chỉ có một phần tử được hiển thị tại một thời điểm:

```vue-html
<Transition>
  <button v-if="docState === 'saved'">Edit</button>
  <button v-else-if="docState === 'edited'">Save</button>
  <button v-else-if="docState === 'editing'">Cancel</button>
</Transition>
```

<BetweenElements />

[Thử trên Playground](https://play.vuejs.org/#eNqdk8tu2zAQRX9loI0SoLLcFN2ostEi6BekmwLa0NTYJkKRBDkSYhj+9wxJO3ZegBGu+Lhz7syQ3Bd/nJtNIxZN0QbplSMISKNbdkYNznqCPXhcwwHW3g5QsrTsTGekNYGgt/KBBCEsouimDGLCvrztTFtnGGN4QTg4zbK4ojY4YSDQTuOiKwbhN8pUXm221MDd3D11xfJeK/kIZEHupEagrbfjZssxzAgNs5nALIC2VxNILUJg1IpMxWmRUAY9U6IZ2/3zwgRFyhowYoieQaseq9ElDaTRrkYiVkyVWrPiXNdiAcequuIkPo3fMub5Sg4l9oqSevmXZ22dwR8YoQ74kdsL4Go7ZTbR74HT/KJfJlxleGrG8l4YifqNYVuf251vqOYr4llbXz4C06b75+ns1a3BPsb0KrBy14Aymnerlbby8Vc8cTajG35uzFITpu0t5ufzHQdeH6LBsezEO0eJVbB6pBiVVLPTU6jQEPpKyMj8dnmgkQs+HmQcvVTIQK1hPrv7GQAFt9eO9Bk6fZ8Ub52Qiri8eUo+4dbWD02exh79v/nBP+H2PStnwz/jelJ1geKvk/peHJ4BoRZYow==)

## Chế Độ Transition {#transition-modes}

Trong ví dụ trước, phần tử đi vào và phần tử rời khỏi được animate cùng lúc, và ta buộc phải đặt `position: absolute` cho chúng để tránh vấn đề bố cục khi cả hai cùng hiện diện trong DOM.

Tuy nhiên, trong một số trường hợp đây không phải là lựa chọn khả thi, hoặc đơn giản là không phải hành vi mong muốn. Ta có thể muốn animate phần tử rời khỏi trước, sau đó chỉ chèn phần tử đi vào **sau khi** animation rời khỏi đã kết thúc. Nếu điều phối kiểu animation này bằng tay thì sẽ rất phức tạp, nhưng may mắn là ta có thể bật hành vi đó bằng cách truyền prop `mode` cho `<Transition>`:

```vue-html
<Transition mode="out-in">
  ...
</Transition>
```

Đây là demo trước đó nhưng với `mode="out-in"`:

<BetweenElements mode="out-in" />

`<Transition>` cũng hỗ trợ `mode="in-out"`, dù nó ít được dùng hơn nhiều.

## Transition Giữa Các Component {#transition-between-components}

`<Transition>` cũng có thể được dùng bao quanh [component động](/guide/essentials/component-basics#dynamic-components):

```vue-html
<Transition name="fade" mode="out-in">
  <component :is="activeComponent"></component>
</Transition>
```

<BetweenComponents />

<div class="composition-api">

[Thử trên Playground](https://play.vuejs.org/#eNqtksFugzAMhl/F4tJNKtDLLoxWKnuDacdcUnC3SCGJiMmEqr77EkgLbXfYYZyI8/v77dinZG9M5npMiqS0dScMgUXqzY4p0RrdEZzAfnEp9fc7HuEMx063sPIZq6viTbdmHy+yfDwF5K2guhFUUcBUnkNvcelBGrjTooHaC7VCRXBAoT6hQTRyAH2w2DlsmKq1sgS8JuEwUCfxdgF7Gqt5ZqrMp+58X/5A2BrJCcOJSskPKP0v+K8UyvQENBjcsqTjjdAsAZe2ukHpI3dm/q5wXPZBPFqxZAf7gCrzGfufDlVwqB4cPjqurCChFSjeBvGRN+iTA9afdE+pUD43FjG/bSHsb667Mr9qJot89vCBMl8+oiotDTL8ZsE39UnYpRN0fQlK5A5jEE6BSVdiAdrwWtAAm+zFAnKLr0ydA3pJDDt0x/PrMrJifgGbKdFPfCwpWU+TuWz5omzfVCNcfJJ5geL8pqtFn5E07u7fSHFOj6TzDyUDNEM=)

</div>
<div class="options-api">

[Thử trên Playground](https://play.vuejs.org/#eNqtks9ugzAMxl/F4tJNamGXXVhWqewVduSSgStFCkkUDFpV9d0XJyn9t8MOkxBg5/Pvi+Mci51z5TxhURdi7LxytG2NGpz1BB92cDvYezvAqqxixNLVjaC5ETRZ0Br8jpIe93LSBMfWAHRBYQ0aGms4Jvw6Q05rFvSS5NNzEgN4pMmbcwQgO1Izsj5CalhFRLDj1RN/wis8olpaCQHh4LQk5IiEll+owy+XCGXcREAHh+9t4WWvbFvAvBlsjzpk7gx5TeqJtdG4LbawY5KoLtR/NGjYoHkw+PTSjIqUNWDkwOK97DHUMjVEdqKNMqE272E5dajV+JvpVlSLJllUF4+QENX1ERox0kHzb8m+m1CEfpOgYYgpqVHOmJNpgLQQa7BOdooO8FK+joByxLc4tlsiX6s7HtnEyvU1vKTCMO+4pWKdBnO+0FfbDk31as5HsvR+Hl9auuozk+J1/hspz+mRdPoBYtonzg==)

</div>

## Transition Động {#dynamic-transitions}

Các prop của `<Transition>` như `name` cũng có thể là động. Điều này cho phép ta áp dụng các transition khác nhau một cách động dựa trên sự thay đổi state:

```vue-html
<Transition :name="transitionName">
  <!-- ... -->
</Transition>
```

Điều này hữu ích khi bạn đã định nghĩa sẵn các CSS transition / animation theo quy ước class transition của Vue và muốn chuyển đổi giữa chúng.

Bạn cũng có thể áp dụng hành vi khác nhau trong các JavaScript transition hook dựa trên state hiện tại của component. Cuối cùng, cách tối hậu để tạo transition động là thông qua [component transition có thể tái sử dụng](#reusable-transitions) nhận props để thay đổi bản chất của transition sẽ dùng. Nghe có vẻ "bay bổng", nhưng giới hạn thực sự gần như chỉ là trí tưởng tượng của bạn.

## Transition Với Thuộc Tính `key` {#transitions-with-the-key-attribute}

Đôi khi bạn cần buộc một phần tử DOM phải render lại để transition có thể xảy ra.

Hãy lấy component bộ đếm này làm ví dụ:

<div class="composition-api">

```vue
<script setup>
import { ref } from 'vue';
const count = ref(0);

setInterval(() => count.value++, 1000);
</script>

<template>
  <Transition>
    <span :key="count">{{ count }}</span>
  </Transition>
</template>
```

</div>
<div class="options-api">

```vue
<script>
export default {
  data() {
    return {
      count: 1,
      interval: null 
    }
  },
  mounted() {
    this.interval = setInterval(() => {
      this.count++;
    }, 1000)
  },
  beforeDestroy() {
    clearInterval(this.interval)
  }
}
</script>

<template>
  <Transition>
    <span :key="count">{{ count }}</span>
  </Transition>
</template>
```

</div>

Nếu ta bỏ thuộc tính `key`, chỉ riêng text node sẽ được cập nhật và vì thế không có transition nào xảy ra. Nhưng khi có `key`, Vue sẽ hiểu rằng cần tạo một phần tử `span` mới mỗi khi `count` thay đổi, và từ đó component `Transition` có hai phần tử khác nhau để tạo transition qua lại.

<div class="composition-api">

[Thử trên Playground](https://play.vuejs.org/#eNp9UsFu2zAM/RVCl6Zo4nhYd/GcAtvQQ3fYhq1HXTSFydTKkiDJbjLD/z5KMrKgLXoTHx/5+CiO7JNz1dAja1gbpFcuQsDYuxtuVOesjzCCxx1MsPO2gwuiXnzkhhtpTYggbW8ibBJlUV/mBJXfmYh+EHqxuITNDYzcQGFWBPZ4dUXEaQnv6jrXtOuiTJoUROycFhEpAmi3agCpRQgbzp68cA49ZyV174UJKiprckxIcMJA84hHImc9oo7jPOQ0kQ4RSvH6WXW7JiV6teszfQpDPGqEIK3DLSGpQbazsyaugvqLDVx77JIhbqp5wsxwtrRvPFI7NWDhEGtYYVrQSsgELzOiUQw4I2Vh8TRgA9YJqeIR6upDABQh9TpTAPE7WN3HlxLp084Foi3N54YN1KWEVpOMkkO2ZJHsmp3aVw/BGjqMXJE22jml0X93STRw1pReKSe0tk9fMxZ9nzwVXP5B+fgK/hAOCePsh8dAt4KcnXJR+D3S16X07a9veKD3KdnZba+J/UbyJ+Zl0IyF9rk3Wxr7jJenvcvnrcz+PtweItKuZ1Np0MScMp8zOvkvb1j/P+776jrX0UbZ9A+fYSTP)

</div>
<div class="options-api">

[Thử trên Playground](https://play.vuejs.org/#eNp9U8tu2zAQ/JUFTwkSyw6aXlQ7QB85pIe2aHPUhZHWDhOKJMiVYtfwv3dJSpbbBgEMWJydndkdUXvx0bmi71CUYhlqrxzdVAa3znqCBtey0wT7ygA0kuTZeX4G8EidN+MJoLadoRKuLkdAGULfS12C6bSGDB/i3yFx2tiAzaRIjyoUYxesICDdDaczZq1uJrNETY4XFx8G5Uu4WiwW55PBA66txy8YyNvdZFNrlP4o/Jdpbq4M/5bzYxZ8IGydloR8Alg2qmcVGcKqEi9eOoe+EqnExXsvTVCkrBkQxoKTBspn3HFDmprp+32ODA4H9mLCKDD/R2E5Zz9+Ws5PpuBjoJ1GCLV12DASJdKGa2toFtRvLOHaY8vx8DrFMGdiOJvlS48sp3rMHGb1M4xRzGQdYU6REY6rxwHJGdJxwBKsk7WiHSyK9wFQhqh14gDyIVjd0f8Wa2/bUwOyWXwQLGGRWzicuChvKC4F8bpmrTbFU7CGL2zqiJm2Tmn03100DZUox5ddCam1ffmaMPJd3Cnj9SPWz6/gT2EbsUr88Bj4VmAljjWSfoP88mL59tc33PLzsdjaptPMfqP4E1MYPGOmfepMw2Of8NK0d238+JTZ3IfbLSFnPSwVB53udyX4q/38xurTuO+K6/Fqi8MffqhR/A==)

</div>

---

**Liên quan**

- [Tài liệu API của `<Transition>`](/api/built-in-components#transition)
