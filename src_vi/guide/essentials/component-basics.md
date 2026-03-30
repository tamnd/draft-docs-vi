# Cơ bản về component {#components-basics}

<ScrimbaLink href="https://scrimba.com/links/vue-component-basics" title="Bài học tương tác về cơ bản component trong Vue.js" type="scrimba">
  Xem bài học video tương tác trên Scrimba
</ScrimbaLink>

Component cho phép chúng ta chia UI thành những phần độc lập và có thể tái sử dụng, đồng thời xem xét từng phần riêng rẽ. Một ứng dụng thường được tổ chức thành cây các component lồng nhau như sau:

![Cây component](./images/components.png)

<!-- https://www.figma.com/file/qa7WHDQRWuEZNRs7iZRZSI/components -->

Cách này khá giống với việc lồng các phần tử HTML gốc, nhưng Vue có mô hình component riêng giúp chúng ta đóng gói nội dung và logic tùy biến vào từng component. Vue cũng làm việc tốt với Web Components gốc. Nếu bạn muốn tìm hiểu mối quan hệ giữa Vue Component và Web Components gốc, hãy [đọc thêm tại đây](/guide/extras/web-components).

## Định nghĩa component {#defining-a-component}

Khi dùng bước build, thông thường chúng ta sẽ định nghĩa mỗi Vue component trong một tệp riêng có phần mở rộng `.vue`, còn gọi là [Single-File Component](/guide/scaling-up/sfc), hay viết tắt là SFC:

<div class="options-api">

```vue
<script>
export default {
  data() {
    return {
      count: 0
    }
  }
}
</script>

<template>
  <button @click="count++">Bạn đã bấm tôi {{ count }} lần.</button>
</template>
```

</div>
<div class="composition-api">

```vue
<script setup>
import { ref } from 'vue'

const count = ref(0)
</script>

<template>
  <button @click="count++">Bạn đã bấm tôi {{ count }} lần.</button>
</template>
```

</div>

Nếu không dùng bước build, một Vue component có thể được định nghĩa như một object JavaScript thông thường, bên trong chứa các option dành riêng cho Vue:

<div class="options-api">

```js
export default {
  data() {
    return {
      count: 0
    }
  },
  template: `
    <button @click="count++">
      Bạn đã bấm tôi {{ count }} lần.
    </button>`
}
```

</div>
<div class="composition-api">

```js
import { ref } from 'vue'

export default {
  setup() {
    const count = ref(0)
    return { count }
  },
  template: `
    <button @click="count++">
      Bạn đã bấm tôi {{ count }} lần.
    </button>`
  // Cũng có thể trỏ tới một in-DOM template:
  // template: '#my-template-element'
}
```

</div>

Ở đây, template được viết inline dưới dạng chuỗi JavaScript và Vue sẽ biên dịch nó ngay khi chạy. Bạn cũng có thể dùng một selector theo ID trỏ tới một phần tử, thường là phần tử `<template>` gốc, và Vue sẽ lấy nội dung của nó làm nguồn template.

Ví dụ trên định nghĩa một component duy nhất và export nó làm default export của một tệp `.js`, nhưng bạn cũng có thể dùng named export để xuất nhiều component từ cùng một tệp.

## Sử dụng component {#using-a-component}

:::tip
Trong phần còn lại của hướng dẫn này, chúng tôi sẽ dùng cú pháp SFC. Các khái niệm xoay quanh component vẫn như nhau dù bạn có dùng bước build hay không. Phần [Ví dụ](/examples/) có minh họa cách dùng component trong cả hai trường hợp.
:::

Để dùng một component con, chúng ta cần import nó vào component cha. Giả sử component bộ đếm của chúng ta nằm trong tệp `ButtonCounter.vue`, component đó sẽ được export mặc định từ tệp:

<div class="options-api">

```vue
<script>
import ButtonCounter from './ButtonCounter.vue'

export default {
  components: {
    ButtonCounter
  }
}
</script>

<template>
  <h1>Đây là một component con!</h1>
  <ButtonCounter />
</template>
```

Để component đã import có thể dùng được trong template, chúng ta cần [đăng ký](/guide/components/registration) nó bằng option `components`. Sau đó component sẽ có thể được dùng như một thẻ với đúng key mà nó được đăng ký.

</div>

<div class="composition-api">

```vue
<script setup>
import ButtonCounter from './ButtonCounter.vue'
</script>

<template>
  <h1>Đây là một component con!</h1>
  <ButtonCounter />
</template>
```

