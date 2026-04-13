---
outline: deep
---

<script setup>
import { ref } from 'vue'
const message = ref('')
const multilineText = ref('')
const checked = ref(false)
const checkedNames = ref([])
const picked = ref('')
const selected = ref('')
const multiSelected = ref([])
const dynamicSelected = ref('A')
const options = ref([
  { text: 'One', value: 'A' },
  { text: 'Two', value: 'B' },
  { text: 'Three', value: 'C' }
])
</script>

# Binding input form {#form-input-bindings}

<div class="options-api">
  <VueSchoolLink href="https://vueschool.io/lessons/user-inputs-vue-devtools-in-vue-3" title="Bài học miễn phí về User Inputs với Vue.js"/>
</div>

<div class="composition-api">
  <VueSchoolLink href="https://vueschool.io/lessons/vue-fundamentals-capi-user-inputs-in-vue" title="Bài học miễn phí về User Inputs với Vue.js"/>
</div>

Khi làm việc với form ở frontend, chúng ta thường cần đồng bộ trạng thái của các phần tử input với trạng thái tương ứng trong JavaScript. Việc tự gắn value binding và lắng nghe event thay đổi có thể khá rườm rà:

```vue-html
<input
  :value="text"
  @input="event => text = event.target.value">
```

Directive `v-model` giúp chúng ta đơn giản hóa đoạn trên thành:

```vue-html
<input v-model="text">
```

Ngoài ra, `v-model` có thể dùng trên các loại input khác nhau, `<textarea>`, và `<select>`. Nó tự động mở rộng thành các cặp property và event DOM khác nhau tùy theo phần tử:

* `<input>` dạng text và `<textarea>` dùng property `value` và event `input`;
* `<input type="checkbox">` và `<input type="radio">` dùng property `checked` và event `change`;
* `<select>` dùng `value` làm prop và `change` làm event.

::: tip Lưu ý
`v-model` sẽ bỏ qua các attribute `value`, `checked` hoặc `selected` ban đầu có trên phần tử form. Nó luôn xem trạng thái JavaScript hiện tại được bind là nguồn dữ liệu chính. Bạn nên khai báo giá trị ban đầu ở phía JavaScript, dùng <span class="options-api">option [`data`]</span><span class="composition-api">reactivity APIs</span>.
:::

## Cách dùng cơ bản {#basic-usage}

### Text {#text}

```vue-html
<p>Message là: {{ message }}</p>
<input v-model="message" placeholder="edit me" />
```

<div class="demo">
  <p>Message là: {{ message }}</p>
  <input v-model="message" placeholder="edit me" />
</div>

<div class="composition-api">

