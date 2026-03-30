---
outline: deep
---

<script setup>
import { ref, onMounted } from 'vue'

const version = ref()

onMounted(async () => {
  const res = await fetch('https://api.github.com/repos/vuejs/core/releases/latest')
  version.value = (await res.json()).name
})
</script>

# Các bản phát hành {#releases}

<p v-if="version">
Phiên bản ổn định mới nhất hiện tại của Vue là <strong>{{ version }}</strong>.
</p>
<p v-else>
Đang kiểm tra phiên bản mới nhất...
</p>

Changelog đầy đủ của các bản phát hành trước có sẵn trên [GitHub](https://github.com/vuejs/core/blob/main/CHANGELOG.md).

## Chu kỳ Phát hành {#release-cycle}

Vue không có chu kỳ phát hành cố định.

- Bản vá được phát hành khi cần thiết.

- Bản minor luôn chứa các tính năng mới, với khung thời gian điển hình là 3-6 tháng giữa các bản. Bản minor luôn trải qua giai đoạn beta pre-release.

- Bản major sẽ được thông báo trước, và sẽ trải qua giai đoạn thảo luận sớm và các giai đoạn alpha / beta pre-release.

## Các Trường hợp Ngoại lệ của Semantic Versioning {#semantic-versioning-edge-cases}

Các bản phát hành Vue tuân theo [Semantic Versioning](https://semver.org/) với một số trường hợp ngoại lệ.

### Định nghĩa TypeScript {#typescript-definitions}

Chúng tôi có thể phát hành các thay đổi không tương thích với định nghĩa TypeScript giữa các phiên bản **minor**. Điều này vì:

1. Đôi khi chính TypeScript phát hành các thay đổi không tương thích giữa các phiên bản minor, và chúng tôi có thể phải điều chỉnh các kiểu để hỗ trợ các phiên bản TypeScript mới hơn.

2. Đôi khi chúng tôi có thể cần áp dụng các tính năng chỉ có sẵn trong phiên bản TypeScript mới hơn, nâng cao phiên bản tối thiểu cần thiết của TypeScript.

Nếu bạn đang dùng TypeScript, bạn có thể dùng dải semver khóa minor hiện tại và nâng cấp thủ công khi phiên bản minor mới của Vue được phát hành.

### Tương thích Code Đã Biên dịch với Runtime Cũ {#compiled-code-compatibility-with-older-runtime}

Phiên bản **minor** mới hơn của Vue compiler có thể tạo ra code không tương thích với Vue runtime từ phiên bản minor cũ hơn. Ví dụ: code được tạo bởi Vue 3.2 compiler có thể không hoàn toàn tương thích nếu được sử dụng bởi runtime từ Vue 3.1.

Đây chỉ là mối lo ngại đối với tác giả thư viện, vì trong các ứng dụng, phiên bản compiler và phiên bản runtime luôn giống nhau. Mismatch phiên bản chỉ có thể xảy ra nếu bạn phát hành code Vue component đã biên dịch trước như một package, và người dùng sử dụng nó trong dự án dùng phiên bản Vue cũ hơn. Kết quả là, package của bạn có thể cần khai báo rõ ràng phiên bản minor tối thiểu cần thiết của Vue.

## Pre-release {#pre-releases}

Bản minor và major thường trải qua một loạt các giai đoạn pre-release: **alpha**, **beta**, và **release candidate (RC)**. Số lượng và loại pre-release phụ thuộc vào phạm vi thay đổi. Ví dụ: bản minor với ít cập nhật có thể chỉ có giai đoạn beta, trong khi bản major thường bao gồm cả ba giai đoạn để cho phép kiểm thử kỹ lưỡng và phản hồi từ cộng đồng.

Bạn có thể cài đặt các pre-release mới nhất từ npm bằng `npx install-vue@alpha`, `npx install-vue@beta`, hoặc `npx install-vue@rc`. Để kiểm thử các thay đổi chưa được bao gồm trong các pre-release đã gắn thẻ, mọi commit vào kho [vuejs/core](https://github.com/vuejs/core) được xuất bản như một preview continuous-release tạm thời, có thể cài đặt bằng `npx install-vue@edge`.

Pre-release dành cho kiểm thử tích hợp / ổn định, và cho những người dùng sớm để cung cấp phản hồi về các tính năng không ổn định. Không dùng pre-release trong production. Tất cả pre-release được coi là không ổn định và có thể có breaking change giữa các bản, vì vậy luôn ghim vào phiên bản chính xác khi dùng pre-release.

## Các tính năng Bị Deprecated {#deprecations}

Chúng tôi có thể định kỳ deprecated các tính năng có thay thế mới tốt hơn trong các bản minor. Các tính năng bị deprecated sẽ tiếp tục hoạt động, và sẽ bị xóa trong bản major tiếp theo sau khi nó bước vào trạng thái deprecated.

## RFC {#rfcs}

Các tính năng mới có bề mặt API đáng kể và các thay đổi lớn đối với Vue sẽ trải qua quy trình **Request for Comments** (RFC). Quy trình RFC nhằm cung cấp con đường nhất quán và có kiểm soát để các tính năng mới đi vào framework, và cho người dùng cơ hội tham gia và đóng góp phản hồi trong quá trình thiết kế.

Quy trình RFC được thực hiện trong kho [vuejs/rfcs](https://github.com/vuejs/rfcs) trên GitHub.

## Tính năng Thử nghiệm {#experimental-features}

Một số tính năng được phát hành và tài liệu hóa trong phiên bản ổn định của Vue, nhưng được đánh dấu là thử nghiệm. Tính năng thử nghiệm thường là các tính năng có thảo luận RFC liên quan với hầu hết các vấn đề thiết kế được giải quyết trên giấy tờ, nhưng vẫn thiếu phản hồi từ việc sử dụng thực tế.

Mục tiêu của các tính năng thử nghiệm là cho phép người dùng cung cấp phản hồi bằng cách kiểm thử chúng trong môi trường production, mà không cần dùng phiên bản Vue không ổn định. Bản thân các tính năng thử nghiệm được coi là không ổn định, và chỉ nên được dùng theo cách có kiểm soát, với kỳ vọng rằng tính năng có thể thay đổi giữa bất kỳ loại bản phát hành nào.
