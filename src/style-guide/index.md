---
outline: deep
---

# Hướng dẫn Phong cách {#style-guide}

::: warning Lưu ý
Hướng dẫn phong cách Vue.js này đã lỗi thời và cần được xem xét lại. Nếu bạn có câu hỏi hoặc gợi ý, vui lòng [mở issue](https://github.com/vuejs/docs/issues/new).
:::

Đây là hướng dẫn phong cách chính thức cho code đặc thù Vue. Nếu bạn dùng Vue trong dự án, đây là tài liệu tham khảo hữu ích để tránh lỗi, tranh luận không cần thiết và các anti-pattern. Tuy nhiên, chúng tôi không cho rằng bất kỳ hướng dẫn phong cách nào là lý tưởng cho mọi nhóm hay dự án, vì vậy hãy tự điều chỉnh có chủ ý dựa trên kinh nghiệm thực tế, tech stack hiện tại và giá trị cá nhân.

Phần lớn, chúng tôi cũng tránh đề xuất về JavaScript hay HTML nói chung. Chúng tôi không quan tâm bạn có dùng dấu chấm phẩy hay trailing comma hay không. Chúng tôi cũng không quan tâm HTML của bạn dùng nháy đơn hay nháy đôi cho giá trị thuộc tính. Tuy nhiên sẽ có một số ngoại lệ khi chúng tôi thấy một pattern cụ thể hữu ích trong ngữ cảnh Vue.

Cuối cùng, chúng tôi chia các quy tắc thành bốn danh mục:

## Danh mục Quy tắc {#rule-categories}

### Ưu tiên A: Thiết yếu (Ngăn ngừa Lỗi) {#priority-a-essential-error-prevention}

Các quy tắc này giúp ngăn ngừa lỗi, vì vậy hãy học và tuân thủ chúng bằng mọi giá. Có thể có ngoại lệ, nhưng phải rất hiếm và chỉ được thực hiện bởi những người có kiến thức chuyên sâu về cả JavaScript và Vue.

- [Xem tất cả quy tắc ưu tiên A](./rules-essential)

### Ưu tiên B: Rất Khuyến nghị {#priority-b-strongly-recommended}

Các quy tắc này được phát hiện là cải thiện khả năng đọc và/hoặc trải nghiệm phát triển trong hầu hết các dự án. Code của bạn vẫn chạy nếu vi phạm chúng, nhưng vi phạm phải hiếm và có lý do chính đáng.

- [Xem tất cả quy tắc ưu tiên B](./rules-strongly-recommended)

### Ưu tiên C: Khuyến nghị {#priority-c-recommended}

Khi có nhiều lựa chọn tốt như nhau, có thể chọn tùy ý để đảm bảo nhất quán. Trong các quy tắc này, chúng tôi mô tả từng lựa chọn chấp nhận được và đề xuất mặc định. Điều đó có nghĩa là bạn hoàn toàn có thể chọn khác trong codebase của mình, miễn là nhất quán và có lý do chính đáng. Hãy thực sự có lý do! Khi thích nghi với tiêu chuẩn cộng đồng, bạn sẽ:

1. Rèn não để phân tích phần lớn code cộng đồng dễ dàng hơn
2. Có thể sao chép dán hầu hết ví dụ code của cộng đồng mà không cần sửa đổi
3. Thường thấy nhân viên mới đã quen với phong cách code bạn ưa thích, ít nhất liên quan đến Vue

- [Xem tất cả quy tắc ưu tiên C](./rules-recommended)

### Ưu tiên D: Dùng Cẩn thận {#priority-d-use-with-caution}

Một số tính năng của Vue tồn tại để hỗ trợ các trường hợp ngoại lệ hiếm gặp hoặc migration mượt mà hơn từ codebase cũ. Tuy nhiên khi lạm dụng, chúng có thể làm code khó bảo trì hơn hoặc thậm chí trở thành nguồn gốc của lỗi. Các quy tắc này làm nổi bật những tính năng tiềm ẩn rủi ro, mô tả khi nào và tại sao nên tránh chúng.

- [Xem tất cả quy tắc ưu tiên D](./rules-use-with-caution)
