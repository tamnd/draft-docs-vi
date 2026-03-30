# Component {#components}

Cho đến nay, chúng ta chỉ làm việc với một component duy nhất. Ứng dụng Vue thực thường được tạo với các component lồng nhau.

Một component cha có thể render một component khác trong template của nó như component con. Để dùng component con, chúng ta cần import nó trước:

<div class="composition-api">
<div class="sfc">

```js
import ChildComp from './ChildComp.vue'
```

</div>
</div>

<div class="options-api">
<div class="sfc">

```js
import ChildComp from './ChildComp.vue'

export default {
  components: {
    ChildComp
  }
}
```

Chúng ta cũng cần đăng ký component bằng tùy chọn `components`. Ở đây chúng ta dùng cú pháp viết tắt object property để đăng ký component `ChildComp` dưới key `ChildComp`.

</div>
</div>

<div class="sfc">

Sau đó, chúng ta có thể dùng component trong template như:

```vue-html
<ChildComp />
```

</div>

<div class="html">

```js
import ChildComp from './ChildComp.js'

createApp({
  components: {
    ChildComp
  }
})
```

Chúng ta cũng cần đăng ký component bằng tùy chọn `components`. Ở đây chúng ta dùng cú pháp viết tắt object property để đăng ký component `ChildComp` dưới key `ChildComp`.

Vì chúng ta đang viết template trong DOM, nó sẽ phụ thuộc vào quy tắc phân tích của trình duyệt, không phân biệt hoa thường cho tên tag. Do đó, chúng ta cần dùng tên kebab-case để tham chiếu component con:

```vue-html
<child-comp></child-comp>
```

</div>


Bây giờ hãy thử tự mình làm - import component con và render nó trong template.
