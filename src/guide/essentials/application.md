# Tạo một ứng dụng Vue {#creating-a-vue-application}

## Instance của ứng dụng {#the-application-instance}

Mỗi ứng dụng Vue đều bắt đầu bằng việc tạo một **application instance** mới bằng hàm [`createApp`](/api/application#createapp):

```js
import { createApp } from 'vue'

const app = createApp({
  /* root component options */
})
```

## Component gốc {#the-root-component}

Object mà chúng ta truyền vào `createApp` thật ra chính là một component. Mỗi ứng dụng đều cần một "root component" để chứa các component khác bên trong nó.

Nếu bạn dùng Single-File Components, thông thường chúng ta sẽ import root component từ một tệp khác:

```js
import { createApp } from 'vue'
// import root component App từ một single-file component.
import App from './App.vue'

const app = createApp(App)
```

Nhiều ví dụ trong phần hướng dẫn này chỉ cần một component duy nhất, nhưng phần lớn ứng dụng thực tế đều được tổ chức thành một cây các component lồng nhau và có thể tái sử dụng. Ví dụ, cây component của một ứng dụng Todo có thể trông như sau:

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

Ở các phần sau của hướng dẫn, chúng ta sẽ nói về cách định nghĩa và ghép nhiều component lại với nhau. Còn trước mắt, hãy tập trung vào những gì diễn ra bên trong một component đơn lẻ.

## Mount ứng dụng {#mounting-the-app}

Một application instance sẽ chưa render gì cả cho đến khi phương thức `.mount()` của nó được gọi. Phương thức này nhận vào một đối số là phần tử chứa, có thể là một phần tử DOM thật hoặc một chuỗi selector:

```html
<div id="app"></div>
```

```js
app.mount('#app')
```

Nội dung của root component sẽ được render vào bên trong phần tử chứa. Bản thân phần tử chứa không được tính là một phần của ứng dụng.

Phương thức `.mount()` luôn nên được gọi sau khi bạn đã cấu hình xong ứng dụng và đăng ký xong các asset cần thiết. Cũng cần lưu ý rằng giá trị trả về của nó, khác với các phương thức đăng ký asset, là root component instance chứ không phải application instance.

### Template của component gốc viết ngay trong DOM {#in-dom-root-component-template}

Template của root component thường nằm ngay trong chính component đó. Tuy nhiên, bạn cũng có thể cung cấp template riêng bằng cách viết trực tiếp nó bên trong phần tử chứa:

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

Vue sẽ tự động dùng `innerHTML` của phần tử chứa làm template nếu root component chưa có sẵn option `template`.

Kiểu template viết ngay trong DOM này thường được dùng trong những ứng dụng [sử dụng Vue mà không có bước build](/guide/quick-start.html#using-vue-from-cdn). Cách này cũng có thể dùng cùng với các framework phía server, nơi template gốc có thể được server tạo ra một cách động.

## Cấu hình ứng dụng {#app-configurations}

Instance ứng dụng cung cấp một object `.config` để chúng ta thiết lập một số tùy chọn ở cấp ứng dụng. Ví dụ, bạn có thể định nghĩa một trình xử lý lỗi ở cấp ứng dụng để bắt lỗi từ tất cả component con:

```js
app.config.errorHandler = (err) => {
  /* handle error */
}
```

Instance ứng dụng cũng cung cấp một vài phương thức để đăng ký các asset ở phạm vi ứng dụng. Ví dụ, đăng ký một component:

```js
app.component('TodoDeleteButton', TodoDeleteButton)
```

Việc này sẽ giúp `TodoDeleteButton` có thể được dùng ở bất cứ đâu trong ứng dụng. Chúng ta sẽ nói kỹ hơn về việc đăng ký component và các loại asset khác ở những phần sau của hướng dẫn. Bạn cũng có thể xem đầy đủ danh sách API của application instance trong phần [API reference](/api/application).

Hãy nhớ áp dụng toàn bộ cấu hình ứng dụng trước khi mount ứng dụng.

## Nhiều instance ứng dụng {#multiple-application-instances}

Bạn không bị giới hạn chỉ một application instance trên cùng một trang. API `createApp` cho phép nhiều ứng dụng Vue cùng tồn tại trên một trang, mỗi ứng dụng có phạm vi cấu hình và asset toàn cục riêng:

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

Nếu bạn dùng Vue để tăng cường HTML do server render sẵn và chỉ cần Vue điều khiển một vài phần cụ thể trên một trang lớn, đừng mount một Vue application instance duy nhất lên toàn bộ trang. Thay vào đó, hãy tạo nhiều application instance nhỏ và mount chúng lên đúng những phần tử mà chúng phụ trách.
