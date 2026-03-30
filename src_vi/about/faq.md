# Câu hỏi Thường gặp {#frequently-asked-questions}

## Ai duy trì Vue? {#who-maintains-vue}

Vue là một dự án độc lập, do cộng đồng thúc đẩy. Nó được tạo ra bởi [Evan You](https://x.com/youyuxi) vào năm 2014 như một dự án cá nhân. Ngày nay, Vue được duy trì tích cực bởi [một nhóm gồm cả thành viên toàn thời gian và tình nguyện từ khắp nơi trên thế giới](/about/team), trong đó Evan đóng vai trò là trưởng dự án. Bạn có thể tìm hiểu thêm về câu chuyện của Vue trong [bộ phim tài liệu](https://www.youtube.com/watch?v=OrxmtDw4pVI) này.

Sự phát triển của Vue chủ yếu được tài trợ qua các nhà tài trợ và chúng tôi đã bền vững về mặt tài chính kể từ năm 2016. Nếu bạn hoặc doanh nghiệp của bạn hưởng lợi từ Vue, hãy cân nhắc [tài trợ cho chúng tôi](/sponsor/) để hỗ trợ sự phát triển của Vue!

## Sự khác biệt giữa Vue 2 và Vue 3 là gì? {#what-s-the-difference-between-vue-2-and-vue-3}

Vue 3 là phiên bản chính mới nhất hiện tại của Vue. Nó chứa các tính năng mới không có trong Vue 2, như Teleport, Suspense, và nhiều root element trong mỗi template. Nó cũng chứa các thay đổi không tương thích ngược khiến nó không tương thích với Vue 2. Chi tiết đầy đủ được ghi lại trong [Hướng dẫn Migration Vue 3](https://v3-migration.vuejs.org/).

Bất chấp những khác biệt, phần lớn API của Vue được chia sẻ giữa hai phiên bản chính, vì vậy hầu hết kiến thức Vue 2 của bạn sẽ tiếp tục hoạt động trong Vue 3. Đặc biệt, Composition API ban đầu là tính năng chỉ dành cho Vue 3, nhưng đã được backport sang Vue 2 và có sẵn trong [Vue 2.7](https://github.com/vuejs/vue/blob/main/CHANGELOG.md#270-2022-07-01).

Nói chung, Vue 3 cung cấp bundle size nhỏ hơn, hiệu suất tốt hơn, khả năng mở rộng tốt hơn, và hỗ trợ TypeScript / IDE tốt hơn. Nếu bạn đang bắt đầu dự án mới ngày nay, Vue 3 là lựa chọn được khuyến nghị. Chỉ có một vài lý do để bạn cân nhắc Vue 2 lúc này:

- Bạn cần hỗ trợ IE11. Vue 3 tận dụng các tính năng JavaScript hiện đại và không hỗ trợ IE11.

Nếu bạn có ý định migrate ứng dụng Vue 2 hiện có sang Vue 3, hãy tham khảo [hướng dẫn migration](https://v3-migration.vuejs.org/).

## Vue 2 còn được hỗ trợ không? {#is-vue-2-still-supported}

Vue 2.7, được ra mắt vào tháng 7 năm 2022, là bản phát hành minor cuối cùng của dòng Vue 2. Vue 2 đã bước vào chế độ bảo trì: nó sẽ không còn ra mắt tính năng mới, nhưng sẽ tiếp tục nhận các bản sửa lỗi quan trọng và cập nhật bảo mật trong 18 tháng kể từ ngày phát hành 2.7. Điều này có nghĩa là **Vue 2 đã kết thúc hỗ trợ vào ngày 31 tháng 12 năm 2023**.

Chúng tôi tin rằng điều này cung cấp đủ thời gian để hầu hết hệ sinh thái migrate sang Vue 3. Tuy nhiên, chúng tôi cũng hiểu rằng có thể có những nhóm hoặc dự án không thể nâng cấp trong thời hạn này trong khi vẫn cần đáp ứng các yêu cầu bảo mật và tuân thủ. Chúng tôi đang hợp tác với các chuyên gia trong ngành để cung cấp hỗ trợ mở rộng cho Vue 2 cho các nhóm có nhu cầu như vậy - nếu nhóm của bạn dự kiến sử dụng Vue 2 sau cuối năm 2023, hãy chủ động lên kế hoạch trước và tìm hiểu thêm về [Vue 2 Extended LTS](https://v2.vuejs.org/lts/).

## Vue sử dụng giấy phép gì? {#what-license-does-vue-use}

Vue là dự án mã nguồn mở miễn phí được phát hành theo [Giấy phép MIT](https://opensource.org/licenses/MIT).

## Vue hỗ trợ những trình duyệt nào? {#what-browsers-does-vue-support}

Phiên bản Vue mới nhất (3.x) chỉ hỗ trợ [các trình duyệt có hỗ trợ ES2016 native](https://caniuse.com/es2016). Điều này không bao gồm IE11. Vue 3.x sử dụng các tính năng ES2016 không thể polyfill trên các trình duyệt cũ hơn, vì vậy nếu bạn cần hỗ trợ trình duyệt cũ, bạn sẽ cần dùng Vue 2.x thay thế.

## Vue có đáng tin cậy không? {#is-vue-reliable}

Vue là một framework trưởng thành và đã được kiểm chứng trong thực tế. Đây là một trong những JavaScript framework được dùng rộng rãi nhất trong production hiện nay, với hơn 1,5 triệu người dùng trên toàn thế giới, và được tải xuống gần 10 triệu lần mỗi tháng trên npm.

Vue được dùng trong production bởi các tổ chức nổi tiếng theo nhiều cách khác nhau trên toàn thế giới, bao gồm Wikimedia Foundation, NASA, Apple, Google, Microsoft, GitLab, Zoom, Tencent, Weibo, Bilibili, Kuaishou, và nhiều hơn nữa.

## Vue có nhanh không? {#is-vue-fast}

Vue 3 là một trong những frontend framework chính thống hiệu suất cao nhất, và xử lý hầu hết các trường hợp sử dụng ứng dụng web với dễ dàng, không cần tối ưu hóa thủ công.

Trong các tình huống stress-test, Vue vượt trội hơn React và Angular đáng kể trong [js-framework-benchmark](https://krausest.github.io/js-framework-benchmark/current.html). Nó cũng so sánh ngang ngửa với một số framework không dùng Virtual DOM ở cấp production nhanh nhất trong benchmark.

Lưu ý rằng các benchmark tổng hợp như trên tập trung vào hiệu suất render thô với các tối ưu hóa chuyên biệt và có thể không hoàn toàn đại diện cho kết quả hiệu suất thực tế. Nếu bạn quan tâm hơn đến hiệu suất tải trang, bạn được chào đón kiểm tra trang web này bằng [WebPageTest](https://www.webpagetest.org/lighthouse) hoặc [PageSpeed Insights](https://pagespeed.web.dev/). Trang web này được cung cấp bởi chính Vue, với SSG pre-rendering, full page hydration và SPA client-side navigation. Nó đạt điểm 100 về hiệu suất trên Moto G4 giả lập với giới hạn CPU 4x qua mạng 4G chậm.

Bạn có thể tìm hiểu thêm về cách Vue tự động tối ưu hóa hiệu suất runtime trong phần [Cơ chế Render](/guide/extras/rendering-mechanism), và cách tối ưu hóa ứng dụng Vue trong các trường hợp đặc biệt đòi hỏi trong [Hướng dẫn Tối ưu hóa Hiệu suất](/guide/best-practices/performance).

## Vue có nhẹ không? {#is-vue-lightweight}

Khi bạn dùng build tool, nhiều API của Vue có thể ["tree-shake"](https://developer.mozilla.org/en-US/docs/Glossary/Tree_shaking) được. Ví dụ: nếu bạn không dùng component `<Transition>` tích hợp, nó sẽ không được bao gồm trong bundle production cuối cùng.

Ứng dụng Vue hello world chỉ dùng các API tối thiểu tuyệt đối có kích thước cơ sở chỉ khoảng **16kb**, với minification và nén brotli. Kích thước thực tế của ứng dụng sẽ phụ thuộc vào số lượng tính năng tùy chọn bạn sử dụng từ framework. Trong trường hợp hiếm gặp khi một ứng dụng dùng mọi tính năng Vue cung cấp, tổng kích thước runtime khoảng **27kb**.

Khi dùng Vue không có build tool, chúng ta không chỉ mất tree-shaking, mà còn phải gửi template compiler đến trình duyệt. Điều này làm phình kích thước lên khoảng **41kb**. Do đó, nếu bạn dùng Vue chủ yếu để progressive enhancement mà không có build step, hãy cân nhắc dùng [petite-vue](https://github.com/vuejs/petite-vue) (chỉ **6kb**) thay thế.

Một số framework, như Svelte, dùng chiến lược biên dịch tạo ra output cực kỳ nhẹ trong các tình huống single-component. Tuy nhiên, [nghiên cứu của chúng tôi](https://github.com/yyx990803/vue-svelte-size-analysis) cho thấy sự khác biệt về kích thước phụ thuộc nhiều vào số lượng component trong ứng dụng. Trong khi Vue có kích thước cơ sở nặng hơn, nó tạo ra ít code hơn cho mỗi component. Trong các tình huống thực tế, ứng dụng Vue có thể nhẹ hơn.

## Vue có thể mở rộng không? {#does-vue-scale}

Có. Mặc dù quan niệm sai lầm phổ biến rằng Vue chỉ phù hợp cho các trường hợp sử dụng đơn giản, Vue hoàn toàn có khả năng xử lý các ứng dụng quy mô lớn:

- [Single-File Component](/guide/scaling-up/sfc) cung cấp mô hình phát triển dạng module cho phép các phần khác nhau của ứng dụng được phát triển độc lập.

- [Composition API](/guide/reusability/composables) cung cấp tích hợp TypeScript hạng nhất và cho phép các pattern rõ ràng để tổ chức, trích xuất và tái sử dụng logic phức tạp.

- [Hỗ trợ tooling toàn diện](/guide/scaling-up/tooling) đảm bảo trải nghiệm phát triển mượt mà khi ứng dụng phát triển.

- Ngưỡng vào thấp và tài liệu xuất sắc giúp giảm chi phí onboarding và đào tạo cho developer mới.

## Làm thế nào để đóng góp cho Vue? {#how-do-i-contribute-to-vue}

Chúng tôi đánh giá cao sự quan tâm của bạn! Vui lòng xem [Hướng dẫn Cộng đồng](/about/community-guide) của chúng tôi.

## Tôi nên dùng Options API hay Composition API? {#should-i-use-options-api-or-composition-api}

Nếu bạn mới với Vue, chúng tôi cung cấp so sánh cấp cao giữa hai phong cách [ở đây](/guide/introduction#which-to-choose).

Nếu bạn đã dùng Options API trước đây và đang đánh giá Composition API, hãy xem [FAQ này](/guide/extras/composition-api-faq).

## Tôi nên dùng JavaScript hay TypeScript với Vue? {#should-i-use-javascript-or-typescript-with-vue}

Trong khi Vue được triển khai bằng TypeScript và cung cấp hỗ trợ TypeScript hạng nhất, nó không ép buộc quan điểm về việc bạn nên dùng TypeScript hay không.

Hỗ trợ TypeScript là một cân nhắc quan trọng khi các tính năng mới được thêm vào Vue. Các API được thiết kế với TypeScript trong tâm trí thường dễ hiểu hơn cho IDE và linter, ngay cả khi bạn không tự mình dùng TypeScript. Mọi người đều được lợi. Các API Vue cũng được thiết kế để hoạt động theo cùng cách trong cả JavaScript và TypeScript nhất có thể.

Việc áp dụng TypeScript liên quan đến sự đánh đổi giữa độ phức tạp khi onboarding và lợi ích khả năng bảo trì lâu dài. Liệu sự đánh đổi đó có thể được biện minh hay không có thể thay đổi tùy thuộc vào nền tảng của nhóm và quy mô dự án của bạn, nhưng Vue không thực sự là yếu tố ảnh hưởng khi đưa ra quyết định đó.

## Vue so sánh thế nào với Web Component? {#how-does-vue-compare-to-web-components}

Vue được tạo ra trước khi Web Component có sẵn native, và một số khía cạnh thiết kế của Vue (ví dụ: slot) được lấy cảm hứng từ mô hình Web Component.

Các đặc tả Web Component tương đối cấp thấp, vì chúng tập trung vào việc định nghĩa custom element. Là một framework, Vue giải quyết các mối lo ngại cấp cao hơn bổ sung như render DOM hiệu quả, quản lý reactive state, tooling, client-side routing, và server-side rendering.

Vue cũng hỗ trợ đầy đủ việc sử dụng hoặc xuất ra native custom element - xem [Hướng dẫn Vue và Web Component](/guide/extras/web-components) để biết thêm chi tiết.

<!-- ## TODO Vue so sánh thế nào với React? -->

<!-- ## TODO Vue so sánh thế nào với Angular? -->
