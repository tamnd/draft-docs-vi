# Bảo mật {#security}

## Báo cáo lỗ hổng bảo mật {#reporting-vulnerabilities}

Khi nhận được báo cáo lỗ hổng, đó ngay lập tức trở thành ưu tiên cao nhất của chúng tôi — một contributor toàn thời gian sẽ gác mọi việc khác để xử lý. Để báo cáo lỗ hổng, vui lòng gửi email tới [security@vuejs.org](mailto:security@vuejs.org).

Dù việc phát hiện ra lỗ hổng mới là khá hiếm, chúng tôi vẫn luôn khuyến nghị bạn dùng phiên bản mới nhất của Vue và các thư viện đồng hành chính thức của nó để ứng dụng của bạn an toàn nhất có thể.

## Quy tắc số 1: Không bao giờ dùng template không đáng tin cậy {#rule-no-1-never-use-non-trusted-templates}

Quy tắc bảo mật nền tảng nhất khi dùng Vue là **không bao giờ dùng nội dung không đáng tin cậy làm template cho component**. Làm như vậy chẳng khác nào cho phép thực thi JavaScript tùy ý trong ứng dụng của bạn, và còn tệ hơn nếu đoạn code đó được chạy trong lúc server-side rendering, vì nó có thể dẫn tới việc máy chủ bị xâm nhập. Ví dụ:

```js
Vue.createApp({
  template: `<div>` + userProvidedString + `</div>` // TUYỆT ĐỐI ĐỪNG LÀM VẬY
}).mount('#app')
```

Template của Vue được biên dịch thành JavaScript, và các biểu thức bên trong template sẽ được thực thi như một phần của quá trình render. Dù các biểu thức được đánh giá trong một ngữ cảnh render cụ thể, sự phức tạp của môi trường thực thi toàn cục khiến Vue không thể bảo vệ bạn hoàn toàn khỏi mã độc mà không phải trả giá hiệu năng quá lớn. Cách đơn giản và hiệu quả nhất để tránh hẳn nhóm vấn đề này là luôn bảo đảm template Vue của bạn là nội dung đáng tin cậy, hoàn toàn do bạn kiểm soát.

## Vue bảo vệ bạn như thế nào {#what-vue-does-to-protect-you}

### Nội dung HTML {#html-content}

Dù dùng template hay render function, nội dung đều được escape tự động. Nghĩa là trong template này:

```vue-html
<h1>{{ userProvidedString }}</h1>
```

nếu `userProvidedString` chứa:

```js
'<script>alert("hi")</script>'
```

thì nó sẽ được escape thành HTML như sau:

```vue-html
&lt;script&gt;alert(&quot;hi&quot;)&lt;/script&gt;
```

nhờ vậy ngăn được việc chèn script. Việc escape này được thực hiện bằng API gốc của trình duyệt như `textContent`, nên chỉ khi bản thân trình duyệt có lỗ hổng thì chỗ này mới có vấn đề.

### Ràng buộc thuộc tính {#attribute-bindings}

Tương tự, các ràng buộc thuộc tính động cũng được escape tự động. Nghĩa là trong template này:

```vue-html
<h1 :title="userProvidedString">
  hello
</h1>
```

nếu `userProvidedString` chứa:

```js
'" onclick="alert(\'hi\')'
```

thì nó sẽ được escape thành HTML như sau:

```vue-html
&quot; onclick=&quot;alert('hi')
```

nhờ đó ngăn việc đóng thuộc tính `title` để chèn HTML tùy ý mới. Việc escape này được thực hiện bằng API gốc của trình duyệt như `setAttribute`, nên chỉ khi bản thân trình duyệt có lỗ hổng thì chỗ này mới có vấn đề.

## Những rủi ro có thể gặp {#potential-dangers}

Trong bất kỳ ứng dụng web nào, việc cho phép nội dung do người dùng cung cấp mà chưa được làm sạch chạy như HTML, CSS hoặc JavaScript đều tiềm ẩn rủi ro, nên cần tránh tối đa. Tuy vậy, vẫn có những lúc một mức rủi ro nào đó có thể chấp nhận được.

