# Xử lý sự kiện {#event-handling}

<div class="options-api">
  <VueSchoolLink href="https://vueschool.io/lessons/user-events-in-vue-3" title="Bài học miễn phí về xử lý sự kiện trong Vue.js"/>
</div>

<div class="composition-api">
  <VueSchoolLink href="https://vueschool.io/lessons/vue-fundamentals-capi-user-events-in-vue-3" title="Bài học miễn phí về xử lý sự kiện trong Vue.js"/>
</div>

## Lắng nghe sự kiện {#listening-to-events}

Chúng ta có thể dùng directive `v-on`, thường được viết tắt thành ký hiệu `@`, để lắng nghe sự kiện DOM và chạy một đoạn JavaScript khi sự kiện được kích hoạt. Cách dùng là `v-on:click="handler"` hoặc dạng viết tắt là `@click="handler"`.

Giá trị của handler có thể là một trong hai kiểu sau:

1. **Inline handlers:** JavaScript viết trực tiếp sẽ được chạy khi sự kiện được kích hoạt, tương tự thuộc tính `onclick` gốc.

2. **Method handlers:** Tên property hoặc đường dẫn trỏ tới một method được định nghĩa trên component.

## Inline handlers {#inline-handlers}

Inline handler thường được dùng trong những trường hợp đơn giản, ví dụ:

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
<p>Giá trị hiện tại: {{ count }}</p>
```

<div class="composition-api">

[Thử trên Playground](https://play.vuejs.org/#eNo9jssKgzAURH/lko0tgrbbEqX+Q5fZaLxiqHmQ3LgJ+fdqFZcD58xMYp1z1RqRvRgP0itHEJCia4VR2llPkMDjBBkmbzUUG1oII4y0JhBIGw2hh2Znbo+7MLw+WjZ/C4TaLT3hnogPkcgaeMtFyW8j2GmXpWBtN47w5PWBHLhrPzPCKfWDXRHmPsCAaOBfgSOkdH3IGUhpDBWv9/e8vsZZ/gFFhFJN)

</div>
<div class="options-api">

[Thử trên Playground](https://play.vuejs.org/#eNo9jcEKgzAQRH9lyKlF0PYqqdR/6DGXaLYo1RjiRgrivzepIizLzu7sm1XUzuVLIFEKObe+d1wpS183eYahtw4DY1UWMJr15ZpmxYAnDt7uF0BxOwXL5Evc0kbxlmyxxZLFyY2CaXSDZkqKZROYJ4tnO/Tt56HEgckyJaraGNxlsVt2u6teHeF40s20EDo9oyGy+CPIYF1xULBt4H6kOZeFiwBZnOFi+wH0B1hk)

</div>

## Method handlers {#method-handlers}

Tuy vậy, logic của nhiều event handler thường phức tạp hơn và khó viết gọn bằng inline handler. Vì thế `v-on` cũng có thể nhận tên hoặc đường dẫn tới method của component mà bạn muốn gọi.

Ví dụ:

<div class="composition-api">

```js
const name = ref('Vue.js')