Với `<script setup>`, các component được import vào sẽ tự động có sẵn trong template.

</div>

Bạn cũng có thể đăng ký component theo kiểu toàn cục, nhờ đó nó dùng được ở mọi component trong ứng dụng mà không cần import lại. Ưu và nhược điểm của đăng ký toàn cục so với cục bộ được nói kỹ hơn trong phần [Đăng ký Component](/guide/components/registration).

Component có thể được dùng lại bao nhiêu lần tùy ý:

```vue-html
<h1>Đây là nhiều component con!</h1>
<ButtonCounter />
<ButtonCounter />
<ButtonCounter />
```

<div class="options-api">

[Thử trên Playground](https://play.vuejs.org/#eNqVUE1LxDAQ/StjLqusNHotcfHj4l8QcontLBtsJiGdiFL6301SdrEqyEJyeG9m3ps3k3gIoXlPKFqhxi7awDtN1gUfGR4Ts6cnn4gxwj56B5tGrtgyutEEoAk/6lCPe5MGhqmwnc9KhMRjuxCwFi3UrCk/JU/uGTC6MBjGglgdbnfPGBFM/s7QJ3QHO/TfxC+UzD21d72zPItU8uQrrsWvnKsT/ZW2N2wur45BI3KKdETlFlmphZsF58j/RgdQr3UJuO8G273daVFFtlstahngxSeoNezBIUzTYgPzDGwdjk1VkYvMj4jzF0nwsyQ=)

</div>
<div class="composition-api">

[Thử trên Playground](https://play.vuejs.org/#eNqVj91KAzEQhV/lmJsqlY3eSlr8ufEVhNys6ZQGNz8kE0GWfXez2SJUsdCLuZiZM9+ZM4qnGLvPQuJBqGySjYxMXOJWe+tiSIznwhz8SyieKWGfgsOqkyfTGbDSXsmFUG9rw+Ti0DPNHavD/faVEqGv5Xr/BXOwww4mVBNPnvOVklXTtKeO8qKhkj++4lb8+fL/mCMS7TEdAy6BtDfBZ65fVgA2s+L67uZMUEC9N0s8msGaj40W7Xa91qKtgbdQ0Ha0gyOM45E+TWDrKHeNIhfMr0DTN4U0me8=)

</div>

Hãy chú ý rằng khi bấm vào các nút, mỗi nút đều giữ giá trị `count` riêng của nó. Đó là vì mỗi lần bạn dùng một component, một **instance** mới của component đó sẽ được tạo ra.

Trong SFC, nên dùng tên thẻ theo kiểu `PascalCase` cho component con để dễ phân biệt với phần tử HTML gốc. Dù tên thẻ HTML gốc không phân biệt hoa thường, Vue SFC là một định dạng được biên dịch nên chúng ta có thể dùng tên thẻ có phân biệt hoa thường. Chúng ta cũng có thể dùng `/>` để đóng thẻ.

Nếu bạn viết template trực tiếp trong DOM, ví dụ như nội dung của phần tử `<template>` gốc, thì template đó sẽ chịu cách phân tích HTML mặc định của trình duyệt. Trong trường hợp này, bạn cần dùng `kebab-case` và phải viết thẻ đóng đầy đủ cho component:

```vue-html
<!-- nếu template này được viết trong DOM -->
<button-counter></button-counter>
<button-counter></button-counter>
<button-counter></button-counter>
```

Xem thêm phần [những lưu ý khi phân tích in-DOM template](#in-dom-template-parsing-caveats) để biết chi tiết.

## Truyền props {#passing-props}

Nếu đang xây dựng một blog, rất có thể chúng ta sẽ cần một component đại diện cho một bài viết. Ta muốn mọi bài viết có cùng bố cục hiển thị, nhưng nội dung thì khác nhau. Một component như vậy sẽ không hữu ích nếu bạn không thể truyền dữ liệu cho nó, chẳng hạn như tiêu đề và nội dung của bài viết cần hiển thị. Đó chính là lúc props phát huy tác dụng.

Props là những thuộc tính tùy chỉnh mà bạn có thể đăng ký trên component. Để truyền tiêu đề cho component bài viết blog của mình, ta phải khai báo nó trong danh sách props mà component chấp nhận, bằng <span class="options-api">option [`props`](/api/options-state#props)</span><span class="composition-api">macro [`defineProps`](/api/sfc-script-setup#defineprops-defineemits)</span>:

<div class="options-api">

```vue [BlogPost.vue]
<script>
export default {
  props: ['title']
}
</script>

<template>
  <h4>{{ title }}</h4>
</template>
```

Khi một giá trị được truyền vào thuộc tính prop, nó sẽ trở thành một property trên component instance đó. Giá trị của property này có thể được truy cập trong template và trong ngữ cảnh `this` của component, giống như các property khác của component.

</div>
<div class="composition-api">

```vue [BlogPost.vue]
<script setup>
defineProps(['title'])
</script>

<template>
  <h4>{{ title }}</h4>
</template>
```

`defineProps` là một macro ở thời điểm biên dịch, chỉ dùng được bên trong `<script setup>` và không cần import một cách tường minh. Những props được khai báo sẽ tự động có sẵn trong template. `defineProps` cũng trả về một object chứa toàn bộ props đã truyền vào component để chúng ta có thể truy cập chúng trong JavaScript khi cần:

```js
const props = defineProps(['title'])
console.log(props.title)
```

Xem thêm: [Khai báo kiểu cho props của component](/guide/typescript/composition-api#typing-component-props) <sup class="vt-badge ts" />

Nếu bạn không dùng `<script setup>`, props nên được khai báo bằng option `props`, và object props sẽ được truyền vào `setup()` như đối số thứ nhất:

```js
export default {
  props: ['title'],
  setup(props) {
    console.log(props.title)
  }
}
```

</div>

Một component có thể có bao nhiêu props tùy ý, và mặc định thì bất kỳ giá trị nào cũng có thể được truyền vào bất kỳ prop nào.

Sau khi prop được đăng ký, bạn có thể truyền dữ liệu cho nó như một thuộc tính tùy chỉnh, ví dụ:

```vue-html
<BlogPost title="Hành trình của tôi với Vue" />
<BlogPost title="Viết blog với Vue" />
<BlogPost title="Vì sao Vue thú vị đến vậy" />
```

Tuy nhiên, trong một ứng dụng thực tế, rất có thể bạn sẽ có một mảng các bài viết trong component cha:

<div class="options-api">

```js
export default {
  // ...
  data() {
    return {
      posts: [
        { id: 1, title: 'Hành trình của tôi với Vue' },
        { id: 2, title: 'Viết blog với Vue' },
        { id: 3, title: 'Vì sao Vue thú vị đến vậy' }
      ]
    }
  }
}
```

</div>
<div class="composition-api">

```js
const posts = ref([
  { id: 1, title: 'Hành trình của tôi với Vue' },
  { id: 2, title: 'Viết blog với Vue' },
  { id: 3, title: 'Vì sao Vue thú vị đến vậy' }
])
```

</div>

Lúc đó bạn sẽ muốn render một component cho từng phần tử bằng `v-for`:

```vue-html
<BlogPost
  v-for="post in posts"
  :key="post.id"
  :title="post.title"
 />
```

<div class="options-api">

[Thử trên Playground](https://play.vuejs.org/#eNp9UU1rhDAU/CtDLrawVfpxklRo74We2kPtQdaoaTUJ8bmtiP+9ia6uC2VBgjOZeXnz3sCejAkPnWAx4+3eSkNJqmRjtCU817p81S2hsLpBEEYL4Q1BqoBUid9Jmosi62rC4Nm9dn4lFLXxTGAt5dG482eeUXZ1vdxbQZ1VCwKM0zr3x4KBATKPcbsDSapFjOClx5d2JtHjR1KFN9fTsfbWcXdy+CZKqcqL+vuT/r3qvQqyRatRdMrpF/nn/DNhd7iPR+v8HCDRmDoj4RHxbfyUDjeFto8p8yEh1Rw2ZV4JxN+iP96FMvest8RTTws/gdmQ8HUr7ikere+yHduu62y//y3NWG38xIOpeODyXcoE8OohGYZ5VhhHHjl83sD4B3XgyGI=)

</div>
<div class="composition-api">

[Thử trên Playground](https://play.vuejs.org/#eNp9kU9PhDAUxL/KpBfWBCH+OZEuid5N9qSHrQezFKhC27RlDSF8d1tYQBP1+N78OpN5HciD1sm54yQj1J6M0A6Wu07nTIpWK+MwwPASI0qjWkQejVbpsVHVQVl30ZJ0WQRHjwFMnpT0gPZLi32w2h2DMEAUGW5iOOEaniF66vGuOiN5j0/hajx7B4zxxt5ubIiphKz+IO828qXugw5hYRXKTnqSydcrJmk61/VF/eB4q5s3x8Pk6FJjauDO16Uye0ZCBwg5d2EkkED2wfuLlogibMOTbMpf9tMwP8jpeiMfRdM1l8Tk+/F++Y6Cl0Lyg1Ha7o7R5Bn9WwSg9X0+DPMxMI409fPP1PELlVmwdQ==)

</div>

Hãy để ý cách [`v-bind`](/api/built-in-directives#v-bind), tức `:title="post.title"`, được dùng để truyền giá trị prop động. Điều này đặc biệt hữu ích khi bạn không biết trước chính xác nội dung mình sẽ render.

Tạm thời, bạn chỉ cần biết về props đến đây là đủ. Nhưng sau khi đọc xong trang này và đã thấy quen với nội dung của nó, chúng tôi khuyên bạn nên quay lại để đọc hướng dẫn đầy đủ về [props](/guide/components/props).

## Lắng nghe sự kiện {#listening-to-events}

Khi phát triển component `<BlogPost>`, một số tính năng có thể cần giao tiếp ngược lên component cha. Ví dụ, ta có thể muốn thêm một tính năng hỗ trợ khả năng truy cập để phóng to phần chữ của bài viết blog, trong khi vẫn giữ phần còn lại của trang ở kích thước mặc định.

Ở component cha, ta có thể hỗ trợ tính năng này bằng cách thêm `postFontSize` dưới dạng <span class="options-api">data property</span><span class="composition-api">ref</span>:

<div class="options-api">

```js{6}
data() {
  return {
    posts: [
      /* ... */
    ],
    postFontSize: 1
  }
}
```

</div>
<div class="composition-api">

```js{5}
const posts = ref([
  /* ... */
])

const postFontSize = ref(1)
```

</div>

Sau đó dùng nó trong template để điều khiển cỡ chữ của toàn bộ bài viết blog:

```vue-html{1,7}
<div :style="{ fontSize: postFontSize + 'em' }">
  <BlogPost
    v-for="post in posts"
    :key="post.id"
    :title="post.title"
   />
</div>
```

Bây giờ hãy thêm một nút vào template của component `<BlogPost>`:

```vue{5} [BlogPost.vue]
<!-- bỏ qua phần <script> -->
<template>
  <div class="blog-post">
    <h4>{{ title }}</h4>
    <button>Phóng to chữ</button>
  </div>
</template>
```

Hiện tại nút này chưa làm gì cả. Chúng ta muốn khi bấm vào nút, component con sẽ báo cho component cha biết rằng cần tăng cỡ chữ của tất cả bài viết. Để giải quyết việc này, component có một hệ thống custom event. Component cha có thể lắng nghe bất kỳ event nào trên component con bằng `v-on` hoặc `@`, giống như khi làm việc với sự kiện DOM gốc:

```vue-html{3}
<BlogPost
  ...
  @enlarge-text="postFontSize += 0.1"
 />
```

Sau đó component con có thể phát event từ chính nó bằng cách gọi [**`$emit`**](/api/component-instance#emit) — phương thức có sẵn của Vue — và truyền vào tên event:

```vue{5} [BlogPost.vue]
<!-- bỏ qua phần <script> -->
<template>
  <div class="blog-post">
    <h4>{{ title }}</h4>
    <button @click="$emit('enlarge-text')">Phóng to chữ</button>
  </div>
</template>
```

Nhờ listener `@enlarge-text="postFontSize += 0.1"`, component cha sẽ nhận được event và cập nhật giá trị của `postFontSize`.

<div class="options-api">

[Thử trên Playground](https://play.vuejs.org/#eNqNUsFOg0AQ/ZUJMaGNbbHqidCmmujNxMRED9IDhYWuhV0CQy0S/t1ZYIEmaiRkw8y8N/vmMZVxl6aLY8EM23ByP+Mprl3Bk1RmCPexjJ5ljhBmMgFzYemEIpiuAHAFOzXQgIVeESNUKutL4gsmMLfbBPStVFTP1Bl46E2mup4xLDKhI4CUsMR+1zFABTywYTkD5BgzG8ynEj4kkVgJnxz38Eqaut5jxvXAUCIiLqI/8TcD/m1fKhTwHHIJYSEIr+HbnqikPkqBL/yLSMs23eDooNexel8pQJaksYeMIgAn4EewcyxjtnKNCsK+zbgpXILJEnW30bCIN7ZTPcd5KDNqoWjARWufa+iyfWBlV13wYJRvJtWVJhiKGyZiL4vYHNkJO8wgaQVXi6UGr51+Ndq5LBqMvhyrH9eYGePtOVu3n3YozWSqFsBsVJmt3SzhzVaYY2nm9l82+7GX5zTGjlTM1SyNmy5SeX+7rqr2r0NdOxbFXWVXIEoBGz/m/oHIF0rB5Pz6KTV6aBOgEo7Vsn51ov4GgAAf2A==)

</div>
<div class="composition-api">

[Thử trên Playground](https://play.vuejs.org/#eNp1Uk1PwkAQ/SuTxqQYgYp6ahaiJngzITHRA/UAZQor7W7TnaK16X93th8UEuHEvPdm5s3bls5Tmo4POTq+I0yYyZTAIOXpLFAySXVGUEKGEVQQZToBl6XukXqO9XahDbXc2OsAO5FlAIEKtWJByqCBqR01WFqiBLnxYTIEkhSjD+5rAV86zxQW8C1pB+88Aaphr73rtXbNVqrtBeV9r/zYFZYHacBoiHLFykB9Xgfq1NmLVvQmf7E1OGFaeE0anAMXhEkarwhtRWIjD+AbKmKcBk4JUdvtn8+6ARcTu87hLuCf6NJpSoDDKNIZj7BtIFUTUuB0tL/HomXHcnOC18d1TF305COqeJVtcUT4Q62mtzSF2/GkE8/E8b1qh8Ljw/if8I7nOkPn9En/+Ug2GEmFi0ynZrB0azOujbfB54kki5+aqumL8bING28Yr4xh+2vePrI39CnuHmZl2TwwVJXwuG6ZdU6kFTyGsQz33HyFvH5wvvyaB80bACwgvKbrYgLVH979DQc=)

</div>

Chúng ta cũng có thể khai báo trước những event sẽ được emit bằng <span class="options-api">option [`emits`](/api/options-state#emits)</span><span class="composition-api">macro [`defineEmits`](/api/sfc-script-setup#defineprops-defineemits)</span>:

<div class="options-api">

```vue{4} [BlogPost.vue]
<script>
export default {
  props: ['title'],
  emits: ['enlarge-text']
}
</script>
```

</div>
<div class="composition-api">

```vue{3} [BlogPost.vue]
<script setup>
defineProps(['title'])
defineEmits(['enlarge-text'])
</script>
```

</div>

Việc này giúp tài liệu hóa tất cả event mà component phát ra và cũng có thể [kiểm tra tính hợp lệ của chúng](/guide/components/events#events-validation). Nó cũng giúp Vue tránh việc ngầm áp chúng thành native listener trên root element của component con.

<div class="composition-api">

Tương tự `defineProps`, `defineEmits` chỉ dùng được trong `<script setup>` và không cần import. Nó trả về một hàm `emit` tương đương với phương thức `$emit`. Hàm này có thể được dùng để phát event trong phần `<script setup>` của component, nơi `$emit` không thể truy cập trực tiếp:

```vue
<script setup>
const emit = defineEmits(['enlarge-text'])

emit('enlarge-text')
</script>
```

Xem thêm: [Khai báo kiểu cho emits của component](/guide/typescript/composition-api#typing-component-emits) <sup class="vt-badge ts" />

Nếu bạn không dùng `<script setup>`, bạn có thể khai báo event phát ra bằng option `emits`. Khi đó bạn có thể truy cập hàm `emit` như một thuộc tính của setup context, được truyền vào `setup()` dưới dạng đối số thứ hai:

```js
export default {
  emits: ['enlarge-text'],
  setup(props, ctx) {
    ctx.emit('enlarge-text')
  }
}
```

</div>

Tạm thời, bạn chỉ cần biết về custom event của component đến đây là đủ. Nhưng sau khi đọc xong trang này và đã thấy quen với nội dung của nó, chúng tôi khuyên bạn nên quay lại để đọc hướng dẫn đầy đủ về [custom events](/guide/components/events).

## Phân phối nội dung bằng slot {#content-distribution-with-slots}

Giống như với phần tử HTML, rất nhiều khi chúng ta cần truyền nội dung vào component, ví dụ:

```vue-html
<AlertBox>
  Có lỗi xảy ra.
</AlertBox>
```

Khi đó nó có thể render thành kiểu như sau:

:::danger Đây là lỗi để minh họa
Có lỗi xảy ra.
:::

Điều này có thể thực hiện bằng phần tử `<slot>` riêng của Vue:

```vue{4} [AlertBox.vue]
<template>
  <div class="alert-box">
    <strong>Đây là lỗi để minh họa</strong>
    <slot />
  </div>
</template>

<style scoped>
.alert-box {
  /* ... */
}
</style>
```

Như bạn thấy ở trên, chúng ta dùng `<slot>` làm chỗ chờ tại nơi mình muốn nội dung được chèn vào. Chỉ vậy thôi là xong.

<div class="options-api">

[Thử trên Playground](https://play.vuejs.org/#eNpVUcFOwzAM/RUTDruwFhCaUCmThsQXcO0lbbKtIo0jx52Kpv07TreWouTynl+en52z2oWQnXqrClXGhtrA28q3XUBi2DlL/IED7Ak7WGX5RKQHq8oDVN4Oo9TYve4dwzmxDcp7bz3HAs5/LpfKyy3zuY0Atl1wmm1CXE5SQeLNX9hZPrb+ALU2cNQhWG9NNkrnLKIt89lGPahlyDTVogVAadoTNE7H+F4pnZTrGodKjUUpRyb0h+0nEdKdRL3CW7GmfNY5ZLiiMhfP/ynG0SL/OAuxwWCNMNncbVqSQyrgfrPZvCVcIxkrxFMYIKJrDZA1i8qatGl72ehLGEY6aGNkNwU8P96YWjffB8Lem/Xkvn9NR6qy+fRd14FSgopvmtQmzTT9Toq9VZdfIpa5jQ==)

</div>
<div class="composition-api">

[Thử trên Playground](https://play.vuejs.org/#eNpVUEtOwzAQvcpgFt3QBBCqUAiRisQJ2GbjxG4a4Xis8aQKqnp37PyUyqv3mZn3fBVH55JLr0Umcl9T6xi85t4VpW07h8RwNJr4Cwc4EXawS9KFiGO70ubpNBcmAmDdOSNZR8T5Yg0IoOQf7DSfW9tAJRWcpXPaapWM1nVt8ObpukY8ie29GHNzAiBX7QVqI73/LIWMzn2FQylGMcieCW1TfBMhPYSoE5zFitLVZ5BhQnkadt6nGKt5/jMafI1Oq8Ak6zW4xrEaDVIGj4fD4SPiCknpQLy4ATyaVgFptVH2JFXb+wze3DDSTioV/iaD1+eZqWT92xD2Vu2X7af3+IJ6G7/UToVigpJnTzwTO42eWDnELsTtH/wUqH4=)

</div>

Tạm thời, bạn chỉ cần biết về slot đến đây là đủ. Nhưng sau khi đọc xong trang này và đã thấy quen với nội dung của nó, chúng tôi khuyên bạn nên quay lại để đọc hướng dẫn đầy đủ về [slot](/guide/components/slots).

## Component động {#dynamic-components}

Đôi khi, việc chuyển đổi qua lại giữa các component một cách động sẽ rất hữu ích, chẳng hạn như trong giao diện tab:

<div class="options-api">

[Mở ví dụ trên Playground](https://play.vuejs.org/#eNqNVE2PmzAQ/Ssj9kArLSHbrXpwk1X31mMPvS17cIxJrICNbJMmivLfO/7AEG2jRiDkefP85sNmztlr3y8OA89ItjJMi96+VFJ0vdIWfqqOQ6NVB/midIYj5sn9Sxlrkt9b14RXzXbiMElEO5IAKsmPnljzhg6thbNDmcLdkktrSADAJ/IYlj5MXEc9Z1w8VFNLP30ed2luBy1HC4UHrVH2N90QyJ1kHnUALN1gtLeIQu6juEUMkb8H5sXHqiS+qzK1Cw3Lu76llqMFsKrFAVhLjVlXWc07VWUeR89msFbhhhAWDkWjNJIwPgjp06iy5CV7fgrOOTgKv+XoKIIgpnoGyiymSmZ1wnq9dqJweZ8p/GCtYHtUmBMdLXFitgDnc9ju68b0yxDO1WzRTEcFRLiUJsEqSw3wwi+rMpFDj0psEq5W5ax1aBp7at1y4foWzq5R0hYN7UR7ImCoNIXhWjTfnW+jdM01gaf+CEa1ooYHzvnMVWhaiwEP90t/9HBP61rILQJL3POMHw93VG+FLKzqUYx3c2yjsOaOwNeRO2B8zKHlzBKQWJNH1YHrplV/iiMBOliFILYNK5mOKdSTMviGCTyNojFdTKBoeWNT3s8f/Vpsd7cIV61gjHkXnotR6OqVkJbrQKdsv9VqkDWBh2bpnn8VXaDcHPexE4wFzsojO9eDUOSVPF+65wN/EW7sHRsi5XaFqaexn+EH9Xcpe8zG2eWG3O0/NVzUaeJMk+jGhUXlNPXulw5j8w7t2bi8X32cuf/Vv/wF/SL98A==)

</div>
<div class="composition-api">

[Mở ví dụ trên Playground](https://play.vuejs.org/#eNqNVMGOmzAQ/ZURe2BXCiHbrXpwk1X31mMPvS1V5RiTWAEb2SZNhPLvHdvggLZRE6TIM/P8/N5gpk/e2nZ57HhCkrVhWrQWDLdd+1pI0bRKW/iuGg6VVg2ky9wFDp7G8g9lrIl1H80Bb5rtxfFKMcRzUA+aV3AZQKEEhWRKGgus05pL+5NuYeNwj6mTkT4VckRYujVY63GT17twC6/Fr4YjC3kp5DoPNtEgBpY3bU0txwhgXYojsJoasymSkjeqSHweK9vOWoUbXIC/Y1YpjaDH3wt39hMI6TUUSYSQAz8jArPT5Mj+nmIhC6zpAu1TZlEhmXndbBwpXH5NGL6xWrADMsyaMj1lkAzQ92E7mvYe8nCcM24xZApbL5ECiHCSnP73KyseGnvh6V/XedwS2pVjv3C1ziddxNDYc+2WS9fC8E4qJW1W0UbUZwKGSpMZrkX11dW2SpdcE3huT2BULUp44JxPSpmmpegMgU/tyadbWpZC7jCxwj0v+OfTDdU7ITOrWiTjzTS3Vei8IfB5xHZ4PmqoObMEJHryWXXkuqrVn+xEgHZWYRKbh06uLyv4iQq+oIDnkXSQiwKymlc26n75WNdit78FmLWCMeZL+GKMwlKrhLRcBzhlh51WnSwJPFQr9/zLdIZ007w/O6bR4MQe2bseBJMzer5yzwf8MtzbOzYMkNsOY0+HfoZv1d+lZJGMg8fNqdsfbbio4b77uRVv7I0Li8xxZN1PHWbeHdyTWXc/+zgw/8t/+QsROe9h)

</div>

Điều này được thực hiện nhờ phần tử `<component>` của Vue kết hợp với thuộc tính đặc biệt `is`:

<div class="options-api">

```vue-html
<!-- Component thay đổi khi currentTab thay đổi -->
<component :is="currentTab"></component>
```

</div>
<div class="composition-api">

```vue-html
<!-- Component thay đổi khi currentTab thay đổi -->
<component :is="tabs[currentTab]"></component>
```

</div>

Trong ví dụ trên, giá trị truyền vào `:is` có thể là một trong hai loại sau:

- chuỗi tên của một component đã được đăng ký, hoặc
- chính object component đã được import

Bạn cũng có thể dùng thuộc tính `is` để tạo các phần tử HTML thông thường.

Khi chuyển đổi giữa nhiều component bằng `<component :is="...">`, một component sẽ bị unmount khi không còn được chọn nữa. Chúng ta có thể buộc các component không hoạt động vẫn được giữ "sống" bằng built-in [`<KeepAlive>` component](/guide/built-ins/keep-alive).

## Những lưu ý khi phân tích in-DOM template {#in-dom-template-parsing-caveats}

Nếu bạn viết template Vue trực tiếp trong DOM, Vue sẽ phải lấy chuỗi template từ DOM. Điều này dẫn đến một vài điểm cần lưu ý do cách trình duyệt phân tích HTML gốc.

:::tip
Cần lưu ý rằng những hạn chế được nói bên dưới chỉ áp dụng khi bạn viết template trực tiếp trong DOM. Chúng KHÔNG áp dụng nếu bạn dùng template chuỗi đến từ các nguồn sau:

- Single-File Components
- Chuỗi template viết inline (ví dụ `template: '...'`)
- `<script type="text/x-template">`
  :::

### Không phân biệt hoa thường {#case-insensitivity}

Tên thẻ HTML và tên thuộc tính HTML không phân biệt chữ hoa chữ thường, nên trình duyệt sẽ hiểu mọi ký tự in hoa thành chữ thường. Điều đó có nghĩa là khi bạn dùng in-DOM template, tên component kiểu PascalCase, tên prop kiểu camelCase, hay tên event của `v-on` đều phải được viết lại dưới dạng kebab-case, tức dùng dấu gạch nối:

```js
// camelCase trong JavaScript
const BlogPost = {
  props: ['postTitle'],
  emits: ['updatePost'],
  template: `
    <h3>{{ postTitle }}</h3>
  `
}
```

```vue-html
<!-- kebab-case trong HTML -->
<blog-post post-title="xin chào!" @update-post="onUpdatePost"></blog-post>
```

### Thẻ tự đóng {#self-closing-tags}

Trong các ví dụ trước, chúng ta đã dùng thẻ tự đóng cho component:

```vue-html
<MyComponent />
```

Đó là vì bộ phân tích template của Vue hiểu `/>` là ký hiệu kết thúc của mọi loại thẻ.

Tuy nhiên, với in-DOM template, chúng ta luôn phải viết thẻ đóng đầy đủ:

```vue-html
<my-component></my-component>
```

Lý do là vì đặc tả HTML chỉ cho phép [một vài phần tử nhất định](https://html.spec.whatwg.org/multipage/syntax.html#void-elements) được bỏ qua thẻ đóng, thường gặp nhất là `<input>` và `<img>`. Với các phần tử khác, nếu bạn bỏ thẻ đóng, bộ phân tích HTML gốc sẽ nghĩ rằng bạn chưa kết thúc thẻ mở. Ví dụ, đoạn sau:

```vue-html
<my-component /> <!-- chúng ta định đóng thẻ ở đây... -->
<span>xin chào</span>
```

sẽ được phân tích thành:

```vue-html
<my-component>
  <span>xin chào</span>
</my-component> <!-- nhưng trình duyệt sẽ đóng nó ở đây. -->
```

### Hạn chế về vị trí của phần tử {#element-placement-restrictions}

Một số phần tử HTML, như `<ul>`, `<ol>`, `<table>`, và `<select>`, có giới hạn về những phần tử nào được phép xuất hiện bên trong chúng. Đồng thời, một số phần tử như `<li>`, `<tr>`, và `<option>` cũng chỉ được phép xuất hiện bên trong một vài phần tử nhất định.

Điều này sẽ gây ra vấn đề khi dùng component cùng với các phần tử có kiểu giới hạn như vậy. Ví dụ:

```vue-html
<table>
  <blog-post-row></blog-post-row>
</table>
```

Custom component `<blog-post-row>` sẽ bị đẩy ra ngoài vì bị xem là nội dung không hợp lệ, và điều đó dẫn đến lỗi trong kết quả render cuối cùng. Chúng ta có thể dùng [`is` attribute](/api/built-in-special-attributes#is) đặc biệt như một cách xử lý:

```vue-html
<table>
  <tr is="vue:blog-post-row"></tr>
</table>
```

:::tip
Khi được dùng trên phần tử HTML gốc, giá trị của `is` phải có tiền tố `vue:` để được hiểu là một Vue component. Điều này là cần thiết để tránh nhầm lẫn với [customized built-in elements](https://html.spec.whatwg.org/multipage/custom-elements.html#custom-elements-customized-builtin-example) của HTML gốc.
:::

Tạm thời, bạn chỉ cần biết về những lưu ý khi phân tích in-DOM template đến đây là đủ, và thực ra đây cũng là phần cuối của _Essentials_ trong Vue. Chúc mừng bạn! Vẫn còn nhiều điều để học, nhưng trước hết, chúng tôi khuyên bạn nên nghỉ một chút rồi tự thử nghịch với Vue, làm một thứ gì đó vui vui, hoặc xem qua phần [Ví dụ](/examples/) nếu bạn chưa xem.

Khi bạn đã thấy quen với những kiến thức vừa học xong, hãy tiếp tục phần hướng dẫn để tìm hiểu sâu hơn về component.