Ví dụ, những dịch vụ như CodePen và JSFiddle cho phép chạy nội dung do người dùng cung cấp, nhưng trong một bối cảnh mà điều này là mong đợi và đã được sandbox ở một mức nào đó trong iframe. Trong những trường hợp mà một tính năng quan trọng tự thân đã đòi hỏi một mức rủi ro nhất định, nhóm của bạn phải tự cân nhắc tầm quan trọng của tính năng đó so với những tình huống xấu nhất mà lỗ hổng có thể gây ra.

### Chèn HTML {#html-injection}

Như bạn đã thấy ở trên, Vue tự động escape nội dung HTML, giúp bạn không vô tình chèn HTML có thể thực thi vào ứng dụng. Tuy vậy, **trong trường hợp bạn biết chắc HTML đó là an toàn**, bạn có thể render HTML một cách tường minh:

- Dùng template:

  ```vue-html
  <div v-html="userProvidedHtml"></div>
  ```

- Dùng render function:

  ```js
  h('div', {
    innerHTML: this.userProvidedHtml
  })
  ```

- Dùng render function với JSX:

  ```jsx
  <div innerHTML={this.userProvidedHtml}></div>
  ```

:::warning
HTML do người dùng cung cấp không bao giờ có thể được xem là an toàn 100%, trừ khi nó nằm trong một iframe đã được sandbox hoặc trong một phần của ứng dụng mà chỉ chính người đã viết HTML đó mới có thể nhìn thấy nó. Ngoài ra, cho phép người dùng tự viết template Vue cũng mang theo rủi ro tương tự.
:::

### Chèn URL {#url-injection}

Trong một URL như thế này:

```vue-html
<a :href="userProvidedUrl">
  click me
</a>
```

