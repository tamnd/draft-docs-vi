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
  { text: 'Một', value: 'A' },
  { text: 'Hai', value: 'B' },
  { text: 'Ba', value: 'C' }
])
</script>

# Ràng buộc input của form {#form-input-bindings}

<div class="options-api">
  <VueSchoolLink href="https://vueschool.io/lessons/user-inputs-vue-devtools-in-vue-3" title="Bài học miễn phí về input người dùng trong Vue.js"/>
</div>

<div class="composition-api">
  <VueSchoolLink href="https://vueschool.io/lessons/vue-fundamentals-capi-user-inputs-in-vue" title="Bài học miễn phí về input người dùng trong Vue.js"/>
</div>

Khi làm việc với form ở phía frontend, chúng ta thường cần đồng bộ state của các phần tử input trong form với state tương ứng ở phía JavaScript. Nếu tự nối `value` binding và `change` event listener bằng tay thì khá cồng kềnh:

```vue-html
<input
  :value="text"
  @input="event => text = event.target.value">
```

Directive `v-model` giúp ta rút gọn đoạn trên thành:

```vue-html
<input v-model="text">
```

Ngoài ra, `v-model` có thể dùng trên nhiều loại input khác nhau, trên phần tử `<textarea>` và `<select>`. Nó sẽ tự mở rộng thành những cặp DOM property và event khác nhau tùy theo phần tử mà nó được dùng trên đó:

- `<input>` kiểu văn bản và phần tử `<textarea>` dùng property `value` và event `input`;
- `<input type="checkbox">` và `<input type="radio">` dùng property `checked` và event `change`;
- `<select>` dùng `value` làm property và `change` làm event.

