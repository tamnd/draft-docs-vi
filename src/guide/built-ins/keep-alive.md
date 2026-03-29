<script setup>
import SwitchComponent from './keep-alive-demos/SwitchComponent.vue'
</script>

# KeepAlive {#keepalive}

`<KeepAlive>` là một component dựng sẵn, cho phép ta cache có điều kiện các instance component khi chuyển qua lại giữa nhiều component động.

## Cách dùng cơ bản {#basic-usage}

Trong chương Kiến thức cơ bản về component, ta đã giới thiệu cú pháp của [Component động](/guide/essentials/component-basics#dynamic-components) thông qua phần tử đặc biệt `<component>`:

```vue-html
<component :is="activeComponent" />
```

Mặc định, một instance component đang hoạt động sẽ bị unmount khi ta chuyển sang component khác. Điều đó làm toàn bộ state đã thay đổi bên trong nó bị mất. Khi component đó được hiển thị lại, một instance mới sẽ được tạo ra và chỉ còn state ban đầu.

Trong ví dụ dưới đây, ta có hai component có state: A chứa bộ đếm, còn B chứa một thông điệp được đồng bộ với ô input qua `v-model`. Hãy thử thay đổi state của một trong hai component, chuyển sang component còn lại, rồi chuyển ngược lại:

<SwitchComponent />

Bạn sẽ thấy rằng khi quay lại, state đã thay đổi trước đó bị reset.

Việc tạo instance component mới khi chuyển qua lại thường là hành vi hữu ích. Nhưng trong trường hợp này, ta lại muốn giữ nguyên hai instance component ngay cả khi chúng không còn active. Để giải quyết, ta có thể bọc component động bằng component dựng sẵn `<KeepAlive>`:

```vue-html
<!-- Component không active sẽ được cache -->
<KeepAlive>
  <component :is="activeComponent" />
</KeepAlive>
```

Lúc này, state sẽ được giữ lại qua các lần chuyển component:

<SwitchComponent use-KeepAlive />

<div class="composition-api">

[Thử trên Playground](https://play.vuejs.org/#eNqtUsFOwzAM/RWrl4IGC+cqq2h3RFw495K12YhIk6hJi1DVf8dJSllBaAJxi+2XZz8/j0lhzHboeZIl1NadMA4sd73JKyVaozsHI9hnJqV+feJHmODY6RZS/JEuiL1uTTEXtiREnnINKFeAcgZUqtbKOqj7ruPKwe6s2VVguq4UJXEynAkDx1sjmeMYAdBGDFBLZu2uShre6ioJeaxIduAyp0KZ3oF7MxwRHWsEQmC4bXXDJWbmxpjLBiZ7DwptMUFyKCiJNP/BWUbO8gvnA+emkGKIgkKqRrRWfh+Z8MIWwpySpfbxn6wJKMGV4IuSs0UlN1HVJae7bxYvBuk+2IOIq7sLnph8P9u5DJv5VfpWWLaGqTzwZTCOM/M0IaMvBMihd04ruK+lqF/8Ajxms8EFbCiJxR8khsP6ncQosLWnWV6a/kUf2nqu75Fby04chA0iPftaYryhz6NBRLjdtajpHZTWPio=)

</div>
<div class="options-api">

[Thử trên Playground](https://play.vuejs.org/#eNqtU8tugzAQ/JUVl7RKWveMXFTIseofcHHAiawasPxArRD/3rVNSEhbpVUrIWB3x7PM7jAkuVL3veNJmlBTaaFsVraiUZ22sO0alcNedw2s7kmIPHS1ABQLQDEBAMqWvwVQzffMSQuDz1aI6VreWpPCEBtsJppx4wE1s+zmNoIBNLdOt8cIjzut8XAKq3A0NAIY/QNveFEyi8DA8kZJZjlGALQWPVSSGfNYJjVvujIJeaxItuMyo6JVzoJ9VxwRmtUCIdDfNV3NJWam5j7HpPOY8BEYkwxySiLLP1AWkbK4oHzmXOVS9FFOSM3jhFR4WTNfRslcO54nSwJKcCD4RsnZmJJNFPXJEl8t88quOuc39fCrHalsGyWcnJL62apYNoq12UQ8DLEFjCMy+kKA7Jy1XQtPlRTVqx+Jx6zXOJI1JbH4jejg3T+KbswBzXnFlz9Tjes/V/3CjWEHDsL/OYNvdCE8Wu3kLUQEhy+ljh+brFFu)

</div>

:::tip
Khi dùng trong [template viết trực tiếp trong DOM](/guide/essentials/component-basics#in-dom-template-parsing-caveats), nó nên được viết là `<keep-alive>`.
:::

## Bao gồm / Loại trừ {#include-exclude}

Mặc định, `<KeepAlive>` sẽ cache mọi instance component nằm bên trong nó. Ta có thể tùy biến hành vi này bằng props `include` và `exclude`. Cả hai prop đều có thể là chuỗi phân tách bằng dấu phẩy, một `RegExp`, hoặc một mảng chứa các kiểu đó:

```vue-html
<!-- chuỗi phân tách bằng dấu phẩy -->
<KeepAlive include="a,b">
  <component :is="view" />
</KeepAlive>

<!-- regex (dùng `v-bind`) -->
<KeepAlive :include="/a|b/">
  <component :is="view" />
</KeepAlive>

<!-- mảng (dùng `v-bind`) -->
<KeepAlive :include="['a', 'b']">
  <component :is="view" />
</KeepAlive>
```

Việc so khớp sẽ dựa trên option [`name`](/api/options-misc#name) của component, vì vậy các component cần được cache có điều kiện bởi `KeepAlive` phải khai báo rõ option `name`.

:::tip
Từ phiên bản 3.2.34, một single-file component dùng `<script setup>` sẽ tự suy ra option `name` từ tên file, nên không cần tự khai báo nữa.
:::

## Số instance cache tối đa {#max-cached-instances}

Ta có thể giới hạn số lượng instance component tối đa được cache bằng prop `max`. Khi `max` được chỉ định, `<KeepAlive>` sẽ hoạt động giống như [LRU cache](<https://en.wikipedia.org/wiki/Cache_replacement_policies#Least_Recently_Used_(LRU)>): nếu số instance cache sắp vượt quá giới hạn này, instance đã được truy cập cách đây lâu nhất sẽ bị hủy để nhường chỗ cho instance mới.

```vue-html
<KeepAlive :max="10">
  <component :is="activeComponent" />
</KeepAlive>
```

## Vòng đời của instance đã được cache {#lifecycle-of-cached-instance}

Khi một instance component bị gỡ khỏi DOM nhưng vẫn thuộc về một cây component được cache bởi `<KeepAlive>`, nó sẽ chuyển sang trạng thái **deactivated** thay vì bị unmount. Khi một instance component được đưa trở lại DOM như một phần của cây đã cache, nó sẽ được **activated**.

<div class="composition-api">

Một component được giữ sống có thể đăng ký hook vòng đời cho hai trạng thái này bằng [`onActivated()`](/api/composition-api-lifecycle#onactivated) và [`onDeactivated()`](/api/composition-api-lifecycle#ondeactivated):

```vue
<script setup>
import { onActivated, onDeactivated } from 'vue'

onActivated(() => {
  // được gọi ở lần mount đầu tiên
  // và mỗi lần component được đưa trở lại từ cache
})

onDeactivated(() => {
  // được gọi khi component bị gỡ khỏi DOM nhưng vẫn nằm trong cache
  // và cả khi bị unmount
})
</script>
```

</div>
<div class="options-api">

Một component được giữ sống có thể đăng ký hook vòng đời cho hai trạng thái này bằng hook [`activated`](/api/options-lifecycle#activated) và [`deactivated`](/api/options-lifecycle#deactivated):

```js
export default {
  activated() {
    // được gọi ở lần mount đầu tiên
    // và mỗi lần component được đưa trở lại từ cache
  },
  deactivated() {
    // được gọi khi component bị gỡ khỏi DOM nhưng vẫn nằm trong cache
    // và cả khi bị unmount
  }
}
```

</div>

Lưu ý:

- <span class="composition-api">`onActivated`</span><span class="options-api">`activated`</span> cũng được gọi khi mount, và <span class="composition-api">`onDeactivated`</span><span class="options-api">`deactivated`</span> cũng được gọi khi unmount.
- Cả hai hook này không chỉ áp dụng cho component gốc được cache bởi `<KeepAlive>`, mà còn áp dụng cho cả các component con cháu trong cây đã cache.

---

**Liên quan**

- [API reference của `<KeepAlive>`](/api/built-in-components#keepalive)
