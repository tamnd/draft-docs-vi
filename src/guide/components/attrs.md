---
outline: deep
---

# Thuộc Tính Kế Thừa {#fallthrough-attributes}

> Trang này giả định rằng bạn đã đọc [Kiến thức cơ bản về Component](/guide/essentials/component-basics). Nếu chưa quen với component, hãy đọc trang đó trước.

## Kế Thừa Thuộc Tính {#attribute-inheritance}

"Thuộc tính kế thừa" là một thuộc tính hoặc listener sự kiện `v-on` được truyền vào component nhưng không được khai báo tường minh trong [props](./props) hoặc [emits](./events#declaring-emitted-events) của component nhận. Một vài ví dụ phổ biến là các thuộc tính `class`, `style` và `id`.

Khi một component render ra đúng một phần tử gốc, các thuộc tính kế thừa sẽ tự động được thêm vào thuộc tính của phần tử gốc đó. Ví dụ, giả sử component `<MyButton>` có template như sau:

```vue-html
<!-- template của <MyButton> -->
<button>Click Me</button>
```

Và component cha dùng nó như sau:

```vue-html
<MyButton class="large" />
```

DOM cuối cùng được render sẽ là:

```html
<button class="large">Click Me</button>
```

Ở đây, `<MyButton>` không khai báo `class` là một prop được chấp nhận. Vì vậy, `class` được xem là thuộc tính kế thừa và tự động được thêm vào phần tử gốc của `<MyButton>`.

### Gộp `class` và `style` {#class-and-style-merging}

Nếu phần tử gốc của component con đã có sẵn thuộc tính `class` hoặc `style`, chúng sẽ được gộp với giá trị `class` và `style` được kế thừa từ component cha. Giả sử ta đổi template của `<MyButton>` trong ví dụ trước thành:

```vue-html
<!-- template của <MyButton> -->
<button class="btn">Click Me</button>
```

Khi đó DOM cuối cùng sẽ trở thành:

```html
<button class="btn large">Click Me</button>
```

### Kế Thừa Listener `v-on` {#v-on-listener-inheritance}

Quy tắc tương tự cũng áp dụng cho listener sự kiện `v-on`:

```vue-html
<MyButton @click="onClick" />
```

Listener `click` sẽ được thêm vào phần tử gốc của `<MyButton>`, tức là phần tử `<button>` gốc. Khi `<button>` gốc được click, phương thức `onClick` của component cha sẽ được gọi. Nếu `<button>` gốc đã có listener `click` được bind bằng `v-on`, cả hai listener đều sẽ chạy.

### Kế Thừa Qua Component Lồng Nhau {#nested-component-inheritance}

Nếu một component render một component khác làm node gốc của nó, ví dụ ta refactor `<MyButton>` để nó render `<BaseButton>` làm gốc:

```vue-html
<!-- template của <MyButton/> chỉ đơn giản render một component khác -->
<BaseButton />
```

Khi đó, các thuộc tính kế thừa mà `<MyButton>` nhận được sẽ tự động được chuyển tiếp tới `<BaseButton>`.

Lưu ý rằng:

1. Các thuộc tính được chuyển tiếp không bao gồm những thuộc tính đã được `<MyButton>` khai báo là prop hoặc listener `v-on` của các sự kiện đã khai báo. Nói cách khác, các prop và listener đã khai báo đã được `<MyButton>` "tiêu thụ".

2. Các thuộc tính được chuyển tiếp có thể được `<BaseButton>` tiếp nhận như props nếu component đó đã khai báo.

## Tắt Kế Thừa Thuộc Tính {#disabling-attribute-inheritance}

Nếu bạn **không** muốn một component tự động kế thừa thuộc tính, bạn có thể đặt `inheritAttrs: false` trong options của component.

<div class="composition-api">

 Từ bản 3.3, bạn cũng có thể dùng trực tiếp [`defineOptions`](/api/sfc-script-setup#defineoptions) trong `<script setup>`:

```vue
<script setup>
defineOptions({
  inheritAttrs: false
})
// ...logic setup
</script>
```

</div>

Trường hợp phổ biến để tắt kế thừa thuộc tính là khi các thuộc tính cần được áp dụng lên phần tử khác thay vì node gốc. Bằng cách đặt `inheritAttrs` thành `false`, bạn có toàn quyền quyết định nơi các thuộc tính kế thừa sẽ được áp dụng.

Bạn có thể truy cập trực tiếp các thuộc tính kế thừa này trong biểu thức template dưới dạng `$attrs`:

```vue-html
<span>Thuộc tính kế thừa: {{ $attrs }}</span>
```

Object `$attrs` bao gồm mọi thuộc tính không được khai báo trong options `props` hoặc `emits` của component (ví dụ `class`, `style`, listener `v-on`, v.v.).

Một vài lưu ý:

- Khác với props, thuộc tính kế thừa giữ nguyên kiểu chữ gốc của chúng trong JavaScript, vì vậy một thuộc tính như `foo-bar` phải được truy cập bằng `$attrs['foo-bar']`.

- Một listener sự kiện `v-on` như `@click` sẽ được lộ ra trên object dưới dạng một hàm nằm ở `$attrs.onClick`.

Quay lại ví dụ `<MyButton>` ở [phần trước](#attribute-inheritance), đôi khi ta cần bọc phần tử `<button>` thật bằng một `<div>` bổ sung để phục vụ việc styling:

```vue-html
<div class="btn-wrapper">
  <button class="btn">Click Me</button>
</div>
```

Ta muốn mọi thuộc tính kế thừa như `class` và listener `v-on` được áp dụng vào `<button>` bên trong, không phải `<div>` bên ngoài. Ta có thể làm điều đó với `inheritAttrs: false` và `v-bind="$attrs"`:

```vue-html{2}
<div class="btn-wrapper">
  <button class="btn" v-bind="$attrs">Click Me</button>
</div>
```

Hãy nhớ rằng [`v-bind` không có đối số](/guide/essentials/template-syntax#dynamically-binding-multiple-attributes) sẽ bind toàn bộ property của một object thành thuộc tính của phần tử đích.

## Kế Thừa Thuộc Tính Với Nhiều Node Gốc {#attribute-inheritance-on-multiple-root-nodes}

Không giống component chỉ có một node gốc, component có nhiều node gốc không có hành vi tự động kế thừa thuộc tính. Nếu `$attrs` không được bind tường minh, Vue sẽ phát ra cảnh báo lúc chạy.

```vue-html
<CustomLayout id="custom-layout" @click="changeValue" />
```

Nếu `<CustomLayout>` có template nhiều node gốc như sau, Vue sẽ cảnh báo vì nó không thể biết phải áp dụng các thuộc tính kế thừa vào đâu:

```vue-html
<header>...</header>
<main>...</main>
<footer>...</footer>
```

Cảnh báo sẽ biến mất nếu `$attrs` được bind tường minh:

```vue-html{2}
<header>...</header>
<main v-bind="$attrs">...</main>
<footer>...</footer>
```

## Truy Cập Thuộc Tính Kế Thừa Trong JavaScript {#accessing-fallthrough-attributes-in-javascript}

<div class="composition-api">

Khi cần, bạn có thể truy cập các thuộc tính kế thừa của component trong `<script setup>` bằng API `useAttrs()`:

```vue
<script setup>
import { useAttrs } from 'vue'

const attrs = useAttrs()
</script>
```

Nếu không dùng `<script setup>`, `attrs` sẽ được lộ ra như một property của context `setup()`:

```js
export default {
  setup(props, ctx) {
    // thuộc tính kế thừa nằm ở ctx.attrs
    console.log(ctx.attrs)
  }
}
```

Lưu ý rằng dù object `attrs` ở đây luôn phản ánh các thuộc tính kế thừa mới nhất, nó không phải là reactive (vì lý do hiệu năng). Bạn không thể dùng watcher để quan sát thay đổi của nó. Nếu cần tính phản ứng, hãy dùng prop. Hoặc bạn có thể dùng `onUpdated()` để thực hiện side effect với `attrs` mới nhất sau mỗi lần cập nhật.

</div>

<div class="options-api">

Khi cần, bạn có thể truy cập các thuộc tính kế thừa của component thông qua property instance `$attrs`:

```js
export default {
  created() {
    console.log(this.$attrs)
  }
}
```

</div>