::: tip Lưu ý
`v-model` sẽ bỏ qua các thuộc tính `value`, `checked` hoặc `selected` ban đầu có trên phần tử form. Nó luôn xem state JavaScript đang được bind hiện tại là nguồn dữ liệu chuẩn. Bạn nên khai báo giá trị ban đầu ở phía JavaScript, bằng <span class="options-api">option [`data`](/api/options-state.html#data)</span><span class="composition-api">[reactivity API](/api/reactivity-core.html#reactivity-api-core)</span>.
:::

## Cách dùng cơ bản {#basic-usage}

### Text {#text}

```vue-html
<p>Nội dung là: {{ message }}</p>
<input v-model="message" placeholder="hãy sửa tôi" />
```

<div class="demo">
  <p>Nội dung là: {{ message }}</p>
  <input v-model="message" placeholder="hãy sửa tôi" />
</div>

<div class="composition-api">

[Thử trên Playground](https://play.vuejs.org/#eNo9jUEOgyAQRa8yYUO7aNkbNOkBegM2RseWRGACoxvC3TumxuX/+f+9ql5Ez31D1SlbpuyJoSBvNLjoA6XMUCHjAg2WnAJomWoXXZxSLAwBSxk/CP2xuWl9d9GaP0YAEhgDrSOjJABLw/s8+NJBrde/NWsOpWPrI20M+yOkGdfeqXPiFAhowm9aZ8zS4+wPv/RGjtZcJtV+YpNK1g==)

</div>
<div class="options-api">

[Thử trên Playground](https://play.vuejs.org/#eNo9jdEKwjAMRX8l9EV90L2POvAD/IO+lDVqoetCmw6h9N/NmBuEJPeSc1PVg+i2FFS90nlMnngwEb80JwaHL1sCQzURwFm258u2AyTkkuKuACbM2b6xh9Nps9o6pEnp7ggWwThRsIyiADQNz40En3uodQ+C1nRHK8HaRyoMy3WaHYa7Uf8To0CCRvzMwWESH51n4cXvBNTd8Um1H0FuTq0=)

</div>

<span id="vmodel-ime-tip"></span>
::: tip Lưu ý
Với những ngôn ngữ cần dùng [IME](https://en.wikipedia.org/wiki/Input_method) (tiếng Trung, tiếng Nhật, tiếng Hàn...), bạn sẽ thấy `v-model` không cập nhật trong quá trình nhập ký tự bằng IME. Nếu bạn cũng muốn phản ứng với những cập nhật đó, hãy tự dùng `input` event listener và `value` binding thay vì `v-model`.
:::

### Multiline Text {#multiline-text}

```vue-html
<span>Nội dung nhiều dòng là:</span>
<p style="white-space: pre-line;">{{ message }}</p>
<textarea v-model="message" placeholder="nhập nhiều dòng"></textarea>
```

<div class="demo">
  <span>Nội dung nhiều dòng là:</span>
  <p style="white-space: pre-line;">{{ multilineText }}</p>
  <textarea v-model="multilineText" placeholder="nhập nhiều dòng"></textarea>
</div>

<div class="composition-api">

[Thử trên Playground](https://play.vuejs.org/#eNo9jktuwzAMRK9CaON24XrvKgZ6gN5AG8FmGgH6ECKdJjB891D5LYec9zCb+SH6Oq9oRmN5roEEGGWlyeWQqFSBDSoeYYdjLQk6rXYuuzyXzAIJmf0fwqF1Prru02U7PDQq0CCYKHrBlsQy+Tz9rlFCDBnfdOBRqfa7twhYrhEPzvyfgmCvnxlHoIp9w76dmbbtDe+7HdpaBQUv4it6OPepLBjV8Gw5AzpjxlOJC1a9+2WB1IZQRGhWVqsdXgb1tfDcbvYbJDRqLQ==)

</div>
<div class="options-api">

[Thử trên Playground](https://play.vuejs.org/#eNo9jk2OwyAMha9isenMIpN9hok0B+gN2FjBbZEIscDpj6LcvaZpKiHg2X6f32L+mX+uM5nO2DLkwNK7RHeesoCnE85RYHEJwKPg1/f2B8gkc067AhipFDxTB4fDVlrro5ce237AKoRGjihUldjCmPqjLgkxJNoxEEqnrtp7TTEUeUT6c+Z2CUKNdgbdxZmaavt1pl+Wj3ldbcubUegumAnh2oyTp6iE95QzoDEGukzRU9Y6eg9jDcKRoFKLUm27E5RXxTu7WZ89/G4E)

</div>

Lưu ý rằng nội suy bên trong `<textarea>` sẽ không hoạt động. Hãy dùng `v-model` thay thế.

```vue-html
<!-- không nên -->
<textarea>{{ text }}</textarea>

<!-- nên dùng -->
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

[Thử trên Playground](https://play.vuejs.org/#eNpVjssKgzAURH/lko3tonVfotD/yEaTKw3Ni3gjLSH/3qhUcDnDnMNk9gzhviRkD8ZnGXUgmJFS6IXTNvhIkCHiBAWm6C00ddoIJ5z0biaQL5RvVNCtmwvFhFfheLuLqqIGQhvMQLgm4tqFREDfgJ1gGz36j2Cg1TkvN+sVmn+JqnbtrjDDiAYmH09En/PxphTebqsK8PY4wMoPslBUxQ==)

</div>
<div class="options-api">

[Thử trên Playground](https://play.vuejs.org/#eNpVjtEKgzAMRX8l9Gl72Po+OmH/0ZdqI5PVNnSpOEr/fVVREEKSc0kuN4sX0X1KKB5Cfbs4EDfa40whMljsTXIMWXsAa9hcrtsOEJFT9DsBdG/sPmgfwDHhJpZl1FZLycO6AuNIzjAuxGrwlBj4R/jUYrVpw6wFDPbM020MFt0uoq2a3CycadFBH+Lpo8l5jwWlKLle1QcljwCi/AH7gFic)

</div>

Chúng ta cũng có thể bind nhiều checkbox vào cùng một mảng hoặc giá trị [Set](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Set):

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
<div>Tên đã chọn: {{ checkedNames }}</div>

<input type="checkbox" id="jack" value="Jack" v-model="checkedNames" />
<label for="jack">Jack</label>

<input type="checkbox" id="john" value="John" v-model="checkedNames" />
<label for="john">John</label>

<input type="checkbox" id="mike" value="Mike" v-model="checkedNames" />
<label for="mike">Mike</label>
```

<div class="demo">
  <div>Tên đã chọn: {{ checkedNames }}</div>

  <input type="checkbox" id="demo-jack" value="Jack" v-model="checkedNames" />
  <label for="demo-jack">Jack</label>

  <input type="checkbox" id="demo-john" value="John" v-model="checkedNames" />
  <label for="demo-john">John</label>

  <input type="checkbox" id="demo-mike" value="Mike" v-model="checkedNames" />
  <label for="demo-mike">Mike</label>
</div>

Trong trường hợp này, mảng `checkedNames` sẽ luôn chứa giá trị của những checkbox đang được chọn.

<div class="composition-api">

[Thử trên Playground](https://play.vuejs.org/#eNqVkUtqwzAURbfy0CTtoNU8KILSWaHdQNWBIj8T1fohyybBeO+RbOc3i2e+vHvuMWggHyG89x2SLWGtijokaDF1gQunbfAxwQARaxihjt7CJlc3wgmnvGsTqAOqBqsfabGFXSm+/P69CsfovJVXckhog5EJcwJgle7558yBK+AWhuFxaRwZLbVCZ0K70CVIp4A7Qabi3h8FAV3l/C9Vk797abpy/lrim/UVmkt/Gc4HOv+EkXs0UPt4XeCFZHQ6lM4TZn9w9+YlrjFPCC/kKrPVDd6Zv5e4wjwv8ELezIxeX4qMZwHduAs=)

</div>
<div class="options-api">

[Thử trên Playground](https://play.vuejs.org/#eNqVUc1qxCAQfpXBU3tovS9WKL0V2hdoenDjLGtjVNwxbAl592rMpru3DYjO5/cnOLLXEJ6HhGzHxKmNJpBsHJ6DjwQaDypZgrFxAFqRenisM0BEStFdEEB7xLZD/al6PO3g67veT+XIW16Cr+kZEPbBKsKMAIQ2g3yrAeBqwjjeRMI0CV5kxZ0dxoVEQL8BXxo2C/f+3DAwOuMf1XZ5HpRNhX5f4FPvNdqLfgnOBK+PsGqPFg4+rgmyOAWfiaK5o9kf3XXzArc0zxZZnJuae9PhVfPHAjc01wRZnP/Ngq8/xaY/yMW74g==)

</div>

### Radio {#radio}

```vue-html
<div>Đã chọn: {{ picked }}</div>

<input type="radio" id="one" value="Một" v-model="picked" />
<label for="one">Một</label>

<input type="radio" id="two" value="Hai" v-model="picked" />
<label for="two">Hai</label>
```

<div class="demo">
  <div>Đã chọn: {{ picked }}</div>

  <input type="radio" id="one" value="Một" v-model="picked" />
  <label for="one">Một</label>

  <input type="radio" id="two" value="Hai" v-model="picked" />
  <label for="two">Hai</label>
</div>

<div class="composition-api">

[Thử trên Playground](https://play.vuejs.org/#eNqFkDFuwzAMRa9CaHE7tNoDxUBP0A4dtTgWDQiRJUKmHQSG7x7KhpMMAbLxk3z/g5zVD9H3NKI6KDO02RPDgDxSbaPvKWWGGTJ2sECXUw+VrFY22timODCQb8/o4FhWPqrfiNWnjUZvRmIhgrGn0DCKAjDOT/XfCh1gnnd+WYwukwJYNj7SyMBXwqNVuXE+WQXeiUgRpZyaMJaR5BX11SeHQfTmJi1dnNiE5oQBupR3shbC6LX9Posvpdyz/jf1OksOe85ayVqIR5bR9z+o5Qbc6oCk)

</div>
<div class="options-api">

[Thử trên Playground](https://play.vuejs.org/#eNqNkEEOAiEMRa/SsFEXyt7gJJ5AFy5ng1ITIgLBMmomc3eLOONSEwJ9Lf//pL3YxrjqMoq1ULdTspGa1uMjhkRg8KyzI+hbD2A06fmi1gAJKSc/EkC0pwuaNcx2Hme1OZSHLz5KTtYMhNfoNGEhUsZ2zf6j7vuPEQyDkmVSBPzJ+pgJ6Blx04qkjQ2tAGsYgkcuO+1yGXF6oeU1GHTM1Y1bsoY5fUQH55BGZcMKJd/t31l0L+WYdaj0V9Zb2bDim6XktAcxvADR+YWb)

</div>

### Select {#select}

Chọn một giá trị:

```vue-html
<div>Đã chọn: {{ selected }}</div>

<select v-model="selected">
  <option disabled value="">Hãy chọn một mục</option>
  <option>A</option>
  <option>B</option>
  <option>C</option>
</select>
```

<div class="demo">
  <div>Đã chọn: {{ selected }}</div>
  <select v-model="selected">
    <option disabled value="">Hãy chọn một mục</option>
    <option>A</option>
    <option>B</option>
    <option>C</option>
  </select>
</div>

<div class="composition-api">

[Thử trên Playground](https://play.vuejs.org/#eNp1j7EOgyAQhl/lwmI7tO4Nmti+QJOuLFTPxASBALoQ3r2H2jYOjvff939wkTXWXucJ2Y1x37rBBvAYJlsLPYzWuAARHPaQoHdmhILQQmihW6N9RhW2ATuoMnQqirPQvFw9ZKAh4GiVDEgTAPdW6hpeW+sGMf4VKVEz73Mvs8sC5stoOlSVYF9SsEVGiLFhMBq6wcu3IsUs1YREEvFUKD1udjAaebnS+27dHOT3g/yxy+nHywM08PJ3KksfXwJ2dA==)

</div>
<div class="options-api">

[Thử trên Playground](https://play.vuejs.org/#eNp1j1ELgyAUhf/KxZe2h633cEHbHxjstReXdxCYSt5iEP333XIJPQSinuN3jjqJyvvrOKAohAxN33oqa4tf73oCjR81GIKptgBakTqd4x6gRxp6uymAgAYbQl1AlkVvXhaeeMg8NbMg7LxRhKwAZPDKlvBK8WlKXTDPnFzOI7naMF46p9HcarFxtVgBRpyn1lnQbVBvwwWjMgMyycTToAr47wZnUeaR3mfL6sC/H/iPnc/vXS9gIfP0UTH/ACgWeYE=)

</div>

:::tip Lưu ý
Nếu giá trị ban đầu của biểu thức `v-model` không khớp với bất kỳ option nào, phần tử `<select>` sẽ được render ở trạng thái "chưa chọn". Trên iOS, điều này sẽ khiến người dùng không thể chọn mục đầu tiên vì iOS không phát ra `change` event trong trường hợp đó. Vì vậy, nên cung cấp một option bị vô hiệu hóa với giá trị rỗng, như ví dụ ở trên.
:::

Chọn nhiều giá trị cùng lúc (bind với mảng):

```vue-html
<div>Đã chọn: {{ selected }}</div>

<select v-model="selected" multiple>
  <option>A</option>
  <option>B</option>
  <option>C</option>
</select>
```

<div class="demo">
  <div>Đã chọn: {{ multiSelected }}</div>

  <select v-model="multiSelected" multiple>
    <option>A</option>
    <option>B</option>
    <option>C</option>
  </select>
</div>

<div class="composition-api">

[Thử trên Playground](https://play.vuejs.org/#eNp1kL2OwjAQhF9l5Ya74i7QBhMJeARKTIESIyz5Z5VsAsjyu7NOQEBB5xl/M7vaKNaI/0OvRSlkV7cGCTpNPVbKG4ehJYjQ6hMkOLXBwYzRmfLK18F3GbW6Jt3AKkM/+8Ov8rKYeriBBWmH9kiaFYBszFDtHpkSYnwVpCSL/JtDDE4+DH8uNNqulHiCSoDrLRm0UyWzAckEX61l8Xh9+psv/vbD563HCSxk8bY0y45u47AJ2D/HHyDm4MU0dC5hMZ/jdal8Gg8wJkS6A3nRew4=)

</div>
<div class="options-api">

[Thử trên Playground](https://play.vuejs.org/#eNp1UEEOgjAQ/MqmJz0oeMVKgj7BI3AgdI1NCjSwIIbwdxcqRA4mTbsznd2Z7CAia49diyIQsslrbSlMSuxtVRMofGStIRiSEkBllO32rgaokdq6XBBAgwZzQhVAnDpunB6++EhvncyAsLAmI2QEIJXuwvvaPAzrJBhH6U2/UxMLHQ/doagUmksiFmEioOCU2ho3krWVJV2VYSS9b7Xlr3/424bn1LMDA+n9hGbY0Hs2c4J4sU/dPl5a0TOAk+/b/rwsYO4Q4wdtRX7l)

</div>

Các option của `select` có thể được render động bằng `v-for`:

<div class="composition-api">

```js
const selected = ref('A')

const options = ref([
  { text: 'Một', value: 'A' },
  { text: 'Hai', value: 'B' },
  { text: 'Ba', value: 'C' }
])
```

</div>
<div class="options-api">

```js
export default {
  data() {
    return {
      selected: 'A',
      options: [
        { text: 'Một', value: 'A' },
        { text: 'Hai', value: 'B' },
        { text: 'Ba', value: 'C' }
      ]
    }
  }
}
```

</div>

```vue-html
<div>Đã chọn: {{ selected }}</div>

<select v-model="selected">
  <option v-for="option in options" :value="option.value">
    {{ option.text }}
  </option>
</select>
```
<div class="demo">
  <div>Đã chọn: {{ dynamicSelected }}</div>
  
  <select v-model="dynamicSelected">
    <option v-for="option in options" :value="option.value">
      {{ option.text }}
    </option>
  </select>
</div>

<div class="composition-api">

[Thử trên Playground](https://play.vuejs.org/#eNp9kj9vwjAQxb/KyUtaiYahGwpIgBjaoVSFre6AkguEOnZkOwEpynfv2flDqlZkyt37+fye7ZotiyKsSmQzFplYZ4UFg7YsFlxmeaG0hRo0ptBAqlUOAaEBl1zGShqHCowtJjB30EOwDB5voipsRj+d9skl0CyLVzuDYCsxmEB1ECVStQygmfzS9xc10ld/9ZPG8YQ1EVx+0e7RtI1BAaiwmBfiYNFVNkqyarHrLM+grm/+myaaOtUtAojaPlRPuUpQzDnrQc4IAfqiNh0hqdIEdGUm+9icwcy7G8TQl8MESlN3cOhSkYdu9LTteo7i+K2piKZDGjZh1tApp9kxPBsl6fZqR3MWq7zIBOpt74JytmM5OwihLq++Z3WJ/kT9mhPG3//0z+bqepy9azSoK/I+aPagj2hbebN7I/8jkU6tFETfET/QKFE6jy22KmVCtkecd/vi32Amj3uzuVqUpg/ljDqyfRec0btc34l+s/scPvt1XDas+QENov3B)

</div>
<div class="options-api">

[Thử trên Playground](https://play.vuejs.org/#eNp9ksFuwjAMhl/FyoVNYuWwG+omAeKwHcY0uC07VK2BspBUiVuQKt59Tkq6Hjakqortz87/J2nFrKqSpkYxFanLbVnRs9R4rowlKHCb1YqglRqgyCi7u+/WABaptjpGAA4V5oTFFEaz0ThmTUWl0W4KnzED0ALhmZhbaRyNoclUjaELLn3fgNqczICa/0ftLQ6nLZiL2Fe3CDH/+EsnvVMOCI+Vygh9RGlRNs/r3kzb9s7gckknvuqbANIuD83D0RSonqSIoBSM+B3Tzj4jW2MZuIaljuciBUyD4r6YhLCfwA7bK5x4p6zhOnrSZQPHdsLWHKST3o0YC3K50dtylxyc0XzB4bakyM2xKhXaVVTBPruxUmRKmdNryJGt8XrW3LPH/PuP/MGdfU6Kd4sObcPa+xpldofUlZfrN9Y/KPKp1YrpG8UPdEbVXmOHzWtdsOwBF9S+HP1jLfVu45ZnQu2iKS80XHrgpeBXvrhh/VfuY/IYH4u4/AD+8ADR)

</div>

## Ràng buộc giá trị {#value-bindings}

Với radio, checkbox và option của select, giá trị được bind bởi `v-model` thường là chuỗi tĩnh, hoặc boolean trong trường hợp checkbox:

```vue-html
<!-- `picked` sẽ là chuỗi "a" khi được chọn -->
<input type="radio" v-model="picked" value="a" />

<!-- `toggle` sẽ là true hoặc false -->
<input type="checkbox" v-model="toggle" />

<!-- `selected` sẽ là chuỗi "abc" khi option đầu tiên được chọn -->
<select v-model="selected">
  <option value="abc">ABC</option>
</select>
```

Tuy nhiên, đôi khi ta muốn bind giá trị đó với một property động trên instance hiện tại. Ta có thể dùng `v-bind` để làm điều này. Ngoài ra, dùng `v-bind` cũng cho phép bind giá trị input với những giá trị không phải chuỗi.

### Checkbox {#checkbox-1}

```vue-html
<input
  type="checkbox"
  v-model="toggle"
  true-value="yes"
  false-value="no" />
```

`true-value` và `false-value` là các thuộc tính riêng của Vue và chỉ hoạt động với `v-model`. Ở đây, giá trị của property `toggle` sẽ được đặt thành `'yes'` khi ô được chọn, và thành `'no'` khi bỏ chọn. Bạn cũng có thể bind chúng với giá trị động bằng `v-bind`:

```vue-html
<input
  type="checkbox"
  v-model="toggle"
  :true-value="dynamicTrueValue"
  :false-value="dynamicFalseValue" />
```

:::tip Mẹo
Thuộc tính `true-value` và `false-value` không ảnh hưởng tới thuộc tính `value` của input, vì trình duyệt không gửi những checkbox chưa được chọn khi submit form. Nếu muốn đảm bảo một trong hai giá trị sẽ luôn được gửi trong form, ví dụ "yes" hoặc "no", hãy dùng radio input thay thế.
:::

### Radio {#radio-1}

```vue-html
<input type="radio" v-model="pick" :value="first" />
<input type="radio" v-model="pick" :value="second" />
```

`pick` sẽ được gán bằng giá trị của `first` khi radio input đầu tiên được chọn, và bằng giá trị của `second` khi radio input thứ hai được chọn.

### Option của select {#select-options}

```vue-html
<select v-model="selected">
  <!-- object literal inline -->
  <option :value="{ number: 123 }">123</option>
</select>
```

`v-model` cũng hỗ trợ ràng buộc giá trị không phải chuỗi. Trong ví dụ trên, khi option được chọn, `selected` sẽ được gán bằng giá trị object literal `{ number: 123 }`.

## Modifiers {#modifiers}

### `.lazy` {#lazy}

Mặc định, `v-model` đồng bộ input với state sau mỗi `input` event, ngoại trừ trường hợp nhập bằng IME như [đã nói ở trên](#vmodel-ime-tip). Bạn có thể thêm modifier `lazy` để thay vào đó chỉ đồng bộ sau `change` event:

```vue-html
<!-- đồng bộ sau "change" thay vì "input" -->
<input v-model.lazy="msg" />
```

### `.number` {#number}

Nếu muốn dữ liệu người dùng nhập vào được tự động ép kiểu thành số, bạn có thể thêm modifier `number` vào input được quản lý bởi `v-model`:

```vue-html
<input v-model.number="age" />
```

Nếu giá trị không thể được phân tích bằng `parseFloat()`, giá trị gốc dưới dạng chuỗi sẽ được dùng thay thế. Đặc biệt, nếu input đang rỗng, chẳng hạn sau khi người dùng xóa hết nội dung trong ô, thì kết quả trả về sẽ là chuỗi rỗng. Cách hoạt động này khác với [DOM property `valueAsNumber`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLInputElement#valueasnumber).

Modifier `number` sẽ được áp dụng tự động nếu input có `type="number"`.

### `.trim` {#trim}

Nếu muốn khoảng trắng ở đầu và cuối dữ liệu người dùng nhập vào được tự động cắt bỏ, bạn có thể thêm modifier `trim` vào input do `v-model` quản lý:

```vue-html
<input v-model.trim="msg" />
```

## `v-model` với component {#v-model-with-components}

> Nếu bạn chưa quen với component trong Vue, có thể tạm bỏ qua phần này.

Các loại input dựng sẵn của HTML không phải lúc nào cũng đáp ứng đủ nhu cầu của bạn. May mắn là component của Vue cho phép bạn xây dựng những input có thể tái sử dụng với hành vi được tùy biến hoàn toàn. Những input này thậm chí còn hoạt động với `v-model`. Để tìm hiểu thêm, hãy đọc phần [Sử dụng với `v-model`](/guide/components/v-model) trong hướng dẫn về Component.
