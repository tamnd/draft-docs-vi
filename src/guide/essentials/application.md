# Tạo một ứng dụng Vue {#creating-a-vue-application}

## Instance của ứng dụng {#the-application-instance}

Mỗi ứng dụng Vue bắt đầu bằng việc tạo một **application instance (instance ứng dụng)** mới với hàm [`createApp`](/api/application#createapp):

```js
import { createApp } from 'vue'

const app = createApp({
  /* root component options (các tùy chọn của component gốc) */
})
```

## Root Component (Component gốc) {#the-root-component}

Đối tượng mà chúng ta truyền vào `createApp` thực chất là một component. Mỗi app đều cần một "root component (component gốc)" có thể chứa các component khác như là các phần tử con của nó.

Nếu bạn đang sử dụng Single-File Components (SFC - component trong một file), thông thường chúng ta import root component từ một file khác:

```js
import { createApp } from 'vue'
// import root component App từ một single-file component.
import App from './App.vue'

const app = createApp(App)
```

Trong khi nhiều ví dụ trong hướng dẫn này chỉ cần một component, hầu hết các ứng dụng thực tế được tổ chức thành một cây (tree) gồm nhiều component lồng nhau và có thể tái sử dụng. Ví dụ, cây component của một ứng dụng Todo có thể trông như sau:

```
App (root component)
├─ TodoList
│  └─ TodoItem
│     ├─ TodoDeleteButton
│     └─ TodoEditButton
└─ TodoFooter
   ├─ TodoClearButton
   └─ TodoStatistics
```

Ở các phần sau của hướng dẫn, chúng ta sẽ bàn về cách định nghĩa và kết hợp (compose) nhiều component lại với nhau. Trước đó, chúng ta sẽ tập trung vào những gì xảy ra bên trong một component đơn lẻ.

## Mounting App (gắn ứng dụng vào DOM) {#mounting-the-app}

Một application instance sẽ không render (hiển thị) gì cho đến khi phương thức `.mount()` của nó được gọi. Nó yêu cầu một đối số "container (phần tử chứa)", có thể là một DOM element thật hoặc một chuỗi selector:

```html
<div id="app"></div>
```

```js
app.mount('#app')
```

Nội dung của root component của app sẽ được render bên trong phần tử container. Bản thân phần tử container không được xem là một phần của app.

Phương thức `.mount()` luôn phải được gọi sau khi tất cả cấu hình của app và việc đăng ký asset (tài nguyên) đã hoàn tất. Ngoài ra lưu ý rằng giá trị trả về của nó, khác với các phương thức đăng ký asset, là instance của root component thay vì application instance.

### Template của Root Component trong DOM (In-DOM Root Component Template) {#in-dom-root-component-template}

Template của root component thường là một phần của chính component đó, nhưng cũng có thể cung cấp template riêng bằng cách viết trực tiếp nó bên trong container được mount:

```html
<div id="app">
  <button @click="count++">{{ count }}</button>
</div>
```

```js
import { createApp } from 'vue'

const app = createApp({
  data() {
    return {
      count: 0
    }
  }
})

app.mount('#app')
```

Vue sẽ tự động sử dụng `innerHTML` của container làm template nếu root component chưa có tùy chọn `template`.

Template trong DOM (In-DOM templates) thường được dùng trong các ứng dụng [sử dụng Vue mà không có build step (bước build)](/guide/quick-start.html#using-vue-from-cdn). Chúng cũng có thể được dùng cùng với các framework phía server (server-side frameworks), nơi mà template gốc có thể được tạo động bởi server.

## Cấu hình App {#app-configurations}

Application instance cung cấp một object `.config` cho phép chúng ta cấu hình một số tùy chọn ở cấp độ app, ví dụ như định nghĩa một error handler (bộ xử lý lỗi) cấp app để bắt lỗi từ tất cả các component con:

```js
app.config.errorHandler = (err) => {
  /* xử lý lỗi */
}
```

Application instance cũng cung cấp một số phương thức để đăng ký các asset (tài nguyên) ở phạm vi app. Ví dụ, đăng ký một component:

```js
app.component('TodoDeleteButton', TodoDeleteButton)
```

Điều này giúp `TodoDeleteButton` có thể được sử dụng ở bất kỳ đâu trong app của chúng ta. Chúng ta sẽ thảo luận về việc đăng ký component và các loại asset khác ở các phần sau của hướng dẫn. Bạn cũng có thể xem toàn bộ danh sách API của application instance trong phần [API reference (tài liệu API)](/api/application).

Hãy đảm bảo áp dụng tất cả cấu hình của app trước khi mount app!

## Nhiều Application Instance {#multiple-application-instances}

Bạn không bị giới hạn chỉ một application instance trên cùng một trang. API `createApp` cho phép nhiều ứng dụng Vue cùng tồn tại trên một trang, mỗi ứng dụng có phạm vi cấu hình và asset global riêng:

```js
const app1 = createApp({
  /* ... */
})
app1.mount('#container-1')

const app2 = createApp({
  /* ... */
})
app2.mount('#container-2')
```

Nếu bạn sử dụng Vue để tăng cường (enhance) HTML được render từ server và chỉ cần Vue kiểm soát một số phần cụ thể của trang lớn, hãy tránh mount một application instance duy nhất cho toàn bộ trang. Thay vào đó, tạo nhiều application instance nhỏ và mount chúng vào các phần tử mà chúng chịu trách nhiệm quản lý.