[Thử trong Playground](https://play.vuejs.org/#eNo9jUEOgyAQRa8yYUO7aNkbNOkBegM2RseWRGACoxvC3TumxuX/+f+9ql5Ez31D1SlbpuyJoSBvNLjoA6XMUCHjAg2WnAJomWoXXZxSLAwBSxk/CP2xuWl9d9GaP0YAEhgDrSOjJABLw/s8+NJBrde/NWsOpWPrI20M+yOkGdfeqXPiFAhowm9aZ8zS4+wPv/RGjtZcJtV+YpNK1g==)

</div>
<div class="options-api">

[Thử trong Playground](https://play.vuejs.org/#eNo9jdEKwjAMRX8l9EV90L2POvAD/IO+lDVqoetCmw6h9N/NmBuEJPeSc1PVg+i2FFS90nlMnngwEb80JwaHL1sCQzURwFm258u2AyTkkuKuACbM2b6xh9Nps9o6pEnp7ggWwThRsIyiADQNz40En3uodQ+C1nRHK8HaRyoMy3WaHYa7Uf8To0CCRvzMwWESH51n4cXvBNTd8Um1H0FuTq0=)

</div>

<span id="vmodel-ime-tip"></span>
::: tip Lưu ý
Với các ngôn ngữ cần IME (Trung, Nhật, Hàn,...), bạn sẽ thấy `v-model` không cập nhật trong quá trình nhập IME. Nếu muốn xử lý cả giai đoạn này, hãy dùng `input` event listener và `value` binding thay vì `v-model`.
:::

### Text nhiều dòng {#multiline-text}

```vue-html
<span>Message nhiều dòng là:</span>
<p style="white-space: pre-line;">{{ message }}</p>
<textarea v-model="message" placeholder="add multiple lines"></textarea>
```

<div class="demo">
  <span>Message nhiều dòng là:</span>
  <p style="white-space: pre-line;">{{ multilineText }}</p>
  <textarea v-model="multilineText" placeholder="add multiple lines"></textarea>
</div>

<div class="composition-api">

[Thử trong Playground](https://play.vuejs.org/#eNo9jktuwzAMRK9CaON24XrvKgZ6gN5AG8FmGgH6ECKdJjB891D5LYec9zCb+SH6Oq9oRmN5roEEGGWlyeWQqFSBDSoeYYdjLQk6rXYuuzyXzAIJmf0fwqF1Prru02U7PDQq0CCYKHrBlsQy+Tz9rlFCDBnfdOBRqfa7twhYrhEPzvyfgmCvnxlHoIp9w76dmbbtDe+7HdpaBQUv4it6OPepLBjV8Gw5AzpjxlOJC1a9+2WB1IZQRGhWVqsdXgb1tfDcbvYbJDRqLQ==)

</div>
<div class="options-api">

[Thử trong Playground](https://play.vuejs.org/#eNo9jk2OwyAMha9isenMIpN9hok0B+gN2FjBbZEIscDpj6LcvaZpKiHg2X6f32L+mX+uM5nO2DLkwNK7RHeesoCnE85RYHEJwKPg1/f2B8gkc067AhipFDxTB4fDVlrro5ce237AKoRGjihUldjCmPqjLgkxJNoxEEqnrtp7TTEUeUT6c+Z2CUKNdgbdxZmaavt1pl+Wj3ldbcubUegumAnh2oyTp6iE95QzoDEGukzRU9Y6eg9jDcKRoFKLUm27E5RXxTu7WZ89/G4E)

</div>

Lưu ý rằng interpolation bên trong `<textarea>` sẽ không hoạt động. Hãy dùng `v-model`.

```vue-html
<!-- sai -->
<textarea>{{ text }}</textarea>

<!-- đúng -->
<textarea v-model="text"></textarea>
```

### Checkbox {#checkbox}

Checkbox đơn, giá trị boolean:

```vue-html
<input type="checkbox" id="checkbox" v-model="checked" />
<label for="checkbox">{{ checked }}</label>
```

<div class="demo">
  <input type="checkbox" id="checkbox-demo" v-model="checked" />
  <label for="checkbox-demo">{{ checked }}</label>
</div>

<div class="composition-api">

[Thử trong Playground](https://play.vuejs.org/#eNpVjssKgzAURH/lko3tonVfotD/yEaTKw3Ni3gjLSH/3qhUcDnDnMNk9gzhviRkD8ZnGXUgmJFS6IXTNvhIkCHiBAWm6C00ddoIJ5z0biaQL5RvVNCtmwvFhFfheLuLqqIGQhvMQLgm4tqFREDfgJ1gGz36j2Cg1TkvN+sVmn+JqnbtrjDDiAYmH09En/PxphTebqsK8PY4wMoPslBUxQ==)

</div>
<div class="options-api">

[Thử trong Playground](https://play.vuejs.org/#eNpVjtEKgzAMRX8l9Gl72Po+OmH/0ZdqI5PVNnSpOEr/fVVREEKSc0kuN4sX0X1KKB5Cfbs4EDfa40whMljsTXIMWXsAa9hcrtsOEJFT9DsBdG/sPmgfwDHhJpZl1FZLycO6AuNIzjAuxGrwlBj4R/jUYrVpw6wFDPbM020MFt0uoq2a3CycadFBH+Lpo8l5jwWlKLle1QcljwCi/AH7gFic)

</div>

Chúng ta cũng có thể bind nhiều checkbox vào cùng một array hoặc Set:

<div class="composition-api">

```js
const checkedNames = ref([])
```

</div>
<div class="options-api">

```js
export default {
  data() {
    return {
      checkedNames: []
    }
  }
}
```

</div>

```vue-html
<div>Checked names: {{ checkedNames }}</div>

<input type="checkbox" id="jack" value="Jack" v-model="checkedNames" />
<label for="jack">Jack</label>

<input type="checkbox" id="john" value="John" v-model="checkedNames" />
<label for="john">John</label>

<input type="checkbox" id="mike" value="Mike" v-model="checkedNames" />
<label for="mike">Mike</label>
```

<div class="demo">
  <div>Checked names: {{ checkedNames }}</div>

  <input type="checkbox" id="demo-jack" value="Jack" v-model="checkedNames" />
  <label for="demo-jack">Jack</label>

  <input type="checkbox" id="demo-john" value="John" v-model="checkedNames" />
  <label for="demo-john">John</label>

  <input type="checkbox" id="demo-mike" value="Mike" v-model="checkedNames" />
  <label for="demo-mike">Mike</label>
</div>

Trong trường hợp này, array `checkedNames` sẽ luôn chứa giá trị của các checkbox đang được chọn.

### Radio {#radio}

```vue-html
<div>Picked: {{ picked }}</div>

<input type="radio" id="one" value="One" v-model="picked" />
<label for="one">One</label>

<input type="radio" id="two" value="Two" v-model="picked" />
<label for="two">Two</label>
```

<div class="demo">
  <div>Picked: {{ picked }}</div>

  <input type="radio" id="one" value="One" v-model="picked" />
  <label for="one">One</label>

  <input type="radio" id="two" value="Two" v-model="picked" />
  <label for="two">Two</label>
</div>

### Select {#select}

Select đơn:

```vue-html
<div>Selected: {{ selected }}</div>

<select v-model="selected">
  <option disabled value="">Please select one</option>
  <option>A</option>
  <option>B</option>
  <option>C</option>
</select>
```

<div class="demo">
  <div>Selected: {{ selected }}</div>
  <select v-model="selected">
    <option disabled value="">Please select one</option>
    <option>A</option>
    <option>B</option>
    <option>C</option>
  </select>
</div>

:::tip Lưu ý
Nếu giá trị ban đầu của `v-model` không khớp với option nào, `<select>` sẽ ở trạng thái chưa chọn. Trên iOS, người dùng sẽ không thể chọn item đầu tiên vì iOS không phát ra event change trong trường hợp này. Vì vậy nên thêm option disabled với giá trị rỗng như ví dụ trên.
:::

Select nhiều:

```vue-html
<div>Selected: {{ selected }}</div>

<select v-model="selected" multiple>
  <option>A</option>
  <option>B</option>
  <option>C</option>
</select>
```

<div class="demo">
  <div>Selected: {{ multiSelected }}</div>

  <select v-model="multiSelected" multiple>
    <option>A</option>
    <option>B</option>
    <option>C</option>
  </select>
</div>

Select có thể render động với `v-for`:

```vue-html
<div>Selected: {{ selected }}</div>

<select v-model="selected">
  <option v-for="option in options" :value="option.value">
    {{ option.text }}
  </option>
</select>
```

<div class="demo">
  <div>Selected: {{ dynamicSelected }}</div>

  <select v-model="dynamicSelected">
    <option v-for="option in options" :value="option.value">
      {{ option.text }}
    </option>
  </select>
</div>

## Binding giá trị {#value-bindings}

Với radio, checkbox và select, giá trị của `v-model` thường là string tĩnh (hoặc boolean với checkbox):

```vue-html
<input type="radio" v-model="picked" value="a" />

<input type="checkbox" v-model="toggle" />

<select v-model="selected">
  <option value="abc">ABC</option>
</select>
```

Nhưng đôi khi chúng ta muốn bind giá trị động. Có thể dùng `v-bind`. Điều này cũng cho phép dùng giá trị không phải string.

### Checkbox {#checkbox-1}

```vue-html
<input
  type="checkbox"
  v-model="toggle"
  true-value="yes"
  false-value="no" />
```

`true-value` và `false-value` là attribute riêng của Vue, chỉ hoạt động với `v-model`. Khi checked, `toggle` = `'yes'`, khi bỏ check, `toggle` = `'no'`.

Có thể bind động:

```vue-html
<input
  type="checkbox"
  v-model="toggle"
  :true-value="dynamicTrueValue"
  :false-value="dynamicFalseValue" />
```

:::tip Lưu ý
`true-value` và `false-value` không ảnh hưởng đến thuộc tính `value` của input, vì trình duyệt không đưa các checkbox không được chọn vào form submission. Để đảm bảo một trong hai giá trị được submit (ví dụ: 'yes' hoặc 'no'), hãy dùng radio input.
:::

### Radio {#radio-1}

```vue-html
<input type="radio" v-model="pick" :value="first" />
<input type="radio" v-model="pick" :value="second" />
```

`pick` sẽ được set thành giá trị của `first` khi radio input đầu tiên được chọn, và thành giá trị của `second` khi cái thứ hai được chọn.

### Select Options {#select-options}

```vue-html
<select v-model="selected">
  <option :value="{ number: 123 }">123</option>
</select>
```

`v-model` hỗ trợ binding giá trị không phải string! Trong ví dụ trên, khi option được chọn, `selected` sẽ được set thành giá trị object `{ number: 123 }`.

## Modifiers {#modifiers}

### `.lazy` {#lazy}

Mặc định `v-model` sync sau mỗi event `input`. Có thể dùng `.lazy` để sync sau `change`:

```vue-html
<input v-model.lazy="msg" />
```

### `.number` {#number}

Tự động chuyển input thành number:

```vue-html
<input v-model.number="age" />
```

Nếu giá trị không thể parse bằng `parseFloat()`, giá trị gốc (string) sẽ được dùng thay thế. Đặc biệt, nếu input rỗng, một string rỗng được trả về. Hành vi này khác với [DOM property `valueAsNumber`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLInputElement#valueasnumber).

Modifier `number` tự động được áp dụng nếu input có `type="number"`.

### `.trim` {#trim}

Tự động trim khoảng trắng:

```vue-html
<input v-model.trim="msg" />
```

## `v-model` với Component {#v-model-with-components}

> Nếu bạn chưa quen với component, có thể bỏ qua phần này.

Input HTML không phải lúc nào cũng đủ. Vue cho phép tạo input tùy chỉnh có thể tái sử dụng, và vẫn dùng được với `v-model`.

Để biết thêm, xem phần [Usage with `v-model`](/guide/components/v-model).
