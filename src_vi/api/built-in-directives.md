# Directive Dựng sẵn {#built-in-directives}

## v-text {#v-text}

Cập nhật nội dung text của element.

- **Nhận:** `string`

- **Chi tiết**

  `v-text` hoạt động bằng cách thiết lập thuộc tính [textContent](https://developer.mozilla.org/en-US/docs/Web/API/Node/textContent) của element, vì vậy nó sẽ ghi đè lên mọi nội dung hiện có bên trong element. Nếu bạn chỉ cần cập nhật một phần `textContent`, hãy dùng [nội suy Mustache](/guide/essentials/template-syntax#text-interpolation) thay thế (tức là <span v-pre>`<span>Giữ phần này nhưng cập nhật {{phầnĐộng}}</span>`</span>).

- **Ví dụ**

  ```vue-html
  <span v-text="msg"></span>
  <!-- tương đương với -->
  <span>{{msg}}</span>
  ```

- **Xem thêm** [Cú pháp Template - Nội suy Văn bản](/guide/essentials/template-syntax#text-interpolation)

## v-html {#v-html}

Cập nhật [innerHTML](https://developer.mozilla.org/en-US/docs/Web/API/Element/innerHTML) của element.

- **Nhận:** `string`

- **Chi tiết**

  Nội dung của `v-html` được chèn vào dưới dạng HTML thuần - cú pháp template Vue sẽ không được xử lý. Nếu bạn đang cố gắng soạn template bằng `v-html`, hãy suy nghĩ lại và dùng component thay thế.

  ::: warning Lưu ý Bảo mật
  Render HTML tùy ý trên trang web của bạn có thể rất nguy hiểm vì nó có thể dễ dàng dẫn đến [tấn công XSS](https://en.wikipedia.org/wiki/Cross-site_scripting). Chỉ dùng `v-html` với nội dung đáng tin cậy và **không bao giờ** với nội dung do người dùng cung cấp.
  :::

  Trong [Single-File Component](/guide/scaling-up/sfc), style có `scoped` sẽ không áp dụng cho nội dung bên trong `v-html`, vì HTML đó không được xử lý bởi trình biên dịch template của Vue. Nếu bạn muốn nhắm mục tiêu nội dung `v-html` bằng CSS có phạm vi, bạn có thể dùng [CSS modules](./sfc-css-features#css-modules) hoặc thêm một element `<style>` toàn cục với chiến lược phạm vi thủ công như BEM.

- **Ví dụ**

  ```vue-html
  <div v-html="html"></div>
  ```

- **Xem thêm** [Cú pháp Template - HTML Thô](/guide/essentials/template-syntax#raw-html)

## v-show {#v-show}

Chuyển trạng thái hiển thị của element dựa trên tính truthy của giá trị biểu thức.

- **Nhận:** `any`

- **Chi tiết**

  `v-show` hoạt động bằng cách thiết lập thuộc tính CSS `display` qua inline style, và sẽ cố giữ lại giá trị `display` ban đầu khi element hiển thị. Nó cũng kích hoạt transition khi điều kiện thay đổi.

- **Xem thêm** [Kết xuất Có Điều kiện - v-show](/guide/essentials/conditional#v-show)

## v-if {#v-if}

Render có điều kiện một element hoặc fragment template dựa trên tính truthy của giá trị biểu thức.

- **Nhận:** `any`

- **Chi tiết**

  Khi một element `v-if` được chuyển trạng thái, element và các directive/component bên trong nó bị hủy và tái tạo. Nếu điều kiện ban đầu là falsy, nội dung bên trong sẽ không được render.

  Có thể dùng trên `<template>` để biểu thị một khối có điều kiện chỉ chứa text hoặc nhiều element.

  Directive này kích hoạt transition khi điều kiện thay đổi.

  Khi dùng cùng nhau, `v-if` có độ ưu tiên cao hơn `v-for`. Chúng tôi không khuyến nghị dùng hai directive này cùng nhau trên một element - xem [hướng dẫn kết xuất danh sách](/guide/essentials/list#v-for-with-v-if) để biết chi tiết.

- **Xem thêm** [Kết xuất Có Điều kiện - v-if](/guide/essentials/conditional#v-if)

## v-else {#v-else}

Biểu thị "khối else" cho `v-if` hoặc chuỗi `v-if` / `v-else-if`.

- **Không nhận biểu thức**

- **Chi tiết**

  - Ràng buộc: element anh em liền trước phải có `v-if` hoặc `v-else-if`.

  - Có thể dùng trên `<template>` để biểu thị một khối có điều kiện chỉ chứa text hoặc nhiều element.

- **Ví dụ**

  ```vue-html
  <div v-if="Math.random() > 0.5">
    Bây giờ bạn thấy tôi
  </div>
  <div v-else>
    Bây giờ bạn không thấy
  </div>
  ```

- **Xem thêm** [Kết xuất Có Điều kiện - v-else](/guide/essentials/conditional#v-else)

## v-else-if {#v-else-if}

Biểu thị "khối else if" cho `v-if`. Có thể nối chuỗi.

- **Nhận:** `any`

- **Chi tiết**

  - Ràng buộc: element anh em liền trước phải có `v-if` hoặc `v-else-if`.

  - Có thể dùng trên `<template>` để biểu thị một khối có điều kiện chỉ chứa text hoặc nhiều element.

- **Ví dụ**

  ```vue-html
  <div v-if="type === 'A'">
    A
  </div>
  <div v-else-if="type === 'B'">
    B
  </div>
  <div v-else-if="type === 'C'">
    C
  </div>
  <div v-else>
    Không phải A/B/C
  </div>
  ```

- **Xem thêm** [Kết xuất Có Điều kiện - v-else-if](/guide/essentials/conditional#v-else-if)

## v-for {#v-for}

Render element hoặc khối template nhiều lần dựa trên dữ liệu nguồn.

- **Nhận:** `Array | Object | number | string | Iterable`

- **Chi tiết**

  Giá trị của directive phải dùng cú pháp đặc biệt `alias in expression` để cung cấp alias cho phần tử đang được lặp:

  ```vue-html
  <div v-for="item in items">
    {{ item.text }}
  </div>
  ```

  Ngoài ra, bạn cũng có thể chỉ định alias cho index (hoặc key nếu dùng với Object):

  ```vue-html
  <div v-for="(item, index) in items"></div>
  <div v-for="(value, key) in object"></div>
  <div v-for="(value, name, index) in object"></div>
  ```

  Hành vi mặc định của `v-for` sẽ cố vá các element tại chỗ mà không di chuyển chúng. Để buộc nó sắp xếp lại các element, bạn nên cung cấp gợi ý sắp xếp bằng thuộc tính đặc biệt `key`:

  ```vue-html
  <div v-for="item in items" :key="item.id">
    {{ item.text }}
  </div>
  ```

  `v-for` cũng có thể hoạt động với các giá trị triển khai [Iterable Protocol](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols#The_iterable_protocol), bao gồm `Map` và `Set` native.

- **Xem thêm**
  - [Kết xuất Danh sách](/guide/essentials/list)

## v-on {#v-on}

Gắn event listener vào element.

- **Cú pháp rút gọn:** `@`

- **Nhận:** `Function | Inline Statement | Object (không có đối số)`

- **Đối số:** `event` (tùy chọn khi dùng cú pháp Object)

- **Modifier**

  - `.stop` - gọi `event.stopPropagation()`.
  - `.prevent` - gọi `event.preventDefault()`.
  - `.capture` - thêm event listener ở chế độ capture.
  - `.self` - chỉ kích hoạt handler nếu sự kiện được phát ra từ chính element này.
  - `.{keyAlias}` - chỉ kích hoạt handler với các phím nhất định.
  - `.once` - kích hoạt handler tối đa một lần.
  - `.left` - chỉ kích hoạt handler cho sự kiện nút chuột trái.
  - `.right` - chỉ kích hoạt handler cho sự kiện nút chuột phải.
  - `.middle` - chỉ kích hoạt handler cho sự kiện nút chuột giữa.
  - `.passive` - gắn DOM event với `{ passive: true }`.

- **Chi tiết**

  Loại sự kiện được biểu thị bằng đối số. Biểu thức có thể là tên phương thức, inline statement, hoặc bỏ qua nếu có modifier.

  Khi dùng trên element thông thường, nó lắng nghe chỉ [**sự kiện DOM native**](https://developer.mozilla.org/en-US/docs/Web/Events). Khi dùng trên component element tùy chỉnh, nó lắng nghe **custom event** được emit từ component con đó.

  Khi lắng nghe sự kiện DOM native, phương thức nhận sự kiện native là đối số duy nhất. Nếu dùng inline statement, statement có thể truy cập thuộc tính đặc biệt `$event`: `v-on:click="handle('ok', $event)"`.

  `v-on` cũng hỗ trợ ràng buộc đến một object các cặp event/listener mà không có đối số. Lưu ý khi dùng cú pháp object, nó không hỗ trợ modifier.

- **Ví dụ**

  ```vue-html
  <!-- method handler -->
  <button v-on:click="doThis"></button>

  <!-- sự kiện động -->
  <button v-on:[event]="doThis"></button>

  <!-- inline statement -->
  <button v-on:click="doThat('hello', $event)"></button>

  <!-- cú pháp rút gọn -->
  <button @click="doThis"></button>

  <!-- cú pháp rút gọn sự kiện động -->
  <button @[event]="doThis"></button>

  <!-- chặn nổi bọt -->
  <button @click.stop="doThis"></button>

  <!-- chặn hành vi mặc định -->
  <button @click.prevent="doThis"></button>

  <!-- chặn hành vi mặc định không có biểu thức -->
  <form @submit.prevent></form>

  <!-- nối chuỗi modifier -->
  <button @click.stop.prevent="doThis"></button>

  <!-- key modifier dùng keyAlias -->
  <input @keyup.enter="onEnter" />

  <!-- sự kiện click sẽ được kích hoạt tối đa một lần -->
  <button v-on:click.once="doThis"></button>

  <!-- cú pháp object -->
  <button v-on="{ mousedown: doThis, mouseup: doThat }"></button>
  ```

  Lắng nghe custom event trên component con (handler được gọi khi "my-event" được emit từ con):

  ```vue-html
  <MyComponent @my-event="handleThis" />

  <!-- inline statement -->
  <MyComponent @my-event="handleThis(123, $event)" />
  ```

- **Xem thêm**
  - [Xử lý Sự kiện](/guide/essentials/event-handling)
  - [Component - Custom Event](/guide/essentials/component-basics#listening-to-events)

## v-bind {#v-bind}

Ràng buộc động một hoặc nhiều thuộc tính, hoặc một prop component với một biểu thức.

- **Cú pháp rút gọn:**
  - `:` hoặc `.` (khi dùng modifier `.prop`)
  - Bỏ qua giá trị (khi tên thuộc tính và giá trị ràng buộc trùng nhau, yêu cầu 3.4+)

- **Nhận:** `any (có đối số) | Object (không có đối số)`

- **Đối số:** `attrOrProp (tùy chọn)`

- **Modifier**

  - `.camel` - chuyển đổi tên thuộc tính kebab-case thành camelCase.
  - `.prop` - buộc ràng buộc được đặt làm DOM property (3.2+).
  - `.attr` - buộc ràng buộc được đặt làm DOM attribute (3.2+).

- **Cách dùng**

  Khi dùng để ràng buộc thuộc tính `class` hoặc `style`, `v-bind` hỗ trợ thêm các kiểu giá trị như Array hoặc Object. Xem phần hướng dẫn được liên kết bên dưới để biết thêm chi tiết.

  Khi thiết lập ràng buộc trên một element, Vue mặc định kiểm tra xem element có key được định nghĩa là property bằng kiểm tra toán tử `in` không. Nếu property được định nghĩa, Vue sẽ đặt giá trị đó là DOM property thay vì attribute. Điều này hoạt động trong hầu hết trường hợp, nhưng bạn có thể ghi đè hành vi này bằng cách dùng rõ ràng modifier `.prop` hoặc `.attr`. Điều này đôi khi cần thiết, đặc biệt khi [làm việc với custom element](/guide/extras/web-components#passing-dom-properties).

  Khi dùng cho ràng buộc prop component, prop phải được khai báo đúng trong component con.

  Khi dùng không có đối số, có thể dùng để ràng buộc một object chứa các cặp tên-giá trị thuộc tính.

- **Ví dụ**

  ```vue-html
  <!-- ràng buộc một thuộc tính -->
  <img v-bind:src="imageSrc" />

  <!-- tên thuộc tính động -->
  <button v-bind:[key]="value"></button>

  <!-- cú pháp rút gọn -->
  <img :src="imageSrc" />

  <!-- cú pháp rút gọn tên trùng nhau (3.4+), mở rộng thành :src="src" -->
  <img :src />

  <!-- cú pháp rút gọn tên thuộc tính động -->
  <button :[key]="value"></button>

  <!-- kết hợp nối chuỗi inline -->
  <img :src="'/path/to/images/' + fileName" />

  <!-- ràng buộc class -->
  <div :class="{ red: isRed }"></div>
  <div :class="[classA, classB]"></div>
  <div :class="[classA, { classB: isB, classC: isC }]"></div>

  <!-- ràng buộc style -->
  <div :style="{ fontSize: size + 'px' }"></div>
  <div :style="[styleObjectA, styleObjectB]"></div>

  <!-- ràng buộc một object các thuộc tính -->
  <div v-bind="{ id: someProp, 'other-attr': otherProp }"></div>

  <!-- ràng buộc prop. "prop" phải được khai báo trong component con. -->
  <MyComponent :prop="someThing" />

  <!-- truyền xuống các prop của cha chung với component con -->
  <MyComponent v-bind="$props" />

  <!-- XLink -->
  <svg><a :xlink:special="foo"></a></svg>
  ```

  Modifier `.prop` cũng có cú pháp rút gọn riêng là `.`:

  ```vue-html
  <div :someProperty.prop="someObject"></div>

  <!-- tương đương với -->
  <div .someProperty="someObject"></div>
  ```

  Modifier `.camel` cho phép camelCase hóa tên thuộc tính `v-bind` khi dùng trong template in-DOM, ví dụ thuộc tính `viewBox` của SVG:

  ```vue-html
  <svg :view-box.camel="viewBox"></svg>
  ```

  `.camel` không cần thiết nếu bạn dùng string template, hoặc pre-compile template với bước build.

- **Xem thêm**
  - [Ràng buộc Class và Style](/guide/essentials/class-and-style)
  - [Component - Chi tiết Truyền Prop](/guide/components/props#prop-passing-details)

## v-model {#v-model}

Tạo ràng buộc hai chiều trên form input element hoặc component.

- **Nhận:** thay đổi tùy theo giá trị của form input element hoặc output của component

- **Giới hạn với:**

  - `<input>`
  - `<select>`
  - `<textarea>`
  - component

- **Modifier**

  - [`.lazy`](/guide/essentials/forms#lazy) - lắng nghe sự kiện `change` thay vì `input`
  - [`.number`](/guide/essentials/forms#number) - chuyển chuỗi input hợp lệ thành số
  - [`.trim`](/guide/essentials/forms#trim) - trim input

- **Xem thêm**

  - [Ràng buộc Input Form](/guide/essentials/forms)
  - [Sự kiện Component - Dùng với `v-model`](/guide/components/v-model)

## v-slot {#v-slot}

Biểu thị slot có tên hoặc scoped slot dự kiến nhận props.

- **Cú pháp rút gọn:** `#`

- **Nhận:** biểu thức JavaScript hợp lệ ở vị trí đối số hàm, bao gồm hỗ trợ destructuring. Tùy chọn - chỉ cần thiết nếu dự kiến props được truyền vào slot.

- **Đối số:** tên slot (tùy chọn, mặc định là `default`)

- **Giới hạn với:**

  - `<template>`
  - [component](/guide/components/slots#scoped-slots) (cho một default slot đơn có props)

- **Ví dụ**

  ```vue-html
  <!-- Slot có tên -->
  <BaseLayout>
    <template v-slot:header>
      Nội dung Header
    </template>

    <template v-slot:default>
      Nội dung slot mặc định
    </template>

    <template v-slot:footer>
      Nội dung Footer
    </template>
  </BaseLayout>

  <!-- Slot có tên nhận props -->
  <InfiniteScroll>
    <template v-slot:item="slotProps">
      <div class="item">
        {{ slotProps.item.text }}
      </div>
    </template>
  </InfiniteScroll>

  <!-- Default slot nhận props, với destructuring -->
  <Mouse v-slot="{ x, y }">
    Vị trí chuột: {{ x }}, {{ y }}
  </Mouse>
  ```

- **Xem thêm**
  - [Component - Slot](/guide/components/slots)

## v-pre {#v-pre}

Bỏ qua biên dịch cho element này và tất cả các con của nó.

- **Không nhận biểu thức**

- **Chi tiết**

  Bên trong element có `v-pre`, tất cả cú pháp template Vue sẽ được giữ nguyên và render như vậy. Trường hợp sử dụng phổ biến nhất là hiển thị thẻ Mustache thô.

- **Ví dụ**

  ```vue-html
  <span v-pre>{{ phần này sẽ không được biên dịch }}</span>
  ```

## v-once {#v-once}

Render element và component chỉ một lần, và bỏ qua các cập nhật sau này.

- **Không nhận biểu thức**

- **Chi tiết**

  Ở các lần render lại tiếp theo, element/component và tất cả các con của nó sẽ được coi là nội dung tĩnh và bị bỏ qua. Điều này có thể dùng để tối ưu hiệu năng cập nhật.

  ```vue-html
  <!-- element đơn -->
  <span v-once>Điều này sẽ không bao giờ thay đổi: {{msg}}</span>
  <!-- element có con -->
  <div v-once>
    <h1>Comment</h1>
    <p>{{msg}}</p>
  </div>
  <!-- component -->
  <MyComponent v-once :comment="msg"></MyComponent>
  <!-- directive `v-for` -->
  <ul>
    <li v-for="i in list" v-once>{{i}}</li>
  </ul>
  ```

  Từ 3.2, bạn cũng có thể ghi nhớ một phần template với điều kiện hủy bộ nhớ cache bằng [`v-memo`](#v-memo).

- **Xem thêm**
  - [Cú pháp Data Binding - nội suy](/guide/essentials/template-syntax#text-interpolation)
  - [v-memo](#v-memo)

## v-memo {#v-memo}

- Chỉ hỗ trợ từ 3.2+

- **Nhận:** `any[]`

- **Chi tiết**

  Ghi nhớ một sub-tree của template. Có thể dùng trên cả element và component. Directive yêu cầu một mảng giá trị dependency có độ dài cố định để so sánh cho việc ghi nhớ. Nếu mọi giá trị trong mảng giống như lần render trước, tất cả cập nhật cho toàn bộ sub-tree sẽ bị bỏ qua. Ví dụ:

  ```vue-html
  <div v-memo="[valueA, valueB]">
    ...
  </div>
  ```

  Khi component re-render, nếu cả `valueA` và `valueB` vẫn giống nhau, tất cả cập nhật cho `<div>` này và các con của nó sẽ bị bỏ qua. Thực tế, ngay cả việc tạo VNode Virtual DOM cũng sẽ bị bỏ qua vì bản sao đã được ghi nhớ của sub-tree có thể được tái sử dụng.

  Điều quan trọng là phải chỉ định mảng ghi nhớ đúng, nếu không chúng ta có thể bỏ qua các cập nhật thực sự cần được áp dụng. `v-memo` với mảng dependency rỗng (`v-memo="[]"`) sẽ tương đương về mặt chức năng với `v-once`.

  **Dùng với `v-for`**

  `v-memo` được cung cấp chỉ cho các tối ưu hóa vi mô trong các tình huống quan trọng về hiệu năng và hiếm khi cần thiết. Trường hợp phổ biến nhất có ích là khi render các danh sách `v-for` lớn (nơi `length > 1000`):

  ```vue-html
  <div v-for="item in list" :key="item.id" v-memo="[item.id === selected]">
    <p>ID: {{ item.id }} - selected: {{ item.id === selected }}</p>
    <p>...thêm node con</p>
  </div>
  ```

  Khi trạng thái `selected` của component thay đổi, một lượng lớn VNode sẽ được tạo ra ngay cả khi hầu hết các mục vẫn hoàn toàn giống nhau. Cách dùng `v-memo` ở đây về cơ bản là nói "chỉ cập nhật mục này nếu nó chuyển từ không được chọn sang được chọn, hoặc ngược lại". Điều này cho phép mọi mục không bị ảnh hưởng tái sử dụng VNode trước đó và bỏ qua hoàn toàn việc so sánh. Lưu ý chúng ta không cần đưa `item.id` vào mảng dependency ghi nhớ ở đây vì Vue tự suy luận nó từ `:key` của mục.

  :::warning
  Khi dùng `v-memo` với `v-for`, hãy đảm bảo chúng được dùng trên cùng một element. **`v-memo` không hoạt động bên trong `v-for`.**
  :::

  `v-memo` cũng có thể được dùng trên component để ngăn thủ công các cập nhật không mong muốn trong một số trường hợp đặc biệt khi việc kiểm tra cập nhật của component con đã bị giảm tối ưu. Nhưng một lần nữa, trách nhiệm của developer là phải chỉ định mảng dependency đúng để tránh bỏ qua các cập nhật cần thiết.

- **Xem thêm**
  - [v-once](#v-once)

## v-cloak {#v-cloak}

Dùng để ẩn template chưa được biên dịch cho đến khi sẵn sàng.

- **Không nhận biểu thức**

- **Chi tiết**

  **Directive này chỉ cần thiết trong các cài đặt không có bước build.**

  Khi dùng template in-DOM, có thể xảy ra hiện tượng "flash template chưa biên dịch": người dùng có thể thấy thẻ Mustache thô cho đến khi component được mount thay thế chúng bằng nội dung đã render.

  `v-cloak` sẽ tồn tại trên element cho đến khi instance component liên quan được mount. Kết hợp với các quy tắc CSS như `[v-cloak] { display: none }`, nó có thể dùng để ẩn các template thô cho đến khi component sẵn sàng.

- **Ví dụ**

  ```css
  [v-cloak] {
    display: none;
  }
  ```

  ```vue-html
  <div v-cloak>
    {{ message }}
  </div>
  ```

  `<div>` sẽ không hiển thị cho đến khi quá trình biên dịch hoàn tất.
