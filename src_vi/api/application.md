# Application API {#application-api}

## createApp() {#createapp}

Tạo một instance ứng dụng.

- **Kiểu**

  ```ts
  function createApp(rootComponent: Component, rootProps?: object): App
  ```

- **Chi tiết**

  Đối số đầu tiên là component gốc. Đối số tùy chọn thứ hai là các props truyền vào component gốc.

- **Ví dụ**

  Với component gốc inline:

  ```js
  import { createApp } from 'vue'

  const app = createApp({
    /* tùy chọn component gốc */
  })
  ```

  Với component được import:

  ```js
  import { createApp } from 'vue'
  import App from './App.vue'

  const app = createApp(App)
  ```

- **Xem thêm** [Hướng dẫn - Tạo một Ứng dụng Vue](/guide/essentials/application)

## createSSRApp() {#createssrapp}

Tạo một instance ứng dụng ở chế độ [SSR Hydration](/guide/scaling-up/ssr#client-hydration). Cách dùng hoàn toàn giống `createApp()`.

## app.mount() {#app-mount}

Mount instance ứng dụng vào một phần tử chứa.

- **Kiểu**

  ```ts
  interface App {
    mount(rootContainer: Element | string): ComponentPublicInstance
  }
  ```

- **Chi tiết**

  Đối số có thể là một phần tử DOM thực hoặc một CSS selector (phần tử đầu tiên khớp sẽ được dùng). Trả về instance component gốc.

  Nếu component có template hoặc render function được định nghĩa, nó sẽ thay thế tất cả DOM node hiện có bên trong container. Ngược lại, nếu runtime compiler có sẵn, `innerHTML` của container sẽ được dùng làm template.

  Ở chế độ SSR hydration, nó sẽ hydrate các DOM node hiện có bên trong container. Nếu có [sai lệch hydration](/guide/scaling-up/ssr#hydration-mismatch), các DOM node hiện có sẽ được biến đổi để khớp với kết quả mong đợi.

  Mỗi instance ứng dụng chỉ được gọi `mount()` một lần.

- **Ví dụ**

  ```js
  import { createApp } from 'vue'
  const app = createApp(/* ... */)

  app.mount('#app')
  ```

  Cũng có thể mount vào một phần tử DOM thực:

  ```js
  app.mount(document.body.firstChild)
  ```

## app.unmount() {#app-unmount}

Unmount một instance ứng dụng đã được mount, kích hoạt các hook vòng đời unmount cho tất cả component trong cây component của ứng dụng.

- **Kiểu**

  ```ts
  interface App {
    unmount(): void
  }
  ```

## app.onUnmount() <sup class="vt-badge" data-text="3.5+" /> {#app-onunmount}

Đăng ký callback được gọi khi ứng dụng bị unmount.

- **Kiểu**

  ```ts
  interface App {
    onUnmount(callback: () => any): void
  }
  ```

## app.component() {#app-component}

Đăng ký component toàn cục nếu truyền vào cả tên chuỗi và định nghĩa component, hoặc lấy component đã đăng ký nếu chỉ truyền tên.

- **Kiểu**

  ```ts
  interface App {
    component(name: string): Component | undefined
    component(name: string, component: Component): this
  }
  ```

- **Ví dụ**

  ```js
  import { createApp } from 'vue'

  const app = createApp({})

  // đăng ký một options object
  app.component('MyComponent', {
    /* ... */
  })

  // lấy component đã đăng ký
  const MyComponent = app.component('MyComponent')
  ```

- **Xem thêm** [Đăng ký Component](/guide/components/registration)

## app.directive() {#app-directive}

Đăng ký directive tùy chỉnh toàn cục nếu truyền vào cả tên chuỗi và định nghĩa directive, hoặc lấy directive đã đăng ký nếu chỉ truyền tên.

- **Kiểu**

  ```ts
  interface App {
    directive(name: string): Directive | undefined
    directive(name: string, directive: Directive): this
  }
  ```

- **Ví dụ**

  ```js
  import { createApp } from 'vue'

  const app = createApp({
    /* ... */
  })

  // đăng ký (directive dạng object)
  app.directive('myDirective', {
    /* hook directive tùy chỉnh */
  })

  // đăng ký (cú pháp rút gọn dạng hàm)
  app.directive('myDirective', () => {
    /* ... */
  })

  // lấy directive đã đăng ký
  const myDirective = app.directive('myDirective')
  ```

- **Xem thêm** [Directive Tùy chỉnh](/guide/reusability/custom-directives)

## app.use() {#app-use}

Cài đặt một [plugin](/guide/reusability/plugins).

- **Kiểu**

  ```ts
  interface App {
    use(plugin: Plugin, ...options: any[]): this
  }
  ```

- **Chi tiết**

  Nhận plugin là đối số đầu tiên, và các tùy chọn plugin tùy chọn là đối số thứ hai.

  Plugin có thể là một object có phương thức `install()`, hoặc đơn giản là một hàm sẽ được dùng làm phương thức `install()`. Các tùy chọn (đối số thứ hai của `app.use()`) sẽ được chuyển tiếp đến phương thức `install()` của plugin.

  Khi `app.use()` được gọi nhiều lần với cùng một plugin, plugin chỉ được cài đặt một lần.

- **Ví dụ**

  ```js
  import { createApp } from 'vue'
  import MyPlugin from './plugins/MyPlugin'

  const app = createApp({
    /* ... */
  })

  app.use(MyPlugin)
  ```

- **Xem thêm** [Plugin](/guide/reusability/plugins)

## app.mixin() {#app-mixin}

Áp dụng một global mixin (trong phạm vi ứng dụng). Một global mixin áp dụng các option đi kèm cho mọi instance component trong ứng dụng.

:::warning Không khuyến nghị
Mixin được hỗ trợ trong Vue 3 chủ yếu để tương thích ngược, do được dùng rộng rãi trong các thư viện ecosystem. Việc dùng mixin, đặc biệt là global mixin, nên tránh trong code ứng dụng.

Để tái sử dụng logic, hãy dùng [Composable](/guide/reusability/composables) thay thế.
:::

- **Kiểu**

  ```ts
  interface App {
    mixin(mixin: ComponentOptions): this
  }
  ```

## app.provide() {#app-provide}

Cung cấp một giá trị có thể được inject trong tất cả component con trong ứng dụng.

- **Kiểu**

  ```ts
  interface App {
    provide<T>(key: InjectionKey<T> | symbol | string, value: T): this
  }
  ```

- **Chi tiết**

  Nhận injection key là đối số đầu tiên, và giá trị được cung cấp là đối số thứ hai. Trả về instance ứng dụng.

- **Ví dụ**

  ```js
  import { createApp } from 'vue'

  const app = createApp(/* ... */)

  app.provide('message', 'hello')
  ```

  Bên trong một component trong ứng dụng:

  <div class="composition-api">

  ```js
  import { inject } from 'vue'

  export default {
    setup() {
      console.log(inject('message')) // 'hello'
    }
  }
  ```

  </div>
  <div class="options-api">

  ```js
  export default {
    inject: ['message'],
    created() {
      console.log(this.message) // 'hello'
    }
  }
  ```

  </div>

- **Xem thêm**
  - [Provide / Inject](/guide/components/provide-inject)
  - [Provide ở cấp ứng dụng](/guide/components/provide-inject#app-level-provide)
  - [app.runWithContext()](#app-runwithcontext)

## app.runWithContext() {#app-runwithcontext}

- Chỉ hỗ trợ từ 3.3+

Thực thi một callback với ứng dụng hiện tại làm injection context.

- **Kiểu**

  ```ts
  interface App {
    runWithContext<T>(fn: () => T): T
  }
  ```

- **Chi tiết**

  Nhận một hàm callback và chạy callback ngay lập tức. Trong suốt lời gọi đồng bộ của callback, các lời gọi `inject()` có thể tra cứu các injection từ các giá trị được cung cấp bởi ứng dụng hiện tại, ngay cả khi không có instance component active nào. Giá trị trả về của callback cũng sẽ được trả về.

- **Ví dụ**

  ```js
  import { inject } from 'vue'

  app.provide('id', 1)

  const injected = app.runWithContext(() => {
    return inject('id')
  })

  console.log(injected) // 1
  ```

## app.version {#app-version}

Cung cấp phiên bản Vue mà ứng dụng được tạo với. Điều này hữu ích bên trong [plugin](/guide/reusability/plugins), nơi bạn có thể cần logic điều kiện dựa trên các phiên bản Vue khác nhau.

- **Kiểu**

  ```ts
  interface App {
    version: string
  }
  ```

- **Ví dụ**

  Kiểm tra phiên bản bên trong một plugin:

  ```js
  export default {
    install(app) {
      const version = Number(app.version.split('.')[0])
      if (version < 3) {
        console.warn('Plugin này yêu cầu Vue 3')
      }
    }
  }
  ```

- **Xem thêm** [Global API - version](/api/general#version)

## app.config {#app-config}

Mỗi instance ứng dụng đều có một object `config` chứa các cài đặt cấu hình cho ứng dụng đó. Bạn có thể thay đổi các thuộc tính của nó (được ghi lại bên dưới) trước khi mount ứng dụng.

```js
import { createApp } from 'vue'

const app = createApp(/* ... */)

console.log(app.config)
```

## app.config.errorHandler {#app-config-errorhandler}

Gán một trình xử lý lỗi toàn cục cho các lỗi chưa được bắt truyền ra từ trong ứng dụng.

- **Kiểu**

  ```ts
  interface AppConfig {
    errorHandler?: (
      err: unknown,
      instance: ComponentPublicInstance | null,
      // `info` là thông tin lỗi riêng của Vue,
      // ví dụ: hook vòng đời nào đã ném lỗi
      info: string
    ) => void
  }
  ```

- **Chi tiết**

  Trình xử lý lỗi nhận ba đối số: lỗi, instance component đã kích hoạt lỗi, và chuỗi thông tin chỉ định loại nguồn lỗi.

  Nó có thể bắt lỗi từ các nguồn sau:

  - Render component
  - Event handler
  - Hook vòng đời
  - Hàm `setup()`
  - Watcher
  - Hook directive tùy chỉnh
  - Hook transition

  :::tip
  Trong production, đối số thứ 3 (`info`) sẽ là mã rút gọn thay vì chuỗi thông tin đầy đủ. Bạn có thể tra cứu bảng ánh xạ mã trong [Tài liệu tra cứu mã lỗi Production](/error-reference/#runtime-errors).
  :::

- **Ví dụ**

  ```js
  app.config.errorHandler = (err, instance, info) => {
    // xử lý lỗi, ví dụ: báo cáo lên một dịch vụ
  }
  ```

- **Mặc định**

  Trình xử lý lỗi mặc định sẽ ném lại lỗi trong quá trình phát triển và ghi log lỗi trong production.
  Bạn có thể cấu hình điều này bằng thuộc tính [throwUnhandledErrorInProduction](#app-config-throwunhandlederrorinproduction).

## app.config.warnHandler {#app-config-warnhandler}

Gán một trình xử lý tùy chỉnh cho các cảnh báo runtime từ Vue.

- **Kiểu**

  ```ts
  interface AppConfig {
    warnHandler?: (
      msg: string,
      instance: ComponentPublicInstance | null,
      trace: string
    ) => void
  }
  ```

- **Chi tiết**

  Trình xử lý cảnh báo nhận thông báo cảnh báo là đối số đầu tiên, instance component nguồn là đối số thứ hai, và chuỗi trace component là đối số thứ ba.

  Nó có thể dùng để lọc các cảnh báo cụ thể nhằm giảm bớt nhiễu trong console. Mọi cảnh báo Vue nên được giải quyết trong quá trình phát triển, do đó chỉ nên dùng tính năng này trong các phiên debug để tập trung vào cảnh báo cụ thể, và cần xóa đi sau khi debug xong.

  :::tip
  Cảnh báo chỉ hoạt động trong quá trình phát triển, nên cấu hình này bị bỏ qua ở chế độ production.
  :::

- **Ví dụ**

  ```js
  app.config.warnHandler = (msg, instance, trace) => {
    // `trace` là trace cấu trúc phân cấp component
  }
  ```

## app.config.performance {#app-config-performance}

Đặt thành `true` để bật theo dõi hiệu năng khởi tạo, biên dịch, render và patch của component trong bảng performance/timeline của browser devtool. Chỉ hoạt động ở chế độ development và trong các trình duyệt hỗ trợ API [performance.mark](https://developer.mozilla.org/en-US/docs/Web/API/Performance/mark).

- **Kiểu:** `boolean`

- **Xem thêm** [Hướng dẫn - Hiệu năng](/guide/best-practices/performance)

## app.config.compilerOptions {#app-config-compileroptions}

Cấu hình các tùy chọn cho runtime compiler. Các giá trị đặt trên object này sẽ được chuyển đến trình biên dịch template trong trình duyệt và ảnh hưởng đến mọi component trong ứng dụng được cấu hình. Lưu ý bạn cũng có thể ghi đè các tùy chọn này theo từng component bằng [tùy chọn `compilerOptions`](/api/options-rendering#compileroptions).

::: warning Quan trọng
Tùy chọn cấu hình này chỉ có hiệu lực khi dùng bản build đầy đủ (tức là `vue.js` độc lập có thể biên dịch template trong trình duyệt). Nếu bạn dùng bản build chỉ có runtime với một build setup, các tùy chọn compiler phải được truyền vào `@vue/compiler-dom` qua cấu hình build tool.

- Với `vue-loader`: [truyền qua tùy chọn loader `compilerOptions`](https://vue-loader.vuejs.org/options.html#compileroptions). Xem thêm [cách cấu hình trong `vue-cli`](https://cli.vuejs.org/guide/webpack.html#modifying-options-of-a-loader).

- Với `vite`: [truyền qua tùy chọn `@vitejs/plugin-vue`](https://github.com/vitejs/vite-plugin-vue/tree/main/packages/plugin-vue#options).
  :::

### app.config.compilerOptions.isCustomElement {#app-config-compileroptions-iscustomelement}

Chỉ định phương thức kiểm tra để nhận biết các custom element native.

- **Kiểu:** `(tag: string) => boolean`

- **Chi tiết**

  Nên trả về `true` nếu tag cần được coi là custom element native. Với tag được khớp, Vue sẽ render nó như một element native thay vì cố gắng resolve nó như một Vue component.

  Các tag HTML và SVG native không cần khớp trong hàm này - trình phân tích của Vue tự động nhận biết chúng.

- **Ví dụ**

  ```js
  // coi tất cả tag bắt đầu bằng 'ion-' là custom element
  app.config.compilerOptions.isCustomElement = (tag) => {
    return tag.startsWith('ion-')
  }
  ```

- **Xem thêm** [Vue và Web Components](/guide/extras/web-components)

### app.config.compilerOptions.whitespace {#app-config-compileroptions-whitespace}

Điều chỉnh hành vi xử lý khoảng trắng trong template.

- **Kiểu:** `'condense' | 'preserve'`

- **Mặc định:** `'condense'`

- **Chi tiết**

  Vue xóa/rút gọn các ký tự khoảng trắng trong template để tạo ra output đã biên dịch hiệu quả hơn. Chiến lược mặc định là "condense", với hành vi sau:

  1. Ký tự khoảng trắng đầu/cuối bên trong một element được rút gọn thành một khoảng trắng đơn.
  2. Ký tự khoảng trắng giữa các element có chứa newline bị xóa.
  3. Các ký tự khoảng trắng liên tiếp trong text node được rút gọn thành một khoảng trắng đơn.

  Đặt tùy chọn này thành `'preserve'` sẽ tắt (2) và (3).

- **Ví dụ**

  ```js
  app.config.compilerOptions.whitespace = 'preserve'
  ```

### app.config.compilerOptions.delimiters {#app-config-compileroptions-delimiters}

Điều chỉnh các delimiter dùng cho nội suy văn bản trong template.

- **Kiểu:** `[string, string]`

- **Mặc định:** `{{ "['\u007b\u007b', '\u007d\u007d']" }}`

- **Chi tiết**

  Thường được dùng để tránh xung đột với các framework phía server cũng dùng cú pháp Mustache.

- **Ví dụ**

  ```js
  // Delimiter được đổi sang kiểu chuỗi template ES6
  app.config.compilerOptions.delimiters = ['${', '}']
  ```

### app.config.compilerOptions.comments {#app-config-compileroptions-comments}

Điều chỉnh cách xử lý comment HTML trong template.

- **Kiểu:** `boolean`

- **Mặc định:** `false`

- **Chi tiết**

  Mặc định, Vue sẽ xóa comment trong production. Đặt tùy chọn này thành `true` sẽ buộc Vue giữ lại comment ngay cả trong production. Comment luôn được giữ lại trong quá trình phát triển. Tùy chọn này thường được dùng khi Vue được kết hợp với các thư viện khác phụ thuộc vào comment HTML.

- **Ví dụ**

  ```js
  app.config.compilerOptions.comments = true
  ```

## app.config.globalProperties {#app-config-globalproperties}

Một object có thể dùng để đăng ký các thuộc tính toàn cục có thể truy cập trên bất kỳ instance component nào trong ứng dụng.

- **Kiểu**

  ```ts
  interface AppConfig {
    globalProperties: Record<string, any>
  }
  ```

- **Chi tiết**

  Đây là thay thế cho `Vue.prototype` của Vue 2, vốn không còn tồn tại trong Vue 3. Như mọi thứ toàn cục, nên dùng tiết kiệm.

  Nếu một thuộc tính toàn cục xung đột với thuộc tính riêng của component, thuộc tính riêng của component sẽ có độ ưu tiên cao hơn.

- **Cách dùng**

  ```js
  app.config.globalProperties.msg = 'hello'
  ```

  Điều này làm `msg` có sẵn bên trong template của bất kỳ component nào trong ứng dụng, và cũng có trên `this` của bất kỳ instance component nào:

  ```js
  export default {
    mounted() {
      console.log(this.msg) // 'hello'
    }
  }
  ```

- **Xem thêm** [Hướng dẫn - Mở rộng Thuộc tính Toàn cục](/guide/typescript/options-api#augmenting-global-properties) <sup class="vt-badge ts" />

## app.config.optionMergeStrategies {#app-config-optionmergestrategies}

Một object dùng để định nghĩa chiến lược hợp nhất cho các component option tùy chỉnh.

- **Kiểu**

  ```ts
  interface AppConfig {
    optionMergeStrategies: Record<string, OptionMergeFunction>
  }

  type OptionMergeFunction = (to: unknown, from: unknown) => any
  ```

- **Chi tiết**

  Một số plugin/thư viện thêm hỗ trợ cho các component option tùy chỉnh (bằng cách inject global mixin). Các option này có thể yêu cầu logic hợp nhất đặc biệt khi cùng một option cần được "hợp nhất" từ nhiều nguồn (ví dụ: mixin hoặc kế thừa component).

  Một hàm chiến lược hợp nhất có thể được đăng ký cho một option tùy chỉnh bằng cách gán nó vào object `app.config.optionMergeStrategies` với tên option làm key.

  Hàm chiến lược hợp nhất nhận giá trị của option được định nghĩa trên instance cha và con lần lượt là đối số đầu tiên và thứ hai.

- **Ví dụ**

  ```js
  const app = createApp({
    // option từ chính nó
    msg: 'Vue',
    // option từ một mixin
    mixins: [
      {
        msg: 'Hello '
      }
    ],
    mounted() {
      // các option đã hợp nhất được expose trên this.$options
      console.log(this.$options.msg)
    }
  })

  // định nghĩa chiến lược hợp nhất tùy chỉnh cho `msg`
  app.config.optionMergeStrategies.msg = (parent, child) => {
    return (parent || '') + (child || '')
  }

  app.mount('#app')
  // ghi ra 'Hello Vue'
  ```

- **Xem thêm** [Instance Component - `$options`](/api/component-instance#options)

## app.config.idPrefix <sup class="vt-badge" data-text="3.5+" /> {#app-config-idprefix}

Cấu hình tiền tố cho tất cả ID được tạo qua [useId()](/api/composition-api-helpers.html#useid) bên trong ứng dụng này.

- **Kiểu:** `string`

- **Mặc định:** `undefined`

- **Ví dụ**

  ```js
  app.config.idPrefix = 'myApp'
  ```

  ```js
  // trong một component:
  const id1 = useId() // 'myApp:0'
  const id2 = useId() // 'myApp:1'
  ```

## app.config.throwUnhandledErrorInProduction <sup class="vt-badge" data-text="3.5+" /> {#app-config-throwunhandlederrorinproduction}

Buộc các lỗi chưa được xử lý phải được ném ra ở chế độ production.

- **Kiểu:** `boolean`

- **Mặc định:** `false`

- **Chi tiết**

  Mặc định, các lỗi được ném ra bên trong một ứng dụng Vue nhưng không được xử lý rõ ràng sẽ có hành vi khác nhau giữa chế độ development và production:

  - Trong development, lỗi được ném ra và có thể làm crash ứng dụng. Điều này giúp lỗi nổi bật hơn để có thể nhận thấy và sửa trong quá trình phát triển.

  - Trong production, lỗi chỉ được ghi vào console để giảm thiểu tác động đến người dùng cuối. Tuy nhiên, điều này có thể ngăn các lỗi chỉ xảy ra trong production bị bắt bởi các dịch vụ giám sát lỗi.

  Khi đặt `app.config.throwUnhandledErrorInProduction` thành `true`, các lỗi chưa được xử lý sẽ được ném ra ngay cả ở chế độ production.