Sẽ có rủi ro bảo mật nếu URL đó chưa được "sanitize" để ngăn việc thực thi JavaScript qua `javascript:`. Có những thư viện như [sanitize-url](https://www.npmjs.com/package/@braintree/sanitize-url) có thể hỗ trợ việc này, nhưng lưu ý rằng nếu bạn phải sanitize URL ở phía frontend thì nghĩa là bạn đã có một vấn đề bảo mật rồi. **URL do người dùng cung cấp luôn phải được backend sanitize trước cả khi lưu vào database.** Khi đó, vấn đề sẽ được giải quyết cho _mọi_ client kết nối vào API của bạn, kể cả ứng dụng mobile native. Cũng cần lưu ý là ngay cả với URL đã được sanitize, Vue cũng không thể giúp bạn bảo đảm rằng chúng luôn dẫn tới một đích đến an toàn.

### Chèn style {#style-injection}

Hãy nhìn vào ví dụ này:

```vue-html
<a
  :href="sanitizedUrl"
  :style="userProvidedStyles"
>
  click me
</a>
```

Giả sử `sanitizedUrl` đã được sanitize nên chắc chắn là một URL hợp lệ chứ không phải JavaScript. Tuy vậy, với `userProvidedStyles`, người dùng độc hại vẫn có thể cung cấp CSS để "click jack", chẳng hạn style liên kết thành một khung trong suốt phủ lên nút "Log in". Khi đó, nếu `https://user-controlled-website.com/` được dựng giống trang đăng nhập của ứng dụng bạn, họ có thể vừa lấy được thông tin đăng nhập thật của người dùng.

Bạn cũng có thể hình dung ra rằng nếu cho phép nội dung do người dùng cung cấp đi vào phần tử `<style>`, lỗ hổng sẽ còn nghiêm trọng hơn nữa, vì khi đó người dùng có toàn quyền style cả trang. Đó là lý do Vue chặn việc render thẻ style bên trong template, ví dụ như:

```vue-html
<style>{{ userProvidedStyles }}</style>
```

Để bảo vệ người dùng khỏi clickjacking một cách đầy đủ, chúng tôi khuyến nghị chỉ cho phép kiểm soát toàn bộ CSS bên trong một iframe đã được sandbox. Hoặc nếu bạn muốn cho người dùng điều khiển style thông qua ràng buộc style, hãy dùng [cú pháp object](/guide/essentials/class-and-style#binding-to-objects-1) và chỉ cho phép họ cung cấp giá trị cho một số property cụ thể mà bạn cho là an toàn, như sau:

```vue-html
<a
  :href="sanitizedUrl"
  :style="{
    color: userProvidedColor,
    background: userProvidedBackground
  }"
>
  click me
</a>
```

### Chèn JavaScript {#javascript-injection}

Chúng tôi rất không khuyến khích việc render phần tử `<script>` bằng Vue, vì template và render function không bao giờ nên có side effect. Tuy vậy, đây không phải là cách duy nhất để đưa vào những chuỗi có thể bị đánh giá như JavaScript lúc chạy.

Mọi phần tử HTML đều có những thuộc tính nhận chuỗi JavaScript, ví dụ như `onclick`, `onfocus` và `onmouseenter`. Việc bind JavaScript do người dùng cung cấp vào bất kỳ thuộc tính sự kiện nào như thế đều là một rủi ro bảo mật, nên cần tránh.

:::warning
JavaScript do người dùng cung cấp không bao giờ có thể được xem là an toàn 100%, trừ khi nó nằm trong một iframe đã được sandbox hoặc trong một phần của ứng dụng mà chỉ chính người đã viết đoạn JavaScript đó mới có thể nhìn thấy nó.
:::

Đôi khi chúng tôi nhận được báo cáo lỗ hổng nói rằng có thể thực hiện cross-site scripting (XSS) trong template Vue. Nói chung, chúng tôi không xem các trường hợp đó là lỗ hổng thực sự, vì không có cách khả thi nào để bảo vệ lập trình viên khỏi hai tình huống sau:

1. Lập trình viên chủ động yêu cầu Vue render nội dung do người dùng cung cấp mà chưa sanitize như một template Vue. Điều này vốn dĩ là không an toàn, và Vue không có cách nào biết được nguồn gốc của nội dung đó.
2. Lập trình viên mount Vue lên cả một trang HTML vốn đã chứa nội dung render từ server và do người dùng cung cấp. Về bản chất, đây vẫn là cùng một vấn đề như ở mục 1, nhưng đôi khi lập trình viên có thể làm vậy mà không nhận ra. Cách này có thể dẫn tới lỗ hổng khi kẻ tấn công cung cấp HTML an toàn nếu coi là HTML thuần, nhưng lại không an toàn nếu bị Vue hiểu như template. Thực hành tốt nhất là **không bao giờ mount Vue lên những node có thể chứa nội dung render từ server và do người dùng cung cấp**.

## Thực hành tốt nhất {#best-practices}

Quy tắc chung là: nếu bạn cho phép nội dung do người dùng cung cấp chưa được sanitize thực thi dưới dạng HTML, JavaScript hay CSS, bạn đang tự mở cửa cho tấn công. Lời khuyên này đúng không chỉ với Vue mà với mọi framework, kể cả khi bạn không dùng framework nào.

Ngoài những khuyến nghị ở trên trong phần [Những rủi ro có thể gặp](#potential-dangers), chúng tôi cũng khuyên bạn làm quen với các tài nguyên sau:

- [HTML5 Security Cheat Sheet](https://html5sec.org/)
- [OWASP's Cross Site Scripting (XSS) Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.html)

Sau đó, hãy dùng những gì bạn học được để rà lại source code của các dependency, nếu có dependency nào chứa component bên thứ ba hoặc có ảnh hưởng tới những gì được render ra DOM.

## Phối hợp với backend {#backend-coordination}

Các lỗ hổng bảo mật ở tầng HTTP, như cross-site request forgery (CSRF/XSRF) và cross-site script inclusion (XSSI), chủ yếu được xử lý ở phía backend nên không phải là mối quan tâm trực tiếp của Vue. Tuy vậy, bạn vẫn nên trao đổi với đội backend để hiểu cách tương tác tốt nhất với API của họ, ví dụ như gửi CSRF token cùng với lần submit form.

## Server-Side Rendering (SSR) {#server-side-rendering-ssr}

Khi dùng SSR sẽ có thêm một số điểm cần lưu ý về bảo mật, vì vậy hãy nhớ làm theo các thực hành tốt nhất được nêu xuyên suốt trong [tài liệu SSR của chúng tôi](/guide/scaling-up/ssr) để tránh lỗ hổng.
