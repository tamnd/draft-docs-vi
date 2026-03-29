# `v-model` Của Component {#component-v-model}

<ScrimbaLink href="https://scrimba.com/links/vue-component-v-model" title="Bài học miễn phí về `v-model` trên component trong Vue.js" type="scrimba">
  Xem bài học video tương tác trên Scrimba
</ScrimbaLink>

## Cách Dùng Cơ Bản {#basic-usage}

`v-model` có thể được dùng trên component để triển khai cơ chế ràng buộc hai chiều.

<div class="composition-api">

Từ Vue 3.4 trở đi, cách làm được khuyến nghị là dùng macro [`defineModel()`](/api/sfc-script-setup#definemodel):

```vue [Child.vue]
<script setup>
const model = defineModel()

function update() {
  model.value++
}
</script>

<template>
  <div>Giá trị v-model được bind từ cha là: {{ model }}</div>
  <button @click="update">Tăng lên</button>
</template>
```

Component cha sau đó có thể bind một giá trị bằng `v-model`:

```vue-html [Parent.vue]
<Child v-model="countModel" />
```

Giá trị do `defineModel()` trả về là một ref. Nó có thể được truy cập và thay đổi như mọi ref khác, ngoại trừ việc nó hoạt động như một liên kết hai chiều giữa giá trị ở component cha và giá trị cục bộ:

- `.value` của nó được đồng bộ với giá trị được bind từ `v-model` của component cha;
- Khi component con thay đổi nó, giá trị được bind ở component cha cũng sẽ được cập nhật.

Điều này có nghĩa là bạn cũng có thể bind ref này vào một phần tử input gốc bằng `v-model`, giúp việc bọc các phần tử input gốc mà vẫn giữ nguyên cách dùng `v-model` trở nên rất thẳng thắn:

```vue
<script setup>
const model = defineModel()
</script>

<template>
  <input v-model="model" />
</template>
```

[Thử trên Playground](https://play.vuejs.org/#eNqFUtFKwzAU/ZWYl06YLbK30Q10DFSYigq+5KW0t11mmoQknZPSf/cm3eqEsT0l555zuefmpKV3WsfbBuiUpjY3XDtiwTV6ziSvtTKOLNZcFKQ0qiZRnATkG6JB0BIDJen2kp5iMlfSOlLbisw8P4oeQAhFPpURxVV0zWSa9PNwEgIHtRaZA0SEpOvbeduG5q5LE0Sh2jvZ3tSqADFjFHlGSYJkmhz10zF1FseXvIo3VklcrfX9jOaq1lyAedGOoz1GpyQwnsvQ3fdTqDnTwPhQz9eQf52ob+zO1xh9NWDBbIHRgXOZqcD19PL9GXZ4H0h03whUnyHfwCrReI+97L6RBdo+0gW3j+H9uaw+7HLnQNrDUt6oV3ZBzyhmsjiz+p/dSTwJfUx2+IpD1ic+xz5enwQGXEDJJaw8Gl2I1upMzlc/hEvdOBR6SNKAjqP1J6P/o6XdL11L5h4=)

### Bên Dưới Hoạt Động Ra Sao {#under-the-hood}

`defineModel` là một macro tiện lợi. Trình biên dịch sẽ mở rộng nó thành:

- Một prop tên là `modelValue`, giá trị của ref cục bộ sẽ được đồng bộ với prop này;
- Một sự kiện tên là `update:modelValue`, được phát ra khi giá trị của ref cục bộ bị thay đổi.

Đây là cách bạn sẽ phải triển khai cùng component con ở trên trước phiên bản 3.4:

```vue [Child.vue]
<script setup>
const props = defineProps(['modelValue'])
const emit = defineEmits(['update:modelValue'])
</script>

<template>
  <input
    :value="props.modelValue"
    @input="emit('update:modelValue', $event.target.value)"
  />
</template>
```

Khi đó, `v-model="foo"` trong component cha sẽ được biên dịch thành:

```vue-html [Parent.vue]
<Child
  :modelValue="foo"
  @update:modelValue="$event => (foo = $event)"
/>
```

Như bạn thấy, cách này dài dòng hơn khá nhiều. Tuy nhiên, hiểu rõ điều gì đang xảy ra phía sau vẫn rất hữu ích.

Vì `defineModel` khai báo một prop, nên bạn cũng có thể khai báo option cho prop ngầm bên dưới bằng cách truyền vào `defineModel`:

```js
// bắt buộc phải có v-model
const model = defineModel({ required: true })

// cung cấp giá trị mặc định
const model = defineModel({ default: 0 })
```

:::warning
Nếu bạn đặt giá trị `default` cho prop của `defineModel` nhưng component cha không truyền giá trị nào cho prop đó, điều này có thể gây mất đồng bộ giữa component cha và component con. Trong ví dụ dưới đây, `myRef` ở component cha là `undefined`, nhưng `model` ở component con lại là `1`:

```vue [Child.vue]
<script setup>
const model = defineModel({ default: 1 })
</script>
```

```vue [Parent.vue]
<script setup>
const myRef = ref()
</script>

<template>
  <Child v-model="myRef"></Child>
</template>
```

:::

</div>

<div class="options-api">

Trước hết, hãy xem lại cách `v-model` được dùng trên một phần tử gốc:

```vue-html
<input v-model="searchText" />
```

Bên dưới, trình biên dịch template sẽ mở rộng `v-model` thành dạng dài hơn cho chúng ta. Vì vậy đoạn mã trên tương đương với:

```vue-html
<input
  :value="searchText"
  @input="searchText = $event.target.value"
/>
```

Khi dùng trên component, `v-model` sẽ được mở rộng thành:

```vue-html
<CustomInput
  :model-value="searchText"
  @update:model-value="newValue => searchText = newValue"
/>
```

Tuy vậy, để điều này hoạt động, component `<CustomInput>` phải làm hai việc:

1. Bind thuộc tính `value` của phần tử `<input>` gốc vào prop `modelValue`
2. Khi sự kiện `input` gốc được kích hoạt, phát một custom event `update:modelValue` với giá trị mới

Đây là ví dụ hoạt động:

```vue [CustomInput.vue]
<script>
export default {
  props: ['modelValue'],
  emits: ['update:modelValue']
}
</script>

<template>
  <input
    :value="modelValue"
    @input="$emit('update:modelValue', $event.target.value)"
  />
</template>
```

Lúc này `v-model` sẽ hoạt động hoàn chỉnh với component đó:

```vue-html
<CustomInput v-model="searchText" />
```

[Thử trên Playground](https://play.vuejs.org/#eNqFkctqwzAQRX9lEAEn4Np744aWrvoD3URdiHiSGvRCHpmC8b93JDfGKYGCkJjXvTrSJF69r8aIohHtcA69p6O0vfEuELzFgZx5tz4SXIIzUFT1JpfGCmmlxe/c3uFFRU0wSQtwdqxh0dLQwHSnNJep3ilS+8PSCxCQYrC3CMDgMKgrNlB8odaOXVJ2TgdvvNp6vSwHhMZrRcgRQLs1G5+M61A/S/ErKQXUR5immwXMWW1VEKX4g3j3Mo9QfXCeKU9FtvpQmp/lM0Oi6RP/qYieebHZNvyL0acLLODNmGYSxCogxVJ6yW1c2iWz/QOnEnY48kdUpMIVGSllD8t8zVZb+PkHqPG4iw==)

Một cách khác để triển khai `v-model` trong component này là dùng thuộc tính `computed` có thể ghi với cả getter lẫn setter. `get` nên trả về property `modelValue`, còn `set` nên phát ra sự kiện tương ứng:

```vue [CustomInput.vue]
<script>
export default {
  props: ['modelValue'],
  emits: ['update:modelValue'],
  computed: {
    value: {
      get() {
        return this.modelValue
      },
      set(value) {
        this.$emit('update:modelValue', value)
      }
    }
  }
}
</script>

<template>
  <input v-model="value" />
</template>
```

</div>

## Đối Số Của `v-model` {#v-model-arguments}

`v-model` trên component cũng có thể nhận một đối số:

```vue-html
<MyComponent v-model:title="bookTitle" />
```

<div class="composition-api">

Trong component con, ta có thể hỗ trợ đối số tương ứng bằng cách truyền một chuỗi vào `defineModel()` làm đối số đầu tiên:

```vue [MyComponent.vue]
<script setup>
const title = defineModel('title')
</script>

<template>
  <input type="text" v-model="title" />
</template>
```

[Thử trên Playground](https://play.vuejs.org/#eNqFklFPwjAUhf9K05dhgiyGNzJI1PCgCWqUx77McQeFrW3aOxxZ9t+9LTAXA/q2nnN6+t12Db83ZrSvgE944jIrDTIHWJmZULI02iJrmIWctSy3umQRRaPOWhweNX0pUHiyR3FP870UZkyoTCuH7FPr3VJiAWzqSwfR/rbUKyhYatdV6VugTktTQHQjVBIfeYiEFgikpwi0YizZ3M2aplfXtklMWvD6UKf+CfrUVPBuh+AspngSd718yH+hX7iS4xihjUZYQS4VLPwJgyiI/3FLZSrafzAeBqFG4jgxeuEqGTo6OZfr0dZpRVxNuFWeEa4swL4alEQm+IQFx3tpUeiv56ChrWB41rMNZLsL+tbVXhP8zYIDuyeQzkN6HyBWb88/XgJ3ZxJ95bH/MN/B6aLyjMfYQ6VWhN3LBdqn8FdJtV66eY2g3HkoD+qTbcgLTo/jX+ra6D+449E47BOq5e039mr+gA==)

Nếu cũng cần option cho prop, hãy truyền chúng sau tên model:

```js
const title = defineModel('title', { required: true })
```

<details>
<summary>Cách dùng trước 3.4</summary>

```vue [MyComponent.vue]
<script setup>
defineProps({
  title: {
    required: true
  }
})
defineEmits(['update:title'])
</script>

<template>
  <input
    type="text"
    :value="title"
    @input="$emit('update:title', $event.target.value)"
  />
</template>
```

[Thử trên Playground](https://play.vuejs.org/#eNp9kE1rwzAMhv+KMIW00DXsGtKyMXYc7D7vEBplM8QfOHJoCfnvk+1QsjJ2svVKevRKk3h27jAGFJWoh7NXjmBACu4kjdLOeoIJPHYwQ+ethoJLi1vq7fpi+WfQ0JI+lCstcrkYQJqzNQMBKeoRjhG4LcYHbVvsofFfQUcCXhrteix20tRl9sIuOCBkvSHkCKD+fjxN04Ka57rkOOlrMwu7SlVHKdIrBZRcWpc3ntiLO7t/nKHFThl899YN248ikYpP9pj1V60o6sG1TMwDU/q/FZRxgeIPgK4uGcQLSZGlamz6sHKd1afUxOoGeeT298A9bHCMKxBfE3mTSNjl1vud5x8qNa76)

</details>

</div>
<div class="options-api">

Trong trường hợp này, thay vì prop mặc định `modelValue` và sự kiện `update:modelValue`, component con nên chờ một prop `title` và phát ra sự kiện `update:title` để cập nhật giá trị ở component cha:

```vue [MyComponent.vue]
<script>
export default {
  props: ['title'],
  emits: ['update:title']
}
</script>

<template>
  <input
    type="text"
    :value="title"
    @input="$emit('update:title', $event.target.value)"
  />
</template>
```

[Thử trên Playground](https://play.vuejs.org/#eNqFUNFqwzAM/BVhCm6ha9hryMrGnvcFdR9Mo26B2DGuHFJC/n2yvZakDAohtuTTne5G8eHcrg8oSlFdTr5xtFe2Ma7zBF/Xz45vFi3B2XcG5K6Y9eKYVFZZHBK8xrMOLcGoLMDphrqUMC6Ypm18rzXp9SZjATxS8PZWAVBDLZYg+xfT1diC9t/BxGEctHFtlI2wKR78468q7ttzQcgoTcgVQPXzuh/HzAnTVBVcp/58qz+lMqHelEinElAwtCrufGIrHhJYBPdfEs53jkM4yEQpj8k+miYmc5DBcRKYZeXxqZXGukDZPF1dWhQHUiK3yl63YbZ97r6nIe6uoup6KbmFFfbRCnHGyI4iwyaPPnqffgGMlsEM)

</div>

## Nhiều Ràng Buộc `v-model` {#multiple-v-model-bindings}

Nhờ khả năng nhắm tới một prop và một sự kiện cụ thể như đã học ở [đối số của `v-model`](#v-model-arguments), giờ đây ta có thể tạo nhiều ràng buộc `v-model` trên cùng một instance component.

Mỗi `v-model` sẽ đồng bộ với một prop khác nhau mà không cần thêm option đặc biệt nào trong component:

```vue-html
<UserName
  v-model:first-name="first"
  v-model:last-name="last"
/>
```

<div class="composition-api">

```vue
<script setup>
const firstName = defineModel('firstName')
const lastName = defineModel('lastName')
</script>

<template>
  <input type="text" v-model="firstName" />
  <input type="text" v-model="lastName" />
</template>
```

[Thử trên Playground](https://play.vuejs.org/#eNqFkstuwjAQRX/F8iZUAqKKHQpIfbAoUmnVx86bKEzANLEt26FUkf+9Y4MDSAg2UWbu9fjckVv6oNRw2wAd08wUmitLDNhGTZngtZLakpZoKIkjpZY1SdCadNK3Ab3IazhowzQ2/ES0MVFIYSwpucbvxA/qJXO5FsldlKr8qDxL8EKW7kEQAQsLtapyC1gRkq3vp217mOccwf8wwLksRSlYIoMvCNkOarmEahyODAT2J4yGgtFzhx8UDf5/r6c4NEs7CNqnpxkvbO0kcVjNhCyh5AJe/SW9pBPOV3DJGvu3dsKFaiyxf8qTW9gheQwVs4Z90BDm5oF47cF/Ht4aZC75argxUmD61g9ktJC14hXoN2U5ZmJ0TILitbyq5O889KxuoB/7xRqKnwv9jdn5HqPvGnDVWwTpNJvrFSCul2efi4DeiRigqdB9RfwAI6vGM+5tj41YIvaJL9C+hOfNxerLzHYWhImhPKh3uuBnFJ/A05XoR9zRcBTOMeGo+wcs+yse)

<details>
<summary>Cách dùng trước 3.4</summary>

```vue
<script setup>
defineProps({
  firstName: String,
  lastName: String
})

defineEmits(['update:firstName', 'update:lastName'])
</script>

<template>
  <input
    type="text"
    :value="firstName"
    @input="$emit('update:firstName', $event.target.value)"
  />
  <input
    type="text"
    :value="lastName"
    @input="$emit('update:lastName', $event.target.value)"
  />
</template>
```

[Thử trên Playground](https://play.vuejs.org/#eNqNUc1qwzAMfhVjCk6hTdg1pGWD7bLDGIydlh1Cq7SGxDaOEjaC332yU6cdFNpLsPRJ348y8idj0qEHnvOi21lpkHWAvdmWSrZGW2Qjs1Azx2qrWyZoVMzQZwf2rWrhhKVZbHhGGivVTqsOWS0tfTeeKBGv+qjEMkJNdUaeNXigyCYjZIEKhNY0FQJVjBXHh+04nvicY/QOBM4VGUFhJHrwBWPDutV7aPKwslbU35Q8FCX/P+GJ4oB/T3hGpEU2m+ArfpnxytX2UEsF71abLhk9QxDzCzn7QCvVYeW7XuGyWSpH0eP6SyuxS75Eb/akOpn302LFYi8SiO8bJ5PK9DhFxV/j0yH8zOnzoWr6+SbhbifkMSwSsgByk1zzsoABFKZY2QNgGpiW57Pdrx2z3JCeI99Svvxh7g8muf2x)

</details>
</div>
<div class="options-api">

```vue
<script>
export default {
  props: {
    firstName: String,
    lastName: String
  },
  emits: ['update:firstName', 'update:lastName']
}
</script>

<template>
  <input
    type="text"
    :value="firstName"
    @input="$emit('update:firstName', $event.target.value)"
  />
  <input
    type="text"
    :value="lastName"
    @input="$emit('update:lastName', $event.target.value)"
  />
</template>
```

[Thử trên Playground](https://play.vuejs.org/#eNqNkk1rg0AQhv/KIAETSJRexYYWeuqhl9JTt4clmSSC7i7rKCnif+/ObtYkELAiujPzztejQ/JqTNZ3mBRJ2e5sZWgrVNUYbQm+WrQfskE4WN1AmuXRwQmpUELh2Qv3eJBdTTAIBbDTLluhoraA4VpjXHNwL0kuV0EIYJE6q6IFcKhsSwWk7/qkUq/nq5be+aa5JztGfrmHu8t8GtoZhI2pJaGzAMrT03YYQk0YR3BnruSOZe5CXhKnC3X7TaP3WBc+ZaOc/1kk3hDJvYILRQGfQzx3Rct8GiJZJ7fA7gg/AmesNszMrUIXFpxbwCfZSh09D0Hc7tbN6sAWm4qZf6edcZgxrMHSdA3RF7PTn1l8lTIdhbXp1/CmhOeJRNHLupv4eIaXyItPdJEFD7R8NM0Ce/d/ZCTtESnzlVZXhP/vHbeZaT0tPdf59uONfx7mDVM=)

</div>

## Xử Lý Modifier Của `v-model` {#handling-v-model-modifiers}

Khi học về ràng buộc input của form, ta đã thấy `v-model` có [modifier dựng sẵn](/guide/essentials/forms#modifiers) là `.trim`, `.number` và `.lazy`. Trong một số trường hợp, bạn cũng có thể muốn `v-model` trên component input tùy chỉnh của mình hỗ trợ thêm modifier riêng.

Hãy tạo một modifier ví dụ tên là `capitalize`, dùng để viết hoa ký tự đầu tiên của chuỗi được cung cấp bởi ràng buộc `v-model`:

```vue-html
<MyComponent v-model.capitalize="myText" />
```

<div class="composition-api">

Modifier được thêm vào `v-model` của component có thể được truy cập trong component con bằng cách destructure giá trị trả về của `defineModel()` như sau:

```vue
<script setup>
const [model, modifiers] = defineModel()

console.log(modifiers) // { capitalize: true }
</script>

<template>
  <input type="text" v-model="model" />
</template>
```

Để điều chỉnh có điều kiện cách giá trị được đọc / ghi dựa trên modifier, ta có thể truyền option `get` và `set` vào `defineModel()`. Hai option này nhận giá trị ở thời điểm get / set của model ref và phải trả về giá trị đã biến đổi. Đây là cách dùng `set` để triển khai modifier `capitalize`:

```vue
<script setup>
const [model, modifiers] = defineModel({
  set(value) {
    if (modifiers.capitalize) {
      return value.charAt(0).toUpperCase() + value.slice(1)
    }
    return value
  }
})
</script>

<template>
  <input type="text" v-model="model" />
</template>
```

[Thử trên Playground](https://play.vuejs.org/#eNp9UsFu2zAM/RVClzhY5mzoLUgHdEUPG9Bt2LLTtIPh0Ik6WRIkKksa5N9LybFrFG1OkvgeyccnHsWNc+UuoliIZai9cgQBKbpP0qjWWU9wBI8NnKDxtoUJUycDdH+4tXwzaOgMl/NRLNVlMoA0tTWBoD2scE9wnSoWk8lUmuW8a8rt+EHYOl0R8gtgtVUBlHGRoK6cokqrRwxAW4RGea6mkQg9HGwEboZ+kbKWY027961doy6f86+l6ERIAXNus5wPPcVMvNB+yZOaiZFw/cKYftI/ufEM+FCNQh/+8tRrbJTB+4QUxySWqxa7SkecQn4DqAaKIWekeyAAe0fRG8h5Zb2t/A0VH6Yl2d/Oob+tAhZTeHfGg1Y1Fh/Z6ZR66o5xhRTh8OnyXyy7f6CDSw5S59/Z3WRpOl91lAL70ahN+RCsYT/zFFIk95RG/92RYr+kWPTzSVFpbf9/zTHyEWd9vN5i/e+V+EPYp5gUPzwG9DuUYsCo8htkrQm++/Ut6x5AVh01sy+APzFYHZPGjvY5mjXLHvGy2i95K5TZrMLdntCEfqgkNDuc+VLwkqQNe2v0Z7lX5VX/M+L0BFEuPdc=)

<details>
<summary>Cách dùng trước 3.4</summary>

```vue
<script setup>
const props = defineProps({
  modelValue: String,
  modelModifiers: { default: () => ({}) }
})

const emit = defineEmits(['update:modelValue'])

function emitValue(e) {
  let value = e.target.value
  if (props.modelModifiers.capitalize) {
    value = value.charAt(0).toUpperCase() + value.slice(1)
  }
  emit('update:modelValue', value)
}
</script>

<template>
  <input type="text" :value="props.modelValue" @input="emitValue" />
</template>
```

[Thử trên Playground](https://play.vuejs.org/#eNp9Us1Og0AQfpUJF5ZYqV4JNTaNxyYmVi/igdCh3QR2N7tDIza8u7NLpdU0nmB+v5/ZY7Q0Jj10GGVR7iorDYFD6sxDoWRrtCU4gsUaBqitbiHm1ngqrfuV5j+Fik7ldH6R83u5GaBQlVaOoO03+Emw8BtFHCeFyucjKMNxQNiapiTkCGCzlw6kMh1BVRpJZSO/0AEe0Pa0l2oHve6AYdBmvj+/ZHO4bfUWm/Q8uSiiEb6IYM4A+XxCi2bRH9ZX3BgVGKuNYwFbrKXCZx+Jo0cPcG9l02EGL2SZ3mxKr/VW1hKty9hMniy7hjIQCSweQByHBIZCDWzGDwi20ps0Yjxx4MR73Jktc83OOPFHGKk7VZHUKkyFgsAEAqcG2Qif4WWYUml3yOp8wldlDSLISX+TvPDstAemLeGbVvvSLkncJSnpV2PQrkqHLOfmVHeNrFDcMz3w0iBQE1cUzMYBbuS2f55CPj4D6o0/I41HzMKsP+u0kLOPoZWzkx1X7j18A8s0DEY=)

</details>
</div>

<div class="options-api">

Modifier được thêm vào `v-model` của component sẽ được truyền vào component thông qua prop `modelModifiers`. Trong ví dụ dưới đây, ta tạo một component có prop `modelModifiers` với giá trị mặc định là một object rỗng:

```vue
<script>
export default {
  props: {
    modelValue: String,
    modelModifiers: {
      default: () => ({})
    }
  },
  emits: ['update:modelValue'],
  created() {
    console.log(this.modelModifiers) // { capitalize: true }
  }
}
</script>

<template>
  <input
    type="text"
    :value="modelValue"
    @input="$emit('update:modelValue', $event.target.value)"
  />
</template>
```

Hãy chú ý rằng prop `modelModifiers` của component có chứa `capitalize` với giá trị `true`, bởi vì nó được đặt trên ràng buộc `v-model` dưới dạng `v-model.capitalize="myText"`.

Giờ khi prop đã sẵn sàng, ta có thể kiểm tra các key trong object `modelModifiers` và viết handler để thay đổi giá trị được emit. Trong đoạn mã dưới đây, ta sẽ viết hoa chuỗi bất cứ khi nào phần tử `<input />` phát ra sự kiện `input`.

```vue
<script>
export default {
  props: {
    modelValue: String,
    modelModifiers: {
      default: () => ({})
    }
  },
  emits: ['update:modelValue'],
  methods: {
    emitValue(e) {
      let value = e.target.value
      if (this.modelModifiers.capitalize) {
        value = value.charAt(0).toUpperCase() + value.slice(1)
      }
      this.$emit('update:modelValue', value)
    }
  }
}
</script>

<template>
  <input type="text" :value="modelValue" @input="emitValue" />
</template>
```

[Thử trên Playground](https://play.vuejs.org/#eNqFks1qg0AQgF9lkIKGpqa9iikNOefUtJfaw6KTZEHdZR1DbPDdO7saf0qgIq47//PNXL2N1uG5Ri/y4io1UtNrUspCK0Owa7aK/0osCQ5GFeCHq4nMuvlJCZCUeHEOGR5EnRNcrTS92VURXGex2qXVZ4JEsOhsAQxSbcrbDaBo9nihCHyXAaC1B3/4jVdDoXwhLHQuCPkGsD/JCmSpa4JUaEkilz9YAZ7RNHSS5REaVQPXgCay9vG0rPNToTLMw9FznXhdHYkHK04Qr4Zs3tL7g2JG8B4QbZS2LLqGXK5PkdcYwTsZrs1R6RU7lcmDRDPaM7AuWARMbf0KwbVdTNk4dyyk5f3l15r5YjRm8b+dQYF0UtkY1jo4fYDDLAByZBxWCmvAkIQ5IvdoBTcLeYCAiVbhvNwJvEk4GIK5M0xPwmwoeF6EpD60RrMVFXJXj72+ymWKwUvfXt+gfVzGB1tzcKfDZec+o/LfxsTdtlCj7bSpm3Xk4tjpD8FZ+uZMWTowu7MW7S+CWR77)

</div>

### Modifier Cho `v-model` Có Đối Số {#modifiers-for-v-model-with-arguments}

<div class="composition-api">

Khi dùng `v-model` vừa có đối số vừa có modifier, ta có thể destructure kết quả trả về từ `defineModel()` để lấy object modifier:

```vue
<script setup>
const [title, titleModifiers] = defineModel('title')

console.log(titleModifiers) // { capitalize: true }
</script>
```

</div>
<div class="options-api">

Khi ràng buộc `v-model` vừa có đối số vừa có modifier, tên prop được sinh ra sẽ là `arg + "Modifiers"`. Ví dụ:

```vue-html
<MyComponent v-model:title.capitalize="myText">
```

Khai báo tương ứng sẽ là:

```js
export default {
  props: ['title', 'titleModifiers'],
  emits: ['update:title'],
  created() {
    console.log(this.titleModifiers) // { capitalize: true }
  }
}
```

</div>

Đây là một ví dụ khác về việc dùng modifier với nhiều `v-model` có đối số khác nhau:

```vue-html
<UserName
  v-model:first-name.capitalize="first"
  v-model:last-name.uppercase="last"
/>
```

<div class="composition-api">

```vue
<script setup>
const [firstName, firstNameModifiers] = defineModel('firstName')
const [lastName, lastNameModifiers] = defineModel('lastName')

console.log(firstNameModifiers) // { capitalize: true }
console.log(lastNameModifiers) // { uppercase: true }
</script>
```

<details>
<summary>Cách dùng trước 3.4</summary>

```vue
<script setup>
const props = defineProps({
  firstName: String,
  lastName: String,
  firstNameModifiers: { default: () => ({}) },
  lastNameModifiers: { default: () => ({}) }
})
defineEmits(['update:firstName', 'update:lastName'])

console.log(props.firstNameModifiers) // { capitalize: true }
console.log(props.lastNameModifiers) // { uppercase: true }
</script>
```

</details>
</div>
<div class="options-api">

```vue
<script>
export default {
  props: {
    firstName: String,
    lastName: String,
    firstNameModifiers: {
      default: () => ({})
    },
    lastNameModifiers: {
      default: () => ({})
    }
  },
  emits: ['update:firstName', 'update:lastName'],
  created() {
    console.log(this.firstNameModifiers) // { capitalize: true }
    console.log(this.lastNameModifiers) // { uppercase: true }
  }
}
</script>
```

</div>
