# Xử lý sự kiện {#event-handling}

<div class="options-api">
  <VueSchoolLink href="https://vueschool.io/lessons/user-events-in-vue-3" title="Bài học miễn phí về Events trong Vue.js"/>
</div>

<div class="composition-api">
  <VueSchoolLink href="https://vueschool.io/lessons/vue-fundamentals-capi-user-events-in-vue-3" title="Bài học miễn phí về Events trong Vue.js"/>
</div>

## Lắng nghe sự kiện {#listening-to-events}

Chúng ta có thể dùng directive `v-on`, thường được viết tắt là ký hiệu `@`, để lắng nghe các sự kiện DOM và chạy một số JavaScript khi chúng được kích hoạt. Cách dùng sẽ là `v-on:click="handler"` hoặc với cú pháp rút gọn là `@click="handler"`.

Giá trị handler có thể là một trong các dạng sau:

1. **Inline handlers:** JavaScript viết trực tiếp sẽ được thực thi khi sự kiện được kích hoạt (tương tự attribute `onclick` gốc).

2. **Method handlers:** Tên hoặc đường dẫn trỏ tới một method được định nghĩa trong component.

## Inline Handlers {#inline-handlers}

Inline handler thường được dùng trong các trường hợp đơn giản, ví dụ:

<div class="composition-api">

```js
const count = ref(0)
```

</div>
<div class="options-api">

```js
data() {
  return {
    count: 0
  }
}
```

</div>

```vue-html
<button @click="count++">Thêm 1</button>
<p>Count là: {{ count }}</p>
```

<div class="composition-api">

