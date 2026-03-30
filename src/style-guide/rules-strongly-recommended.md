# Quy tắc Ưu tiên B: Rất Khuyến nghị {#priority-b-rules-strongly-recommended}

::: warning Lưu ý
Hướng dẫn phong cách Vue.js này đã lỗi thời và cần được xem xét lại. Nếu bạn có câu hỏi hoặc gợi ý, vui lòng [mở issue](https://github.com/vuejs/docs/issues/new).
:::

Các quy tắc này được phát hiện là cải thiện khả năng đọc và/hoặc trải nghiệm phát triển trong hầu hết các dự án. Code của bạn vẫn chạy nếu vi phạm chúng, nhưng vi phạm phải hiếm và có lý do chính đáng.

## File component {#component-files}

**Bất cứ khi nào có build system để nối các file, mỗi component nên ở trong file riêng của nó.**

Điều này giúp bạn tìm component nhanh hơn khi cần chỉnh sửa hoặc xem cách dùng.

<div class="style-example style-example-bad">
<h3>Không nên</h3>

```js
app.component('TodoList', {
  // ...
})

app.component('TodoItem', {
  // ...
})
```

</div>

<div class="style-example style-example-good">
<h3>Nên</h3>

```
components/
|- TodoList.js
|- TodoItem.js
```

```
components/
|- TodoList.vue
|- TodoItem.vue
```

</div>

## Kiểu đặt tên file Single-File Component {#single-file-component-filename-casing}

**Tên file của [Single-File Component](/guide/scaling-up/sfc) nên luôn PascalCase hoặc luôn kebab-case.**

PascalCase hoạt động tốt nhất với tính năng autocomplete trong code editor, vì nó nhất quán với cách chúng ta tham chiếu component trong JS(X) và template. Tuy nhiên, tên file chữ hoa chữ thường hỗn hợp đôi khi gây vấn đề trên hệ thống file không phân biệt chữ hoa chữ thường, do đó kebab-case cũng hoàn toàn chấp nhận được.

<div class="style-example style-example-bad">
<h3>Không nên</h3>

```
components/
|- mycomponent.vue
```

```
components/
|- myComponent.vue
```

</div>

<div class="style-example style-example-good">
<h3>Nên</h3>

```
components/
|- MyComponent.vue
```

```
components/
|- my-component.vue
```

</div>

## Tên component cơ sở {#base-component-names}

**Các component cơ sở (còn gọi là presentational, dumb, hoặc pure component) áp dụng styling và quy ước đặc thù cho app nên tất cả bắt đầu bằng tiền tố cụ thể, như `Base`, `App`, hoặc `V`.**

::: details Giải thích chi tiết
Các component này đặt nền tảng cho styling và hành vi nhất quán trong ứng dụng. Chúng **chỉ** có thể chứa:

- HTML element,
- các component cơ sở khác, và
- component UI bên thứ ba.

Nhưng chúng **không bao giờ** chứa global state (ví dụ: từ store [Pinia](https://pinia.vuejs.org/)).

Tên của chúng thường bao gồm tên của element mà chúng bọc (ví dụ: `BaseButton`, `BaseTable`), trừ khi không có element nào cho mục đích cụ thể của chúng (ví dụ: `BaseIcon`). Nếu bạn xây dựng các component tương tự cho ngữ cảnh cụ thể hơn, chúng hầu như luôn sử dụng các component này (ví dụ: `BaseButton` có thể dùng trong `ButtonSubmit`).

Một số lợi ích của quy ước này:

- Khi được sắp xếp theo alphabet trong editor, các component cơ sở của app được liệt kê cùng nhau, giúp dễ nhận biết hơn.

- Vì tên component nên luôn nhiều từ, quy ước này ngăn bạn phải chọn tiền tố tùy ý cho các component wrapper đơn giản (ví dụ: `MyButton`, `VueButton`).

- Vì các component này được dùng thường xuyên, bạn có thể muốn đơn giản là đăng ký chúng global thay vì import ở mọi nơi. Tiền tố giúp điều này khả thi với Webpack:

  ```js
  const requireComponent = require.context(
    './src',
    true,
    /Base[A-Z]\w+\.(vue|js)$/
  )
  requireComponent.keys().forEach(function (fileName) {
    let baseComponentConfig = requireComponent(fileName)
    baseComponentConfig =
      baseComponentConfig.default || baseComponentConfig
    const baseComponentName =
      baseComponentConfig.name ||
      fileName.replace(/^.+\//, '').replace(/\.\w+$/, '')
    app.component(baseComponentName, baseComponentConfig)
  })
  ```

  :::

<div class="style-example style-example-bad">
<h3>Không nên</h3>

```
components/
|- MyButton.vue
|- VueTable.vue
|- Icon.vue
```

</div>

<div class="style-example style-example-good">
<h3>Nên</h3>

```
components/
|- BaseButton.vue
|- BaseTable.vue
|- BaseIcon.vue
```

```
components/
|- AppButton.vue
|- AppTable.vue
|- AppIcon.vue
```

```
components/
|- VButton.vue
|- VTable.vue
|- VIcon.vue
```

</div>

## Tên component gắn kết chặt chẽ {#tightly-coupled-component-names}

**Các child component gắn kết chặt chẽ với parent nên bao gồm tên parent component làm tiền tố.**

Nếu một component chỉ có nghĩa trong ngữ cảnh của một parent component, mối quan hệ đó nên rõ ràng qua tên. Vì editor thường sắp xếp file theo alphabet, điều này cũng giúp các file liên quan nằm cạnh nhau.

::: details Giải thích chi tiết
Bạn có thể bị cám dỗ giải quyết vấn đề này bằng cách đặt child component trong thư mục con đặt tên theo parent. Ví dụ:

```
components/
|- TodoList/
   |- Item/
      |- index.vue
      |- Button.vue
   |- index.vue
```

hoặc:

```
components/
|- TodoList/
   |- Item/
      |- Button.vue
   |- Item.vue
|- TodoList.vue
```

Điều này không được khuyến nghị vì dẫn đến:

- Nhiều file có tên tương tự, khiến việc chuyển đổi file nhanh trong code editor khó hơn.
- Nhiều thư mục con lồng nhau, làm tăng thời gian duyệt component trong sidebar của editor.
  :::

<div class="style-example style-example-bad">
<h3>Không nên</h3>

```
components/
|- TodoList.vue
|- TodoItem.vue
|- TodoButton.vue
```

```
components/
|- SearchSidebar.vue
|- NavigationForSearchSidebar.vue
```

</div>

<div class="style-example style-example-good">
<h3>Nên</h3>

```
components/
|- TodoList.vue
|- TodoListItem.vue
|- TodoListItemButton.vue
```

```
components/
|- SearchSidebar.vue
|- SearchSidebarNavigation.vue
```

</div>

## Thứ tự từ trong tên component {#order-of-words-in-component-names}

**Tên component nên bắt đầu bằng từ cấp cao nhất (thường là từ chung nhất) và kết thúc bằng các từ mô tả.**

::: details Giải thích chi tiết
Bạn có thể thắc mắc:

> "Tại sao chúng ta lại buộc tên component dùng ngôn ngữ ít tự nhiên hơn?"

Trong tiếng Anh tự nhiên, tính từ và các mô tả khác thường xuất hiện trước danh từ, trong khi các ngoại lệ yêu cầu từ kết nối. Ví dụ:

- Coffee _with_ milk
- Soup _of the_ day
- Visitor _to the_ museum

Bạn hoàn toàn có thể bao gồm các từ kết nối này trong tên component nếu muốn, nhưng thứ tự vẫn quan trọng.

Cũng lưu ý rằng **"cấp cao nhất" sẽ mang tính ngữ cảnh theo app của bạn**. Ví dụ: hãy tưởng tượng một app có form tìm kiếm. Nó có thể bao gồm các component như:

```
components/
|- ClearSearchButton.vue
|- ExcludeFromSearchInput.vue
|- LaunchOnStartupCheckbox.vue
|- RunSearchButton.vue
|- SearchInput.vue
|- TermsCheckbox.vue
```

Như bạn có thể thấy, khá khó để nhận ra component nào đặc thù cho tìm kiếm. Bây giờ hãy đổi tên các component theo quy tắc:

```
components/
|- SearchButtonClear.vue
|- SearchButtonRun.vue
|- SearchInputExcludeGlob.vue
|- SearchInputQuery.vue
|- SettingsCheckboxLaunchOnStartup.vue
|- SettingsCheckboxTerms.vue
```

Vì editor thường sắp xếp file theo alphabet, tất cả mối quan hệ quan trọng giữa các component giờ đây đều hiển nhiên ngay khi nhìn vào.

Bạn có thể bị cám dỗ giải quyết vấn đề này khác đi, đặt tất cả component tìm kiếm trong thư mục "search", rồi tất cả component settings trong thư mục "settings". Chúng tôi chỉ khuyến nghị xem xét cách tiếp cận này trong các app rất lớn (ví dụ: 100+ component) vì các lý do sau:

- Thường mất nhiều thời gian hơn để điều hướng qua các thư mục con lồng nhau so với cuộn qua thư mục `components` duy nhất.
- Xung đột tên (ví dụ: nhiều component `ButtonDelete.vue`) làm khó điều hướng nhanh đến component cụ thể trong code editor.
- Refactoring trở nên khó hơn vì tìm và thay thế thường không đủ để cập nhật các tham chiếu tương đối đến component đã di chuyển.
  :::

<div class="style-example style-example-bad">
<h3>Không nên</h3>

```
components/
|- ClearSearchButton.vue
|- ExcludeFromSearchInput.vue
|- LaunchOnStartupCheckbox.vue
|- RunSearchButton.vue
|- SearchInput.vue
|- TermsCheckbox.vue
```

</div>

<div class="style-example style-example-good">
<h3>Nên</h3>

```
components/
|- SearchButtonClear.vue
|- SearchButtonRun.vue
|- SearchInputQuery.vue
|- SearchInputExcludeGlob.vue
|- SettingsCheckboxTerms.vue
|- SettingsCheckboxLaunchOnStartup.vue
```

</div>

## Component tự đóng {#self-closing-components}

**Component không có nội dung nên tự đóng trong [Single-File Component](/guide/scaling-up/sfc), string template, và [JSX](/guide/extras/render-function#jsx-tsx) - nhưng không bao giờ trong template trong DOM.**

Các component tự đóng cho thấy chúng không chỉ không có nội dung, mà còn **được thiết kế** để không có nội dung. Đó là sự khác biệt giữa một trang trắng trong sách và một trang có ghi "Trang này cố ý để trống." Code của bạn cũng gọn hơn mà không có tag đóng không cần thiết.

Thật không may, HTML không cho phép custom element tự đóng - chỉ [các element "void" chính thức](https://www.w3.org/TR/html/syntax.html#void-elements). Đó là lý do tại sao chiến lược này chỉ khả thi khi Vue's template compiler có thể xử lý template trước DOM, sau đó cung cấp HTML tuân thủ spec DOM.

<div class="style-example style-example-bad">
<h3>Không nên</h3>

```vue-html
<!-- Trong Single-File Component, string template, và JSX -->
<MyComponent></MyComponent>
```

```vue-html
<!-- Trong template trong DOM -->
<my-component/>
```

</div>

<div class="style-example style-example-good">
<h3>Nên</h3>

```vue-html
<!-- Trong Single-File Component, string template, và JSX -->
<MyComponent/>
```

```vue-html
<!-- Trong template trong DOM -->
<my-component></my-component>
```

</div>

## Kiểu đặt tên component trong template {#component-name-casing-in-templates}

**Trong hầu hết các dự án, tên component nên luôn PascalCase trong [Single-File Component](/guide/scaling-up/sfc) và string template - nhưng kebab-case trong template trong DOM.**

PascalCase có một số lợi thế so với kebab-case:

- Editor có thể autocomplete tên component trong template, vì PascalCase cũng được dùng trong JavaScript.
- `<MyComponent>` dễ phân biệt hơn về mặt thị giác so với element HTML một từ so với `<my-component>`, vì có hai ký tự khác nhau (hai chữ hoa), thay vì chỉ một (dấu gạch ngang).
- Nếu bạn dùng bất kỳ custom element không phải Vue nào trong template, như web component, PascalCase đảm bảo các Vue component của bạn vẫn nổi bật rõ ràng.

Thật không may, do HTML không phân biệt chữ hoa chữ thường, template trong DOM vẫn phải dùng kebab-case.

Cũng lưu ý rằng nếu bạn đã đầu tư nhiều vào kebab-case, sự nhất quán với quy ước HTML và khả năng dùng cùng kiểu viết trong tất cả dự án có thể quan trọng hơn các lợi ích đã liệt kê. Trong những trường hợp đó, **dùng kebab-case ở mọi nơi cũng chấp nhận được.**

<div class="style-example style-example-bad">
<h3>Không nên</h3>

```vue-html
<!-- Trong Single-File Component và string template -->
<mycomponent/>
```

```vue-html
<!-- Trong Single-File Component và string template -->
<myComponent/>
```

```vue-html
<!-- Trong template trong DOM -->
<MyComponent></MyComponent>
```

</div>

<div class="style-example style-example-good">
<h3>Nên</h3>

```vue-html
<!-- Trong Single-File Component và string template -->
<MyComponent/>
```

```vue-html
<!-- Trong template trong DOM -->
<my-component></my-component>
```

HOẶC

```vue-html
<!-- Ở mọi nơi -->
<my-component></my-component>
```

</div>

## Kiểu đặt tên component trong JS/JSX {#component-name-casing-in-js-jsx}

**Tên component trong JS/[JSX](/guide/extras/render-function#jsx-tsx) nên luôn PascalCase, dù có thể kebab-case trong chuỗi cho các ứng dụng đơn giản chỉ dùng đăng ký component global qua `app.component`.**

::: details Giải thích chi tiết
Trong JavaScript, PascalCase là quy ước cho class và prototype constructor - về cơ bản là bất cứ thứ gì có thể có các instance riêng biệt. Các Vue component cũng có instance, vì vậy có lý khi cũng dùng PascalCase. Thêm vào đó, dùng PascalCase trong JSX (và template) cho phép người đọc code dễ dàng phân biệt giữa component và HTML element.

Tuy nhiên, đối với các ứng dụng **chỉ** dùng định nghĩa component global qua `app.component`, chúng tôi khuyến nghị kebab-case thay thế. Lý do:

- Hiếm khi component global được tham chiếu trong JavaScript, vì vậy tuân theo quy ước JavaScript ít có ý nghĩa hơn.
- Các ứng dụng này luôn bao gồm nhiều template trong DOM, nơi [kebab-case **phải** được dùng](#component-name-casing-in-templates).
  :::

<div class="style-example style-example-bad">
<h3>Không nên</h3>

```js
app.component('myComponent', {
  // ...
})
```

```js
import myComponent from './MyComponent.vue'
```

```js
export default {
  name: 'myComponent'
  // ...
}
```

```js
export default {
  name: 'my-component'
  // ...
}
```

</div>

<div class="style-example style-example-good">
<h3>Nên</h3>

```js
app.component('MyComponent', {
  // ...
})
```

```js
app.component('my-component', {
  // ...
})
```

```js
import MyComponent from './MyComponent.vue'
```

```js
export default {
  name: 'MyComponent'
  // ...
}
```

</div>

## Tên component viết đầy đủ {#full-word-component-names}

**Tên component nên ưu tiên từ đầy đủ hơn là viết tắt.**

Tính năng autocomplete trong editor khiến chi phí viết tên dài rất thấp, trong khi độ rõ ràng chúng mang lại vô giá. Đặc biệt các viết tắt không phổ biến nên luôn tránh.

<div class="style-example style-example-bad">
<h3>Không nên</h3>

```
components/
|- SdSettings.vue
|- UProfOpts.vue
```

</div>

<div class="style-example style-example-good">
<h3>Nên</h3>

```
components/
|- StudentDashboardSettings.vue
|- UserProfileOptions.vue
```

</div>

## Kiểu đặt tên prop {#prop-name-casing}

**Tên prop nên luôn dùng camelCase khi khai báo. Khi dùng trong template trong DOM, prop nên là kebab-case. Template Single-File Component và [JSX](/guide/extras/render-function#jsx-tsx) có thể dùng cả kebab-case hoặc camelCase. Kiểu viết phải nhất quán - nếu bạn chọn camelCase, đảm bảo không dùng kebab-case trong ứng dụng của mình.**

<div class="style-example style-example-bad">
<h3>Không nên</h3>

<div class="options-api">

```js
props: {
  'greeting-text': String
}
```

</div>

<div class="composition-api">

```js
const props = defineProps({
  'greeting-text': String
})
```

</div>

```vue-html
// dành cho template trong DOM
<welcome-message greetingText="hi"></welcome-message>
```

</div>

<div class="style-example style-example-good">
<h3>Nên</h3>

<div class="options-api">

```js
props: {
  greetingText: String
}
```

</div>

<div class="composition-api">

```js
const props = defineProps({
  greetingText: String
})
```

</div>

```vue-html
// dành cho SFC - hãy đảm bảo kiểu viết nhất quán trong toàn dự án
// bạn có thể dùng cả hai quy ước nhưng không nên trộn lẫn
<WelcomeMessage greeting-text="hi"/>
// hoặc
<WelcomeMessage greetingText="hi"/>
```

```vue-html
// dành cho template trong DOM
<welcome-message greeting-text="hi"></welcome-message>
```

</div>

## Element nhiều thuộc tính {#multi-attribute-elements}

**Element có nhiều thuộc tính nên trải rộng trên nhiều dòng, mỗi dòng một thuộc tính.**

Trong JavaScript, việc chia object có nhiều property thành nhiều dòng được xem là quy ước tốt vì dễ đọc hơn nhiều. Template và [JSX](/guide/extras/render-function#jsx-tsx) của chúng ta xứng đáng được đối xử tương tự.

<div class="style-example style-example-bad">
<h3>Không nên</h3>

```vue-html
<img src="https://vuejs.org/images/logo.png" alt="Vue Logo">
```

```vue-html
<MyComponent foo="a" bar="b" baz="c"/>
```

</div>

<div class="style-example style-example-good">
<h3>Nên</h3>

```vue-html
<img
  src="https://vuejs.org/images/logo.png"
  alt="Vue Logo"
>
```

```vue-html
<MyComponent
  foo="a"
  bar="b"
  baz="c"
/>
```

</div>

## Biểu thức đơn giản trong template {#simple-expressions-in-templates}

**Template của component nên chỉ bao gồm các biểu thức đơn giản, với các biểu thức phức tạp hơn được tách ra thành computed property hoặc method.**

Các biểu thức phức tạp trong template làm chúng kém khai báo hơn. Chúng ta nên cố gắng mô tả _cái gì_ sẽ xuất hiện, không phải _cách_ chúng ta tính toán giá trị đó. Computed property và method cũng cho phép code được tái sử dụng.

<div class="style-example style-example-bad">
<h3>Không nên</h3>

```vue-html
{{
  fullName.split(' ').map((word) => {
    return word[0].toUpperCase() + word.slice(1)
  }).join(' ')
}}
```

</div>

<div class="style-example style-example-good">
<h3>Nên</h3>

```vue-html
<!-- Trong template -->
{{ normalizedFullName }}
```

<div class="options-api">

```js
// Biểu thức phức tạp được chuyển vào computed property
computed: {
  normalizedFullName() {
    return this.fullName.split(' ')
      .map(word => word[0].toUpperCase() + word.slice(1))
      .join(' ')
  }
}
```

</div>

<div class="composition-api">

```js
// Biểu thức phức tạp được chuyển vào computed property
const normalizedFullName = computed(() =>
  fullName.value
    .split(' ')
    .map((word) => word[0].toUpperCase() + word.slice(1))
    .join(' ')
)
```

</div>

</div>

## Computed property đơn giản {#simple-computed-properties}

**Computed property phức tạp nên được chia thành nhiều property đơn giản hơn càng nhiều càng tốt.**

::: details Giải thích chi tiết
Các computed property đơn giản và có tên rõ ràng:

- **Dễ test hơn**

  Khi mỗi computed property chỉ chứa một biểu thức rất đơn giản với rất ít dependency, việc viết test xác nhận nó hoạt động đúng dễ hơn nhiều.

- **Dễ đọc hơn**

  Đơn giản hóa computed property buộc bạn đặt tên mô tả cho mỗi giá trị, ngay cả khi nó không được tái sử dụng. Điều này giúp developer khác (và bạn trong tương lai) dễ tập trung vào code họ quan tâm và hiểu điều gì đang xảy ra hơn nhiều.

- **Thích nghi tốt hơn với yêu cầu thay đổi**

  Bất kỳ giá trị nào có thể đặt tên đều có thể hữu ích cho view. Ví dụ: chúng ta có thể quyết định hiển thị thông báo cho người dùng biết họ đã tiết kiệm bao nhiêu tiền. Chúng ta cũng có thể quyết định tính thuế bán hàng, nhưng có thể hiển thị riêng thay vì như một phần của giá cuối.

  Các computed property nhỏ, tập trung đưa ra ít giả định hơn về cách thông tin sẽ được dùng, do đó yêu cầu ít refactoring hơn khi yêu cầu thay đổi.
  :::

<div class="style-example style-example-bad">
<h3>Không nên</h3>

<div class="options-api">

```js
computed: {
  price() {
    const basePrice = this.manufactureCost / (1 - this.profitMargin)
    return (
      basePrice -
      basePrice * (this.discountPercent || 0)
    )
  }
}
```

</div>

<div class="composition-api">

```js
const price = computed(() => {
  const basePrice = manufactureCost.value / (1 - profitMargin.value)
  return basePrice - basePrice * (discountPercent.value || 0)
})
```

</div>

</div>

<div class="style-example style-example-good">
<h3>Nên</h3>

<div class="options-api">

```js
computed: {
  basePrice() {
    return this.manufactureCost / (1 - this.profitMargin)
  },

  discount() {
    return this.basePrice * (this.discountPercent || 0)
  },

  finalPrice() {
    return this.basePrice - this.discount
  }
}
```

</div>

<div class="composition-api">

```js
const basePrice = computed(
  () => manufactureCost.value / (1 - profitMargin.value)
)

const discount = computed(
  () => basePrice.value * (discountPercent.value || 0)
)

const finalPrice = computed(() => basePrice.value - discount.value)
```

</div>

</div>

## Giá trị thuộc tính trong dấu nháy {#quoted-attribute-values}

**Giá trị thuộc tính HTML không rỗng nên luôn ở trong dấu nháy (đơn hoặc đôi, cái nào không dùng trong JS).**

Trong khi giá trị thuộc tính không có khoảng trắng không bắt buộc có dấu nháy trong HTML, thực hành này thường dẫn đến _tránh_ khoảng trắng, làm giá trị thuộc tính kém đọc hơn.

<div class="style-example style-example-bad">
<h3>Không nên</h3>

```vue-html
<input type=text>
```

```vue-html
<AppSidebar :style={width:sidebarWidth+'px'}>
```

</div>

<div class="style-example style-example-good">
<h3>Nên</h3>

```vue-html
<input type="text">
```

```vue-html
<AppSidebar :style="{ width: sidebarWidth + 'px' }">
```

</div>

## Cú pháp rút gọn của directive {#directive-shorthands}

**Cú pháp rút gọn của directive (`:` cho `v-bind:`, `@` cho `v-on:` và `#` cho `v-slot`) nên được dùng luôn luôn hoặc không bao giờ.**

<div class="style-example style-example-bad">
<h3>Không nên</h3>

```vue-html
<input
  v-bind:value="newTodoText"
  :placeholder="newTodoInstructions"
>
```

```vue-html
<input
  v-on:input="onInput"
  @focus="onFocus"
>
```

```vue-html
<template v-slot:header>
  <h1>Here might be a page title</h1>
</template>

<template #footer>
  <p>Here's some contact info</p>
</template>
```

</div>

<div class="style-example style-example-good">
<h3>Nên</h3>

```vue-html
<input
  :value="newTodoText"
  :placeholder="newTodoInstructions"
>
```

```vue-html
<input
  v-bind:value="newTodoText"
  v-bind:placeholder="newTodoInstructions"
>
```

```vue-html
<input
  @input="onInput"
  @focus="onFocus"
>
```

```vue-html
<input
  v-on:input="onInput"
  v-on:focus="onFocus"
>
```

```vue-html
<template v-slot:header>
  <h1>Here might be a page title</h1>
</template>

<template v-slot:footer>
  <p>Here's some contact info</p>
</template>
```

```vue-html
<template #header>
  <h1>Here might be a page title</h1>
</template>

<template #footer>
  <p>Here's some contact info</p>
</template>
```

</div>