function greet(event) {
  alert(`Xin chào ${name.value}!`)
  // `event` là sự kiện DOM gốc
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
    // `this` bên trong methods trỏ tới instance hiện tại
    alert(`Xin chào ${this.name}!`)
    // `event` là sự kiện DOM gốc
    if (event) {
      alert(event.target.tagName)
    }
  }
}
```

</div>

```vue-html
<!-- `greet` là tên của method đã được định nghĩa ở trên -->
<button @click="greet">Chào</button>
```

<div class="composition-api">

[Thử trên Playground](https://play.vuejs.org/#eNpVj0FLxDAQhf/KMwjtXtq7dBcFQS/qzVMOrWFao2kSkkkvpf/dJIuCEBgm771vZnbx4H23JRJ3YogqaM+IxMlfpNWrd4GxI9CMA3NwK5psbaSVVjkbGXZaCediaJv3RN1XbE5FnZNVrJ3FEoi4pY0sn7BLC0yGArfjMxnjcLsXQrdNJtFxM+Ys0PcYa2CEjuBPylNYb4THtxdUobj0jH/YX3D963gKC5WyvGZ+xR7S5jf01yPzeblhWr2ZmErHw0dizivfK6PV91mKursUl6dSh/4qZ+vQ/+XE8QODonDi)

</div>
<div class="options-api">

[Thử trên Playground](https://play.vuejs.org/#eNplUE1LxDAQ/StjEbYL0t5LXRQEvag3Tz00prNtNE1CMilC6X83SUkRhJDJfLz3Jm8tHo2pFo9FU7SOW2Ho0in8MdoSDHhlXhKsnQIYGLHyvL8BLJK3KmcAis3YwOnDY/XlTnt1i2G7i/eMNOnBNRkwWkQqcUFFByVAXUNPk3A9COXEgBkGRgtFDkgDTQjcWxuAwDiJBeMsMcUxszCJlsr+BaXUcLtGwiqut930579KST1IBd5Aqlgie3p/hdTIk+IK//bMGqleEbMjxjC+BZVDIv0+m9CpcNr6MDgkhLORjDBm1H56Iq3ggUvBv++7IhnUFZfnGNt6b4fRtj5wxfYL9p+Sjw==)

</div>

Method handler sẽ tự động nhận object DOM Event gốc đã kích hoạt nó. Trong ví dụ trên, chúng ta có thể truy cập phần tử kích hoạt sự kiện qua `event.target`.

<div class="composition-api">

Xem thêm: [Khai báo kiểu cho event handler](/guide/typescript/composition-api#typing-event-handlers) <sup class="vt-badge ts" />

</div>
<div class="options-api">

Xem thêm: [Khai báo kiểu cho event handler](/guide/typescript/options-api#typing-event-handlers) <sup class="vt-badge ts" />

</div>

### Phân biệt method và inline handler {#method-vs-inline-detection}

Trình biên dịch template nhận biết method handler bằng cách kiểm tra xem chuỗi giá trị của `v-on` có phải là một định danh JavaScript hợp lệ hay một đường dẫn truy cập property hay không. Ví dụ, `foo`, `foo.bar` và `foo['bar']` sẽ được xem là method handler, còn `foo()` và `count++` sẽ được xem là inline handler.

## Gọi method trong inline handler {#calling-methods-in-inline-handlers}

Thay vì bind trực tiếp tới tên method, ta cũng có thể gọi method ngay trong inline handler. Cách này cho phép ta truyền đối số tùy ý cho method thay vì chỉ nhận event gốc:

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
<button @click="say('xin chào')">Nói xin chào</button>
<button @click="say('tạm biệt')">Nói tạm biệt</button>
```

<div class="composition-api">