[Thử trong Playground](https://play.vuejs.org/#eNo9jssKgzAURH/lko0tgrbbEqX+Q5fZaLxiqHmQ3LgJ+fdqFZcD58xMYp1z1RqRvRgP0itHEJCia4VR2llPkMDjBBkmbzUUG1oII4y0JhBIGw2hh2Znbo+7MLw+WjZ/C4TaLT3hnogPkcgaeMtFyW8j2GmXpWBtN47w5PWBHLhrPzPCKfWDXRHmPsCAaOBfgSOkdH3IGUhpDBWv9/e8vsZZ/gFFhFJN)

</div>
<div class="options-api">

[Thử trong Playground](https://play.vuejs.org/#eNo9jcEKgzAQRH9lyKlF0PYqqdR/6DGXaLYo1RjiRgrivzepIizLzu7sm1XUzuVLIFEKObe+d1wpS183eYahtw4DY1UWMJr15ZpmxYAnDt7uF0BxOwXL5Evc0kbxlmyxxZLFyY2CaXSDZkqKZROYJ4tnO/Tt56HEgckyJaraGNxlsVt2u6teHeF40s20EDo9oyGy+CPIYF1xULBt4H6kOZeFiwBZnOFi+wH0B1hk)

</div>

## Method Handlers {#method-handlers}

Tuy nhiên, logic cho nhiều event handler sẽ phức tạp hơn, và có thể không phù hợp với inline handler. Vì vậy `v-on` cũng có thể nhận tên hoặc đường dẫn tới một method của component mà bạn muốn gọi.

Ví dụ:

<div class="composition-api">

```js
const name = ref('Vue.js')

function greet(event) {
  alert(`Hello ${name.value}!`)
  // `event` là DOM event gốc
  if (event) {
    alert(event.target.tagName)
  }
}
```

</div>
<div class="options-api">

```js
data() {
  return {
    name: 'Vue.js'
  }
},
methods: {
  greet(event) {
    // `this` bên trong method trỏ tới instance hiện tại
    alert(`Hello ${this.name}!`)
    // `event` là DOM event gốc
    if (event) {
      alert(event.target.tagName)
    }
  }
}
```

</div>

```vue-html
<!-- `greet` là tên method được định nghĩa ở trên -->
<button @click="greet">Greet</button>
```

<div class="composition-api">

[Thử trong Playground](https://play.vuejs.org/#eNpVj0FLxDAQhf/KMwjtXtq7dBcFQS/qzVMOrWFao2kSkkkvpf/dJIuCEBgm771vZnbx4H23JRJ3YogqaM+IxMlfpNWrd4GxI9CMA3NwK5psbaSVVjkbGXZaCediaJv3RN1XbE5FnZNVrJ3FEoi4pY0sn7BLC0yGArfjMxnjcLsXQrdNJtFxM+Ys0PcYa2CEjuBPylNYb4THtxdUobj0jH/YX3D963gKC5WyvGZ+xR7S5jf01yPzeblhWr2ZmErHw0dizivfK6PV91mKursUl6dSh/4qZ+vQ/+XE8QODonDi)

</div>
<div class="options-api">

[Thử trong Playground](https://play.vuejs.org/#eNplUE1LxDAQ/StjEbYL0t5LXRQEvag3Tz00prNtNE1CMilC6X83SUkRhJDJfLz3Jm8tHo2pFo9FU7SOW2Ho0in8MdoSDHhlXhKsnQIYGLHyvL8BLJK3KmcAis3YwOnDY/XlTnt1i2G7i/eMNOnBNRkwWkQqcUFFByVAXUNPk3A9COXEgBkGRgtFDkgDTQjcWxuAwDiJBeMsMcUxszCJlsr+BaXUcLtGwiqut930579KST1IBd5Aqlgie3p/hdTIk+IK//bMGqleEbMjxjC+BZVDIv0+m9CpcNr6MDgkhLORjDBm1H56Iq3ggUvBv++7IhnUFZfnGNt6b4fRtj5wxfYL9p+Sjw==)

</div>

Một method handler tự động nhận object DOM Event gốc đã kích hoạt nó - trong ví dụ trên, chúng ta có thể truy cập phần tử phát ra sự kiện thông qua `event.target`.

<div class="composition-api">

Xem thêm: [Typing Event Handlers](/guide/typescript/composition-api#typing-event-handlers)

</div>
<div class="options-api">

Xem thêm: [Typing Event Handlers](/guide/typescript/options-api#typing-event-handlers)

</div>

### Phân biệt method và inline {#method-vs-inline-detection}

Template compiler phân biệt method handler bằng cách kiểm tra xem chuỗi giá trị của `v-on` có phải là một JavaScript identifier hợp lệ hoặc đường dẫn truy cập property hay không. Ví dụ, `foo`, `foo.bar` và `foo['bar']` được xem là method handler, trong khi `foo()` và `count++` được xem là inline handler.

## Gọi method trong inline handler {#calling-methods-in-inline-handlers}

Thay vì bind trực tiếp tới tên method, chúng ta cũng có thể gọi method trong inline handler. Điều này cho phép truyền tham số tùy chỉnh thay vì event mặc định:

<div class="composition-api">

```js
function say(message) {
  alert(message)
}
```

</div>
<div class="options-api">

```js
methods: {
  say(message) {
    alert(message)
  }
}
```

</div>

```vue-html
<button @click="say('hello')">Say hello</button>
<button @click="say('bye')">Say bye</button>
```

<div class="composition-api">

[Thử trong Playground](https://play.vuejs.org/#eNp9jTEOwjAMRa8SeSld6I5CBWdg9ZJGBiJSN2ocpKjq3UmpFDGx+Vn//b/ANYTjOxGcQEc7uyAqkqTQI98TW3ETq2jyYaQYzYNatSArZTzNUn/IK7Ludr2IBYTG4I3QRqKHJFJ6LtY7+zojbIXNk7yfmhahv5msvqS7PfnHGjJVp9w/hu7qKKwfEd1NSg==)

</div>
<div class="options-api">

[Thử trong Playground](https://play.vuejs.org/#eNptjUEKwjAQRa8yZFO7sfsSi57B7WzGdjTBtA3NVC2ldzehEFwIw8D7vM9f1cX742tmVSsd2sl6aXDgjx8ngY7vNDuBFQeAnsWMXagToQAEWg49h0APLncDAIUcT5LzlKJsqRBfPF3ljQjCvXcknEj0bRYZBzi3zrbPE6o0UBhblKiaKy1grK52J/oA//23IcmNBD8dXeVBtX0BF0pXsg==)

</div>

## Truy cập tham số event trong inline handler {#accessing-event-argument-in-inline-handlers}

Đôi khi chúng ta cũng cần truy cập DOM event gốc trong inline handler. Bạn có thể truyền nó vào method bằng biến đặc biệt `$event`, hoặc dùng arrow function:

```vue-html
<!-- dùng biến đặc biệt $event -->
<button @click="warn('Form chưa thể submit.', $event)">
  Submit
</button>

<!-- dùng arrow function inline -->
<button @click="(event) => warn('Form chưa thể submit.', event)">
  Submit
</button>
```

<div class="composition-api">

```js
function warn(message, event) {
  // giờ chúng ta có thể truy cập event gốc
  if (event) {
    event.preventDefault()
  }
  alert(message)
}
```

</div>
<div class="options-api">

```js
methods: {
  warn(message, event) {
    // giờ chúng ta có thể truy cập event gốc
    if (event) {
      event.preventDefault()
    }
    alert(message)
  }
}
```

</div>

## Event Modifiers {#event-modifiers}

Nhu cầu gọi `event.preventDefault()` hoặc `event.stopPropagation()` trong handler là rất phổ biến. Mặc dù có thể làm điều này trong method, sẽ tốt hơn nếu method chỉ tập trung vào logic dữ liệu thay vì chi tiết DOM.

Để giải quyết vấn đề này, Vue cung cấp **event modifiers** cho `v-on`. Nhớ rằng modifier là hậu tố của directive được biểu thị bằng dấu chấm.

* `.stop`
* `.prevent`
* `.self`
* `.capture`
* `.once`
* `.passive`

```vue-html
<!-- dừng propagation của sự kiện click -->
<a @click.stop="doThis"></a>

<!-- sự kiện submit sẽ không reload trang -->
<form @submit.prevent="onSubmit"></form>

<!-- có thể chain nhiều modifier -->
<a @click.stop.prevent="doThat"></a>

<!-- chỉ dùng modifier -->
<form @submit.prevent></form>

<!-- chỉ trigger nếu event.target là chính phần tử -->
<div @click.self="doThat">...</div>
```

::: tip
Thứ tự modifier quan trọng vì code được tạo ra theo thứ tự đó. Vì vậy `@click.prevent.self` sẽ ngăn hành vi mặc định của click trên phần tử và cả phần tử con, trong khi `@click.self.prevent` chỉ ngăn trên chính phần tử đó.
:::

Các modifier `.capture`, `.once`, và `.passive` tương ứng với các option của `addEventListener`:

```vue-html
<!-- dùng capture mode -->
<div @click.capture="doThis">...</div>

<!-- chỉ trigger một lần -->
<a @click.once="doThis"></a>

<!-- scroll sẽ xảy ra ngay -->
<div @scroll.passive="onScroll">...</div>
```

Modifier `.passive` thường dùng với touch event để cải thiện hiệu năng trên mobile.

::: tip
Không dùng `.passive` và `.prevent` cùng nhau, vì `.passive` đã báo với trình duyệt rằng bạn không có ý định ngăn hành vi mặc định, và sẽ gây warning.
:::

## Key Modifiers {#key-modifiers}

Khi lắng nghe keyboard event, chúng ta thường cần kiểm tra phím cụ thể. Vue cho phép thêm key modifier:

```vue-html
<!-- chỉ gọi submit khi nhấn Enter -->
<input @keyup.enter="submit" />
```

Bạn có thể dùng bất kỳ tên phím hợp lệ từ `KeyboardEvent.key`, chuyển sang kebab-case:

```vue-html
<input @keyup.page-down="onPageDown" />
```

Trong ví dụ trên, handler chỉ được gọi khi `$event.key` là `'PageDown'`.

### Key Aliases {#key-aliases}

Vue cung cấp alias cho các phím phổ biến:

* `.enter`
* `.tab`
* `.delete` (bao gồm Delete và Backspace)
* `.esc`
* `.space`
* `.up`
* `.down`
* `.left`
* `.right`

### System Modifier Keys {#system-modifier-keys}

Bạn có thể dùng các modifier sau để chỉ trigger khi phím tương ứng được nhấn:

* `.ctrl`
* `.alt`
* `.shift`
* `.meta`

::: tip Lưu ý
Trên bàn phím Macintosh, meta là phím command (⌘). Trên bàn phím Windows, meta là phím Windows (⊞). Trên bàn phím Sun Microsystems, meta được ký hiệu là kim cương đặc (◆). Trên một số bàn phím, đặc biệt là MIT và Lisp machine keyboards và các loại kế thừa như Knight keyboard, space-cadet keyboard, meta được gán nhãn 'META'. Trên bàn phím Symbolics, meta được gán nhãn 'META' hoặc 'Meta'.
:::

Ví dụ:

```vue-html
<!-- Alt + Enter -->
<input @keyup.alt.enter="clear" />

<!-- Ctrl + Click -->
<div @click.ctrl="doSomething">Do something</div>
```

::: tip
Modifier key khác với key thông thường. Với `keyup`, chúng phải được giữ khi event xảy ra. Ví dụ `keyup.ctrl` chỉ trigger khi thả một phím trong khi vẫn giữ Ctrl.
:::

### Modifier `.exact` {#exact-modifier}

Modifier `.exact` cho phép kiểm soát chính xác tổ hợp phím:

```vue-html
<!-- vẫn trigger nếu Alt hoặc Shift cũng được nhấn -->
<button @click.ctrl="onClick">A</button>

<!-- chỉ trigger khi chỉ có Ctrl -->
<button @click.ctrl.exact="onCtrlClick">A</button>

<!-- chỉ trigger khi không có modifier -->
<button @click.exact="onClick">A</button>
```

## Mouse Button Modifiers {#mouse-button-modifiers}

* `.left`
* `.right`
* `.middle`

Các modifier này giới hạn handler theo nút chuột cụ thể.

Lưu ý rằng tên modifier `.left`, `.right`, và `.middle` dựa trên bố cục chuột phải tay thông thường, nhưng thực ra đại diện cho các trigger sự kiện 'main' (chính), 'secondary' (phụ), và 'auxiliary' (trợ), chứ không phải là các nút vật lý.
