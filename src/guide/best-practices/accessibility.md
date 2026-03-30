# Accessibility {#accessibility}

Khả năng tiếp cận trên web, thường được gọi là a11y, là cách xây dựng website sao cho ai cũng có thể dùng được. Đó có thể là người khuyết tật, người có đường truyền chậm, dùng phần cứng cũ hay hỏng, hoặc đơn giản là đang ở trong một môi trường không thuận lợi. Ví dụ, việc thêm phụ đề cho video sẽ giúp cả người khiếm thính lẫn người đang ở nơi ồn ào không nghe rõ điện thoại. Tương tự, việc bảo đảm văn bản có độ tương phản đủ cao sẽ giúp cả người có thị lực kém lẫn người đang dùng điện thoại dưới nắng gắt.

Bạn muốn bắt đầu nhưng chưa rõ nên đi từ đâu?

Hãy xem hướng dẫn [Planning and managing web accessibility guide](https://www.w3.org/WAI/planning-and-managing/) do [World Wide Web Consortium (W3C)](https://www.w3.org/) cung cấp.

## Liên kết bỏ qua nội dung lặp {#skip-link}

Bạn nên thêm một liên kết ở đầu mỗi trang để đưa người dùng thẳng tới vùng nội dung chính, giúp họ bỏ qua những phần lặp lại trên nhiều trang.

Thông thường việc này được đặt ở đầu `App.vue`, vì đó sẽ là phần tử đầu tiên có thể nhận focus trên mọi trang:

```vue-html
<span ref="backToTop" tabindex="-1" />
<ul class="skip-links">
  <li>
    <a href="#main" ref="skipLink" class="skip-link">Bỏ qua để tới nội dung chính</a>
  </li>
</ul>
```

Để ẩn liên kết này đi cho tới khi nó được focus, bạn có thể thêm style sau:

```css
.skip-links {
  list-style: none;
}
.skip-link {
  white-space: nowrap;
  margin: 1em auto;
  top: 0;
  position: fixed;
  left: 50%;
  margin-left: -72px;
  opacity: 0;
}
.skip-link:focus {
  opacity: 1;
  background-color: white;
  padding: 0.5em;
  border: 1px solid black;
}
```

Sau khi người dùng chuyển route, hãy đưa focus trở lại ngay đầu trang, ngay trước liên kết bỏ qua. Có thể làm điều này bằng cách gọi `focus` trên template ref `backToTop` nếu bạn đang dùng `vue-router`:

<div class="options-api">

```vue
<script>
export default {
  watch: {
    $route() {
      this.$refs.backToTop.focus()
    }
  }
}
</script>
```

</div>
<div class="composition-api">

```vue
<script setup>
import { ref, watch } from 'vue'
import { useRoute } from 'vue-router'

const route = useRoute()
const backToTop = ref()

watch(
  () => route.path,
  () => {
    backToTop.value.focus()
  }
)
</script>
```

</div>

[Đọc tài liệu về skip link tới nội dung chính](https://www.w3.org/WAI/WCAG21/Techniques/general/G1.html)

## Cấu trúc nội dung {#content-structure}

Một trong những phần quan trọng nhất của accessibility là bảo đảm thiết kế có thể hỗ trợ việc triển khai dễ tiếp cận. Thiết kế không chỉ cần quan tâm tới độ tương phản màu, lựa chọn font, cỡ chữ và ngôn ngữ, mà còn phải tính tới cách nội dung được tổ chức trong ứng dụng.

### Heading {#headings}

Người dùng có thể điều hướng ứng dụng thông qua heading. Việc dùng heading rõ nghĩa cho từng phần trong ứng dụng giúp họ đoán trước nội dung của từng khu vực. Với heading, có một số thực hành accessibility được khuyến nghị:

- Lồng heading theo đúng thứ bậc: `<h1>` - `<h6>`
- Không bỏ qua cấp heading trong cùng một phần
- Dùng đúng thẻ heading thay vì chỉ style văn bản cho giống heading

[Đọc thêm về heading](https://www.w3.org/TR/UNDERSTANDING-WCAG20/navigation-mechanisms-descriptive.html)

```vue-html
<main role="main" aria-labelledby="main-title">
  <h1 id="main-title">Tiêu đề chính</h1>
  <section aria-labelledby="section-title-1">
    <h2 id="section-title-1">Tiêu đề phần</h2>
    <h3>Tiêu đề phụ của phần</h3>
    <!-- Nội dung -->
  </section>
  <section aria-labelledby="section-title-2">
    <h2 id="section-title-2">Tiêu đề phần</h2>
    <h3>Tiêu đề phụ của phần</h3>
    <!-- Nội dung -->
    <h3>Tiêu đề phụ của phần</h3>
    <!-- Nội dung -->
  </section>
</main>
```

### Landmark {#landmarks}

[Landmark](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Roles/landmark_role) cho phép truy cập theo cách có thể nhận biết được bằng công cụ vào từng vùng trong ứng dụng. Người dùng phụ thuộc vào công nghệ hỗ trợ có thể nhảy tới từng phần của ứng dụng và bỏ qua những nội dung không cần thiết. Bạn có thể dùng [ARIA role](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Roles) để làm điều này.

| HTML | ARIA Role | Mục đích của landmark |
| --- | --- | --- |
| header | role="banner" | Phần đầu chính: tiêu đề của trang |
| nav | role="navigation" | Tập hợp các liên kết phù hợp cho việc điều hướng trong tài liệu hoặc các tài liệu liên quan |
| main | role="main" | Nội dung chính hoặc trung tâm của tài liệu |
| footer | role="contentinfo" | Thông tin về tài liệu cha: chú thích, bản quyền, liên kết tới chính sách riêng tư |
| aside | role="complementary" | Hỗ trợ nội dung chính nhưng vẫn tách biệt và có ý nghĩa riêng |
| search | role="search" | Phần chứa chức năng tìm kiếm của ứng dụng |
| form | role="form" | Tập hợp các phần tử liên quan tới form |
| section | role="region" | Nội dung quan trọng mà người dùng có thể muốn điều hướng tới. Phần tử này phải có nhãn |

[Đọc thêm về landmark](https://www.w3.org/TR/wai-aria-1.2/#landmark_roles)

## Form ngữ nghĩa {#semantic-forms}

Khi tạo form, bạn có thể dùng những phần tử sau: `<form>`, `<label>`, `<input>`, `<textarea>` và `<button>`.

Label thường được đặt phía trên hoặc bên trái các trường nhập:

```vue-html
<form action="/dataCollectionLocation" method="post" autocomplete="on">
  <div v-for="item in formItems" :key="item.id" class="form-item">
    <label :for="item.id">{{ item.label }}: </label>
    <input
      :type="item.type"
      :id="item.id"
      :name="item.id"
      v-model="item.value"
    />
  </div>
  <button type="submit">Gửi</button>
</form>
```

Hãy để ý là bạn có thể đặt `autocomplete='on'` trên phần tử form và nó sẽ áp dụng cho mọi input trong form. Bạn cũng có thể đặt các [giá trị khác nhau cho thuộc tính autocomplete](https://developer.mozilla.org/en-US/docs/Web/HTML/Attributes/autocomplete) cho từng input.

### Label {#labels}

Hãy cung cấp label để mô tả mục đích của mọi control trong form, bằng cách liên kết `for` với `id`:

```vue-html
<label for="name">Tên: </label>
<input type="text" name="name" id="name" v-model="name" />
```

Nếu bạn inspect phần tử này trong Chrome DevTools và mở tab Accessibility bên trong tab Elements, bạn sẽ thấy input lấy tên của nó từ label:

![Chrome Developer Tools showing input accessible name from label](./images/AccessibleLabelChromeDevTools.png)

:::warning Cảnh báo
Dù bạn có thể từng thấy label bọc luôn input như thế này:

```vue-html
<label>
  Tên:
  <input type="text" name="name" id="name" v-model="name" />
</label>
```

Việc khai báo label tường minh với `id` khớp nhau vẫn được công nghệ hỗ trợ hỗ trợ tốt hơn.
:::

#### `aria-label` {#aria-label}

Bạn cũng có thể gán cho input một tên truy cập được (accessible name) bằng [`aria-label`](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Attributes/aria-label).

```vue-html
<label for="name">Tên: </label>
<input
  type="text"
  name="name"
  id="name"
  v-model="name"
  :aria-label="nameLabel"
/>
```

Bạn có thể inspect phần tử này trong Chrome DevTools để thấy tên truy cập được đã thay đổi như thế nào:

![Chrome Developer Tools showing input accessible name from aria-label](./images/AccessibleARIAlabelDevTools.png)

#### `aria-labelledby` {#aria-labelledby}

Việc dùng [`aria-labelledby`](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Attributes/aria-labelledby) khá giống với `aria-label`, nhưng nó được dùng khi văn bản nhãn đang hiển thị sẵn trên màn hình. Nó được ghép với phần tử khác thông qua `id`, và bạn có thể nối nhiều `id` lại với nhau:

```vue-html
<form
  class="demo"
  action="/dataCollectionLocation"
  method="post"
  autocomplete="on"
>
  <h1 id="billing">Thông tin thanh toán</h1>
  <div class="form-item">
    <label for="name">Tên: </label>
    <input
      type="text"
      name="name"
      id="name"
      v-model="name"
      aria-labelledby="billing name"
    />
  </div>
  <button type="submit">Gửi</button>
</form>
```

![Chrome Developer Tools showing input accessible name from aria-labelledby](./images/AccessibleARIAlabelledbyDevTools.png)

#### `aria-describedby` {#aria-describedby}

[`aria-describedby`](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Attributes/aria-describedby) được dùng gần giống như `aria-labelledby`, nhưng nó cung cấp thêm phần mô tả với thông tin bổ sung mà người dùng có thể cần. Cách này rất phù hợp để mô tả tiêu chí của một input:

```vue-html
<form
  class="demo"
  action="/dataCollectionLocation"
  method="post"
  autocomplete="on"
>
  <h1 id="billing">Thông tin thanh toán</h1>
  <div class="form-item">
    <label for="name">Họ và tên: </label>
    <input
      type="text"
      name="name"
      id="name"
      v-model="name"
      aria-labelledby="billing name"
      aria-describedby="nameDescription"
    />
    <p id="nameDescription">Vui lòng nhập cả họ và tên.</p>
  </div>
  <button type="submit">Gửi</button>
</form>
```

Bạn có thể xem phần mô tả này bằng cách inspect trong Chrome DevTools:

![Chrome Developer Tools showing input accessible name from aria-labelledby and description with aria-describedby](./images/AccessibleARIAdescribedby.png)

### Placeholder {#placeholder}

Nên tránh dùng placeholder vì chúng có thể gây nhầm lẫn cho nhiều người dùng.

Một trong các vấn đề của placeholder là mặc định chúng không đạt [tiêu chí về độ tương phản màu](https://www.w3.org/WAI/WCAG21/Understanding/contrast-minimum.html). Nếu bạn chỉnh lại cho đủ tương phản thì placeholder rất dễ trông giống như dữ liệu đã được điền sẵn vào ô nhập. Trong ví dụ dưới đây, bạn có thể thấy placeholder của Last Name khi đạt độ tương phản yêu cầu lại trông như dữ liệu có sẵn:

![Accessible placeholder](./images/AccessiblePlaceholder.png)

```vue-html
<form
  class="demo"
  action="/dataCollectionLocation"
  method="post"
  autocomplete="on"
>
  <div v-for="item in formItems" :key="item.id" class="form-item">
    <label :for="item.id">{{ item.label }}: </label>
    <input
      type="text"
      :id="item.id"
      :name="item.id"
      v-model="item.value"
      :placeholder="item.placeholder"
    />
  </div>
  <button type="submit">Gửi</button>
</form>
```

```css
/* https://www.w3schools.com/howto/howto_css_placeholder.asp */

#lastName::placeholder {
  /* Chrome, Firefox, Opera, Safari 10.1+ */
  color: black;
  opacity: 1; /* Firefox */
}

#lastName:-ms-input-placeholder {
  /* Internet Explorer 10-11 */
  color: black;
}

#lastName::-ms-input-placeholder {
  /* Microsoft Edge */
  color: black;
}
```

Tốt nhất là cung cấp toàn bộ thông tin mà người dùng cần để điền form ở bên ngoài các input.

### Hướng dẫn nhập liệu {#instructions}

Khi thêm hướng dẫn cho các trường input, hãy bảo đảm bạn liên kết chúng đúng cách với input.
Bạn có thể thêm hướng dẫn và ràng buộc nhiều `id` trong một [`aria-labelledby`](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Attributes/aria-labelledby). Cách này cho phép thiết kế linh hoạt hơn.

```vue-html
<fieldset>
  <legend>Dùng aria-labelledby</legend>
  <label id="date-label" for="date">Ngày hiện tại: </label>
  <input
    type="date"
    name="date"
    id="date"
    aria-labelledby="date-label date-instructions"
  />
  <p id="date-instructions">MM/DD/YYYY</p>
</fieldset>
```

Hoặc bạn có thể gắn phần hướng dẫn cho input bằng [`aria-describedby`](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Attributes/aria-describedby):

```vue-html
<fieldset>
  <legend>Dùng aria-describedby</legend>
  <label id="dob" for="dob">Ngày sinh: </label>
  <input type="date" name="dob" id="dob" aria-describedby="dob-instructions" />
  <p id="dob-instructions">MM/DD/YYYY</p>
</fieldset>
```

### Ẩn nội dung {#hiding-content}

Thông thường, không nên ẩn label khỏi giao diện, kể cả khi input đã có tên truy cập được (accessible name). Tuy vậy, nếu chức năng của input có thể hiểu được từ nội dung xung quanh, ta có thể ẩn phần label hiển thị.

Hãy xem trường tìm kiếm sau:

```vue-html
<form role="search">
  <label for="search" class="hidden-visually">Tìm kiếm: </label>
  <input type="text" name="search" id="search" v-model="search" />
  <button type="submit">Tìm kiếm</button>
</form>
```

Ta có thể làm vậy vì nút tìm kiếm sẽ giúp người dùng nhìn thấy hiểu được mục đích của ô nhập.

Ta có thể dùng CSS để ẩn phần tử khỏi giao diện nhưng vẫn giữ chúng khả dụng cho công nghệ hỗ trợ:

```css
.hidden-visually {
  position: absolute;
  overflow: hidden;
  white-space: nowrap;
  margin: 0;
  padding: 0;
  height: 1px;
  width: 1px;
  clip: rect(0 0 0 0);
  clip-path: inset(100%);
}
```

#### `aria-hidden="true"` {#aria-hidden-true}

Việc thêm `aria-hidden="true"` sẽ ẩn phần tử khỏi công nghệ hỗ trợ nhưng vẫn để nó hiển thị với những người dùng khác. Không dùng nó trên những phần tử có thể nhận focus, chỉ nên dùng cho nội dung trang trí, nội dung lặp lại hoặc nội dung nằm ngoài màn hình.

```vue-html
<p>Dòng này không bị ẩn khỏi screen reader.</p>
<p aria-hidden="true">Dòng này bị ẩn khỏi screen reader.</p>
```

### Button {#buttons}

Khi dùng button bên trong form, bạn phải đặt `type` để tránh việc form bị submit ngoài ý muốn.
Bạn cũng có thể dùng `input` để tạo button:

```vue-html
<form action="/dataCollectionLocation" method="post" autocomplete="on">
  <!-- Button -->
  <button type="button">Hủy</button>
  <button type="submit">Gửi</button>

  <!-- Input dạng button -->
  <input type="button" value="Hủy" />
  <input type="submit" value="Gửi" />
</form>
```

### Hình ảnh có chức năng {#functional-images}

Bạn có thể dùng kỹ thuật này để tạo những hình ảnh có chức năng.

- Trường input

```vue-html
<form role="search">
  <label for="search" class="hidden-visually">Tìm kiếm: </label>
  <input type="text" name="search" id="search" v-model="search" />
  <input
    type="image"
    class="btnImg"
    src="https://img.icons8.com/search"
    alt="Tìm kiếm"
  />
</form>
```

- Icon

```vue-html
<form role="search">
  <label for="searchIcon" class="hidden-visually">Tìm kiếm: </label>
  <input type="text" name="searchIcon" id="searchIcon" v-model="searchIcon" />
  <button type="submit">
    <i class="fas fa-search" aria-hidden="true"></i>
    <span class="hidden-visually">Tìm kiếm</span>
  </button>
</form>
```

## Tiêu chuẩn {#standards}

Sáng kiến Web Accessibility Initiative (WAI) của World Wide Web Consortium (W3C) xây dựng các tiêu chuẩn accessibility cho web ở nhiều thành phần khác nhau:

- [User Agent Accessibility Guidelines (UAAG)](https://www.w3.org/WAI/standards-guidelines/uaag/)
  - trình duyệt web và trình phát media, bao gồm cả một số khía cạnh của công nghệ hỗ trợ
- [Authoring Tool Accessibility Guidelines (ATAG)](https://www.w3.org/WAI/standards-guidelines/atag/)
  - công cụ biên soạn nội dung
- [Web Content Accessibility Guidelines (WCAG)](https://www.w3.org/WAI/standards-guidelines/wcag/)
  - nội dung web, được dùng bởi lập trình viên, công cụ biên soạn và công cụ đánh giá accessibility

### Web Content Accessibility Guidelines (WCAG) {#web-content-accessibility-guidelines-wcag}

[WCAG 2.1](https://www.w3.org/TR/WCAG21/) mở rộng từ [WCAG 2.0](https://www.w3.org/TR/WCAG20/) và cho phép triển khai các công nghệ mới bằng cách phản ánh những thay đổi của web. W3C khuyến khích dùng phiên bản WCAG mới nhất khi xây dựng hoặc cập nhật chính sách accessibility cho web.

#### Bốn nguyên tắc chính của WCAG 2.1 (thường viết tắt là POUR) {#wcag-2-1-four-main-guiding-principles-abbreviated-as-pour}

- [Perceivable](https://www.w3.org/TR/WCAG21/#perceivable)
  - Người dùng phải có thể nhận biết được thông tin được trình bày
- [Operable](https://www.w3.org/TR/WCAG21/#operable)
  - Form, control và điều hướng của giao diện phải sử dụng được
- [Understandable](https://www.w3.org/TR/WCAG21/#understandable)
  - Thông tin và cách vận hành của giao diện phải dễ hiểu với mọi người dùng
- [Robust](https://www.w3.org/TR/WCAG21/#robust)
  - Người dùng phải có thể truy cập nội dung ngay cả khi công nghệ tiếp tục thay đổi

#### Web Accessibility Initiative – Accessible Rich Internet Applications (WAI-ARIA) {#web-accessibility-initiative-–-accessible-rich-internet-applications-wai-aria}

WAI-ARIA của W3C cung cấp hướng dẫn về cách xây dựng nội dung động và các control giao diện người dùng nâng cao.

- [Accessible Rich Internet Applications (WAI-ARIA) 1.2](https://www.w3.org/TR/wai-aria-1.2/)
- [WAI-ARIA Authoring Practices 1.2](https://www.w3.org/TR/wai-aria-practices-1.2/)

## Tài nguyên tham khảo {#resources}

### Tài liệu {#documentation}

- [WCAG 2.0](https://www.w3.org/TR/WCAG20/)
- [WCAG 2.1](https://www.w3.org/TR/WCAG21/)
- [Accessible Rich Internet Applications (WAI-ARIA) 1.2](https://www.w3.org/TR/wai-aria-1.2/)
- [WAI-ARIA Authoring Practices 1.2](https://www.w3.org/TR/wai-aria-practices-1.2/)

### Công nghệ hỗ trợ {#assistive-technologies}

- Screen reader
  - [NVDA](https://www.nvaccess.org/download/)
  - [VoiceOver](https://www.apple.com/accessibility/mac/vision/)
  - [JAWS](https://www.freedomscientific.com/products/software/jaws/?utm_term=jaws%20screen%20reader&utm_source=adwords&utm_campaign=All+Products&utm_medium=ppc&hsa_tgt=kwd-394361346638&hsa_cam=200218713&hsa_ad=296201131673&hsa_kw=jaws%20screen%20reader&hsa_grp=52663682111&hsa_net=adwords&hsa_mt=e&hsa_src=g&hsa_acc=1684996396&hsa_ver=3&gclid=Cj0KCQjwnv71BRCOARIsAIkxW9HXKQ6kKNQD0q8a_1TXSJXnIuUyb65KJeTWmtS6BH96-5he9dsNq6oaAh6UEALw_wcB)
  - [ChromeVox](https://chrome.google.com/webstore/detail/chromevox-classic-extensi/kgejglhpjiefppelpmljglcjbhoiplfn?hl=en)
- Công cụ phóng to
  - [MAGic](https://www.freedomscientific.com/products/software/magic/)
  - [ZoomText](https://www.freedomscientific.com/products/software/zoomtext/)
  - [Magnifier](https://support.microsoft.com/en-us/help/11542/windows-use-magnifier-to-make-things-easier-to-see)

### Kiểm thử {#testing}

- Công cụ tự động
  - [Lighthouse](https://chrome.google.com/webstore/detail/lighthouse/blipmdconlkpinefehnmjammfjpmpbjk)
  - [WAVE](https://chrome.google.com/webstore/detail/wave-evaluation-tool/jbbplnpkjmmeebjpijfedlgcdilocofh)
  - [ARC Toolkit](https://chrome.google.com/webstore/detail/arc-toolkit/chdkkkccnlfncngelccgbgfmjebmkmce?hl=en-US)
- Công cụ màu sắc
  - [WebAim Color Contrast](https://webaim.org/resources/contrastchecker/)
  - [WebAim Link Color Contrast](https://webaim.org/resources/linkcontrastchecker)
- Công cụ hữu ích khác
  - [HeadingMap](https://chrome.google.com/webstore/detail/headingsmap/flbjommegcjonpdmenkdiocclhjacmbi?hl=en…)
  - [Color Oracle](https://colororacle.org)
  - [NerdeFocus](https://chrome.google.com/webstore/detail/nerdefocus/lpfiljldhgjecfepfljnbjnbjfhennpd?hl=en-US…)
  - [Visual Aria](https://chrome.google.com/webstore/detail/visual-aria/lhbmajchkkmakajkjenkchhnhbadmhmk?hl=en-US)
  - [Silktide Website Accessibility Simulator](https://chrome.google.com/webstore/detail/silktide-website-accessib/okcpiimdfkpkjcbihbmhppldhiebhhaf?hl=en-US)

### Người dùng {#users}

Tổ chức Y tế Thế giới ước tính rằng 15% dân số toàn cầu có một dạng khuyết tật nào đó, trong đó khoảng 2-4% là nghiêm trọng. Tức là vào khoảng 1 tỷ người trên toàn thế giới. Điều này khiến người khuyết tật trở thành nhóm thiểu số lớn nhất trên thế giới.

Có rất nhiều dạng khuyết tật khác nhau, nhưng có thể chia tương đối thành bốn nhóm:

- _[Thị giác](https://webaim.org/articles/visual/)_ - Những người dùng này có thể hưởng lợi từ screen reader, công cụ phóng to màn hình, điều chỉnh độ tương phản màn hình hoặc màn hình chữ nổi braille.
- _[Thính giác](https://webaim.org/articles/auditory/)_ - Những người dùng này có thể hưởng lợi từ phụ đề, bản chép lời hoặc video ngôn ngữ ký hiệu.
- _[Vận động](https://webaim.org/articles/motor/)_ - Những người dùng này có thể hưởng lợi từ nhiều loại [công nghệ hỗ trợ cho suy giảm vận động](https://webaim.org/articles/motor/assistive), như phần mềm nhận diện giọng nói, theo dõi mắt, điều khiển một nút, que đội đầu, công tắc sip-and-puff, chuột bi cỡ lớn, bàn phím thích nghi hoặc các công nghệ hỗ trợ khác.
- _[Nhận thức](https://webaim.org/articles/cognitive/)_ - Những người dùng này có thể hưởng lợi từ media bổ trợ, cách tổ chức nội dung có cấu trúc, và lối viết rõ ràng, đơn giản.

Hãy xem các liên kết sau từ WebAim để hiểu thêm góc nhìn của người dùng:

- [Web Accessibility Perspectives: Explore the Impact and Benefits for Everyone](https://www.w3.org/WAI/perspective-videos/)