[Thử trên Playground](https://play.vuejs.org/#eNp9jTEOwjAMRa8SeSld6I5CBWdg9ZJGBiJSN2ocpKjq3UmpFDGx+Vn//b/ANYTjOxGcQEc7uyAqkqTQI98TW3ETq2jyYaQYzYNatSArZTzNUn/IK7Ludr2IBYTG4I3QRqKHJFJ6LtY7+zojbIXNk7yfmhahv5msvqS7PfnHGjJVp9w/hu7qKKwfEd1NSg==)

</div>
<div class="options-api">

[Thử trên Playground](https://play.vuejs.org/#eNptjUEKwjAQRa8yZFO7sfsSi57B7WzGdjTBtA3NVC2ldzehEFwIw8D7vM9f1cX742tmVSsd2sl6aXDgjx8ngY7vNDuBFQeAnsWMXagToQAEWg49h0APLncDAIUcT5LzlKJsqRBfPF3ljQjCvXcknEj0bRYZBzi3zrbPE6o0UBhblKiaKy1grK52J/oA//23IcmNBD8dXeVBtX0BF0pXsg==)

</div>

## Truy cập đối số event trong inline handler {#accessing-event-argument-in-inline-handlers}

Đôi khi ta cũng cần truy cập sự kiện DOM gốc trong inline handler. Bạn có thể truyền nó vào method bằng biến đặc biệt `$event`, hoặc dùng một hàm mũi tên inline:

```vue-html
<!-- dùng biến đặc biệt $event -->
<button @click="warn('Biểu mẫu vẫn chưa thể gửi.', $event)">
  Gửi
</button>

<!-- dùng hàm mũi tên inline -->
<button @click="(event) => warn('Biểu mẫu vẫn chưa thể gửi.', event)">
  Gửi
</button>
```

<div class="composition-api">

```js
function warn(message, event) {
  // giờ ta có thể truy cập event gốc
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
    // giờ ta có thể truy cập event gốc
    if (event) {
      event.preventDefault()
    }
    alert(message)
  }
}
```

</div>

## Modifier cho sự kiện {#event-modifiers}

Việc gọi `event.preventDefault()` hoặc `event.stopPropagation()` bên trong event handler là nhu cầu rất thường gặp. Dù ta có thể làm điều này trong method, sẽ tốt hơn nếu method chỉ tập trung vào logic dữ liệu thay vì phải lo chi tiết sự kiện DOM.

Để giải quyết chuyện đó, Vue cung cấp **event modifiers** cho `v-on`. Nhắc lại một chút, modifier là phần hậu tố của directive và được viết với dấu chấm.

- `.stop`
- `.prevent`
- `.self`
- `.capture`
- `.once`
- `.passive`

```vue-html
<!-- chặn nổi bọt của sự kiện click -->
<a @click.stop="doThis"></a>

<!-- sự kiện submit sẽ không làm tải lại trang nữa -->
<form @submit.prevent="onSubmit"></form>

<!-- modifier có thể nối chuỗi -->
<a @click.stop.prevent="doThat"></a>

<!-- chỉ dùng modifier -->
<form @submit.prevent></form>

<!-- chỉ kích hoạt handler nếu event.target chính là phần tử này -->
<!-- tức là không phải từ phần tử con -->
<div @click.self="doThat">...</div>
```

::: tip
Thứ tự có ý nghĩa khi dùng modifier vì đoạn mã tương ứng sẽ được tạo ra theo đúng thứ tự đó. Vì vậy, `@click.prevent.self` sẽ chặn **hành vi mặc định của click trên chính phần tử đó và cả phần tử con của nó**, trong khi `@click.self.prevent` chỉ chặn hành vi mặc định của click trên chính phần tử đó.
:::

Các modifier `.capture`, `.once` và `.passive` tương ứng với các [tùy chọn của phương thức `addEventListener` gốc](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/addEventListener#options):

```vue-html
<!-- dùng chế độ capture khi thêm event listener         -->
<!-- tức là sự kiện nhắm tới phần tử bên trong sẽ được xử lý -->
<!-- ở đây trước khi được phần tử đó xử lý               -->
<div @click.capture="doThis">...</div>

<!-- sự kiện click sẽ chỉ được kích hoạt nhiều nhất một lần -->
<a @click.once="doThis"></a>

<!-- hành vi mặc định của sự kiện scroll (cuộn) sẽ diễn ra ngay -->
<!-- thay vì phải chờ `onScroll` chạy xong                     -->
<!-- trong trường hợp nó có chứa `event.preventDefault()`      -->
<div @scroll.passive="onScroll">...</div>
```

Modifier `.passive` thường được dùng với touch event listener để [cải thiện hiệu năng trên thiết bị di động](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/addEventListener#improving_scroll_performance_using_passive_listeners).

::: tip
Không nên dùng `.passive` cùng với `.prevent`, vì `.passive` đã báo cho trình duyệt biết rằng bạn _không_ có ý định chặn hành vi mặc định của sự kiện. Nếu vẫn kết hợp như vậy, rất có thể bạn sẽ thấy cảnh báo từ trình duyệt.
:::

## Modifier cho phím {#key-modifiers}

Khi lắng nghe sự kiện bàn phím, chúng ta thường cần kiểm tra những phím cụ thể. Vue cho phép thêm key modifier cho `v-on` hoặc `@` khi lắng nghe key event:

```vue-html
<!-- chỉ gọi `submit` khi `key` là `Enter` -->
<input @keyup.enter="submit" />
```

Bạn có thể dùng trực tiếp bất kỳ tên phím hợp lệ nào được cung cấp qua [`KeyboardEvent.key`](https://developer.mozilla.org/en-US/docs/Web/API/UI_Events/Keyboard_event_key_values) làm modifier bằng cách chuyển nó sang kebab-case.

```vue-html
<input @keyup.page-down="onPageDown" />
```

Trong ví dụ trên, handler sẽ chỉ được gọi nếu `$event.key` bằng `'PageDown'`.

### Bí danh của phím {#key-aliases}

Vue cung cấp bí danh cho những phím được dùng phổ biến nhất:

- `.enter`
- `.tab`
- `.delete` (bắt cả phím "Delete" lẫn "Backspace")
- `.esc`
- `.space`
- `.up`
- `.down`
- `.left`
- `.right`

### Phím modifier của hệ thống {#system-modifier-keys}

Bạn có thể dùng các modifier sau để chỉ kích hoạt listener chuột hoặc bàn phím khi phím modifier tương ứng đang được nhấn:

- `.ctrl`
- `.alt`
- `.shift`
- `.meta`

::: tip Lưu ý
Trên bàn phím Macintosh, `meta` là phím command (⌘). Trên bàn phím Windows, `meta` là phím Windows (⊞). Trên bàn phím của Sun Microsystems, `meta` được đánh dấu bằng hình thoi đặc (◆). Trên một số bàn phím khác, đặc biệt là bàn phím MIT, Lisp machine và các biến thể kế thừa như Knight keyboard hay space-cadet keyboard, `meta` được ghi là “META”. Trên bàn phím Symbolics, `meta` được ghi là “META” hoặc “Meta”.
:::

Ví dụ:

```vue-html
<!-- Alt + Enter -->
<input @keyup.alt.enter="clear" />

<!-- Ctrl + Click -->
<div @click.ctrl="doSomething">Làm gì đó</div>
```

::: tip
Lưu ý rằng phím modifier khác với phím thông thường, và khi dùng với sự kiện `keyup`, chúng phải đang được nhấn vào đúng lúc sự kiện được phát ra. Nói cách khác, `keyup.ctrl` chỉ kích hoạt khi bạn thả một phím nào đó trong lúc vẫn đang giữ `ctrl`. Nó sẽ không kích hoạt nếu bạn chỉ thả riêng phím `ctrl`.
:::

### Modifier `.exact` {#exact-modifier}

Modifier `.exact` cho phép kiểm soát chính xác tổ hợp phím modifier của hệ thống cần có để kích hoạt sự kiện.

```vue-html
<!-- vẫn kích hoạt ngay cả khi Alt hoặc Shift cũng đang được nhấn -->
<button @click.ctrl="onClick">A</button>

<!-- chỉ kích hoạt khi Ctrl được nhấn và không có phím nào khác -->
<button @click.ctrl.exact="onCtrlClick">A</button>

<!-- chỉ kích hoạt khi không có phím modifier hệ thống nào được nhấn -->
<button @click.exact="onClick">A</button>
```

## Modifier cho nút chuột {#mouse-button-modifiers}

- `.left`
- `.right`
- `.middle`

Các modifier này giới hạn handler chỉ chạy với những sự kiện được kích hoạt bởi một nút chuột cụ thể.

Tuy vậy, cần lưu ý rằng tên modifier `.left`, `.right` và `.middle` dựa trên bố cục chuột tay phải thông dụng, nhưng trên thực tế chúng lần lượt đại diện cho cơ chế kích hoạt "chính", "phụ" và "bổ trợ" của thiết bị trỏ, chứ không nhất thiết là nút vật lý thật sự. Vì thế, với chuột cho người thuận tay trái, nút "chính" có thể nằm bên phải về mặt vật lý nhưng vẫn kích hoạt handler của `.left`. Hoặc trên trackpad, thao tác chạm một ngón có thể kích hoạt `.left`, chạm hai ngón kích hoạt `.right`, và chạm ba ngón kích hoạt `.middle`. Tương tự, các thiết bị hay nguồn sự kiện khác tạo ra "mouse" event cũng có thể có cơ chế kích hoạt hoàn toàn không liên quan đến "trái" hay "phải".
