# Các Cách Sử Dụng Vue {#ways-of-using-vue}

Chúng tôi tin rằng web không có một hướng đi nào "phù hợp với tất cả". Đó là lý do Vue được thiết kế để linh hoạt và có thể áp dụng dần dần. Tùy vào trường hợp sử dụng, Vue có thể được dùng theo nhiều cách khác nhau để đạt điểm cân bằng tối ưu giữa độ phức tạp của stack, trải nghiệm phát triển và hiệu năng đầu ra.

## Script Độc Lập {#standalone-script}

Vue có thể được dùng dưới dạng một file script độc lập, không cần bước build. Nếu bạn đã có một framework backend render phần lớn HTML, hoặc logic frontend của bạn chưa đủ phức tạp để cần đến bước build, thì đây là cách dễ nhất để tích hợp Vue vào stack hiện tại. Trong những trường hợp như vậy, bạn có thể xem Vue như một phiên bản thay thế jQuery theo hướng khai báo hơn.

Trước đây, chúng tôi từng cung cấp một bản phân phối khác tên là [petite-vue](https://github.com/vuejs/petite-vue), được tối ưu riêng cho việc tăng cường dần dần HTML hiện có. Tuy nhiên, petite-vue không còn được duy trì tích cực nữa; phiên bản cuối cùng được phát hành là Vue 3.2.27.

## Web Components Nhúng Vào Trang {#embedded-web-components}

Bạn có thể dùng Vue để [xây dựng Web Components chuẩn](/guide/extras/web-components) có thể được nhúng vào bất kỳ trang HTML nào, bất kể chúng được render theo cách nào. Lựa chọn này cho phép bạn tận dụng Vue theo cách hoàn toàn không phụ thuộc vào phía tiêu thụ: các web component tạo ra có thể được nhúng vào ứng dụng cũ, HTML tĩnh, hoặc thậm chí ứng dụng được xây dựng bằng framework khác.

## Single-Page Application (SPA) {#single-page-application-spa}

Một số ứng dụng cần mức độ tương tác cao, phiên làm việc dài, và logic có state ở frontend không hề đơn giản. Cách tốt nhất để xây dựng những ứng dụng như vậy là dùng kiến trúc mà trong đó Vue không chỉ kiểm soát toàn bộ trang, mà còn xử lý cập nhật dữ liệu và điều hướng mà không cần tải lại trang. Loại ứng dụng này thường được gọi là Single-Page Application (SPA).

Vue cung cấp các thư viện lõi và [hệ công cụ hỗ trợ toàn diện](/guide/scaling-up/tooling) với trải nghiệm phát triển rất tốt để xây dựng SPA hiện đại, bao gồm:

- Router phía client
- Chuỗi công cụ build cực nhanh
- Hỗ trợ từ IDE
- Devtools trên trình duyệt
- Tích hợp TypeScript
- Utility cho kiểm thử

SPA thường yêu cầu backend phải cung cấp các API endpoint, nhưng bạn cũng có thể kết hợp Vue với những giải pháp như [Inertia.js](https://inertiajs.com) để có được lợi ích của SPA mà vẫn giữ mô hình phát triển thiên về server.

## Fullstack / SSR {#fullstack-ssr}

SPA chạy hoàn toàn phía client có vấn đề khi ứng dụng nhạy cảm với SEO và thời gian hiển thị nội dung. Lý do là trình duyệt ban đầu chỉ nhận được một trang HTML gần như trống và phải chờ JavaScript tải xong mới có thể render nội dung.

Vue cung cấp API hạng nhất để "render" một ứng dụng Vue thành chuỗi HTML ở phía server. Nhờ đó, server có thể trả về HTML đã được render sẵn, cho phép người dùng cuối thấy nội dung ngay lập tức trong khi JavaScript vẫn đang được tải. Sau đó Vue sẽ "hydrate" ứng dụng ở phía client để biến nó thành tương tác được. Đây được gọi là [Server-Side Rendering (SSR)](/guide/scaling-up/ssr), và nó cải thiện đáng kể các chỉ số Core Web Vitals như [Largest Contentful Paint (LCP)](https://web.dev/lcp/).

Trên nền mô hình này còn có các framework cấp cao hơn xây dựng bằng Vue, chẳng hạn [Nuxt](https://nuxt.com/), cho phép bạn phát triển ứng dụng fullstack bằng Vue và JavaScript.

## JAMStack / SSG {#jamstack-ssg}

Server-side rendering có thể được thực hiện trước thời điểm phục vụ nếu dữ liệu cần thiết là tĩnh. Điều này có nghĩa là ta có thể render sẵn toàn bộ ứng dụng thành HTML và phục vụ chúng dưới dạng file tĩnh. Cách này cải thiện hiệu năng trang web và làm cho việc triển khai đơn giản hơn rất nhiều vì ta không còn cần render động từng trang ở mỗi request. Vue vẫn có thể hydrate những ứng dụng như vậy để cung cấp mức độ tương tác phong phú ở phía client. Kỹ thuật này thường được gọi là Static-Site Generation (SSG), còn được biết đến với tên [JAMStack](https://jamstack.org/what-is-jamstack/).

Có hai kiểu SSG: single-page và multi-page. Cả hai đều render sẵn trang web thành HTML tĩnh; điểm khác nhau là:

- Sau lần tải trang đầu tiên, single-page SSG sẽ "hydrate" trang thành một SPA. Điều này đòi hỏi tải JavaScript và chi phí hydration cao hơn ở đầu vào, nhưng các lần điều hướng sau sẽ nhanh hơn vì chỉ cần cập nhật một phần nội dung trang thay vì tải lại cả trang.

- Multi-page SSG sẽ tải một trang mới ở mỗi lần điều hướng. Ưu điểm là nó có thể gửi lượng JavaScript tối thiểu, hoặc thậm chí không cần JavaScript nếu trang không yêu cầu tương tác. Một số framework multi-page SSG như [Astro](https://astro.build/) còn hỗ trợ "partial hydration", cho phép bạn dùng component Vue để tạo các "đảo" tương tác nằm bên trong HTML tĩnh.

Single-page SSG phù hợp hơn nếu bạn kỳ vọng mức độ tương tác đáng kể, phiên làm việc dài, hoặc có các phần tử / state cần được giữ lại xuyên suốt khi điều hướng. Nếu không, multi-page SSG thường sẽ là lựa chọn tốt hơn.

Nhóm Vue cũng duy trì một static-site generator tên là [VitePress](https://vitepress.dev/), chính là công cụ đang chạy trang web bạn đang đọc. VitePress hỗ trợ cả hai kiểu SSG. [Nuxt](https://nuxt.com/) cũng hỗ trợ SSG. Bạn thậm chí có thể kết hợp SSR và SSG cho các route khác nhau trong cùng một ứng dụng Nuxt.

## Vượt Ra Ngoài Web {#beyond-the-web}

Dù Vue chủ yếu được thiết kế để xây dựng ứng dụng web, nó hoàn toàn không chỉ giới hạn trong trình duyệt. Bạn có thể:

- Xây dựng ứng dụng desktop với [Electron](https://www.electronjs.org/) hoặc [Wails](https://wails.io)
- Xây dựng ứng dụng di động với [Ionic Vue](https://ionicframework.com/docs/vue/overview)
- Xây dựng cả ứng dụng desktop lẫn di động từ cùng một codebase với [Quasar](https://quasar.dev/) hoặc [Tauri](https://tauri.app)
- Xây dựng trải nghiệm WebGL 3D với [TresJS](https://tresjs.org/)
- Dùng [Custom Renderer API](/api/custom-renderer) của Vue để xây dựng renderer tùy chỉnh, ví dụ renderer cho [terminal](https://github.com/vue-terminal/vue-termui)
