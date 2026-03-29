# Translation Checklist

Tài liệu này dùng để theo dõi tiến độ dịch thủ công cho repo `docs-vi`.

## Trạng thái

- `[ ]` chưa bắt đầu
- `[x]` đã dịch và đã review
- Khi đang làm dở, thêm hậu tố `(WIP)` vào cuối dòng checklist

## Workflow

1. Mỗi trang nên đi qua issue hoặc PR riêng để dễ review.
2. Không dịch hàng loạt rồi merge một lần lớn.
3. Bám theo cấu trúc và liên kết của `vuejs/docs`.
4. Khi upstream thay đổi, đồng bộ trước rồi cập nhật bản dịch theo diff.

## Quy ước dịch

- Giữ nguyên anchor gốc: `## Tiêu đề đã dịch {#original-anchor}`.
- Giữ nguyên marker của Vue docs, ví dụ `### <script setup> ** {#script-setup}`.
- Ưu tiên văn phong kỹ thuật rõ ràng, ngắn, dễ đọc.
- Tên API, directive, component, package, và code sample giữ nguyên tiếng Anh.
- Thuật ngữ cần được thống nhất trong [GLOSSARY.md](./GLOSSARY.md) trước khi dùng rộng rãi.
- Text ngoài nội dung markdown cũng cần dịch, đặc biệt là `.vitepress/config.ts`.
- Ảnh có chữ cần xử lý riêng; tạo file ảnh mới thay vì ghi đè asset gốc nếu cần biến thể tiếng Việt.

## Checklist 30 trang tiếp theo

- [x] `src/guide/scaling-up/sfc.md`
- [x] `src/guide/scaling-up/ssr.md`
- [x] `src/guide/scaling-up/state-management.md`
- [x] `src/guide/scaling-up/testing.md`
- [x] `src/guide/scaling-up/tooling.md`
- [x] `src/guide/typescript/composition-api.md`
- [x] `src/guide/typescript/options-api.md`
- [x] `src/guide/typescript/overview.md`
- [x] `src/api/application.md`
- [x] `src/api/built-in-components.md`
- [x] `src/api/built-in-directives.md`
- [x] `src/api/built-in-special-attributes.md`
- [x] `src/api/built-in-special-elements.md`
- [x] `src/api/compile-time-flags.md`
- [x] `src/api/component-instance.md`
- [x] `src/api/composition-api-dependency-injection.md`
- [x] `src/api/composition-api-helpers.md`
- [x] `src/api/composition-api-lifecycle.md`
- [x] `src/api/composition-api-setup.md`
- [x] `src/api/custom-elements.md`
- [x] `src/api/custom-renderer.md`
- [x] `src/api/general.md`
- [x] `src/api/index.md`
- [x] `src/api/options-composition.md`
- [x] `src/api/options-lifecycle.md`
- [x] `src/api/options-misc.md`
- [x] `src/api/options-rendering.md`
- [x] `src/api/options-state.md`
- [x] `src/api/reactivity-advanced.md`
- [x] `src/api/reactivity-core.md`

## Ưu tiên sprint đầu

- [x] `README.md`
- [x] `TRANSLATES.md`
- [x] `.vitepress/config.ts`
- [x] `src/index.md`
- [x] `src/guide/introduction.md`
- [x] `src/guide/quick-start.md`
- [x] `src/guide/essentials/application.md`
- [x] `src/guide/essentials/template-syntax.md`
- [x] `src/guide/essentials/class-and-style.md`
- [x] `src/guide/essentials/component-basics.md`
- [x] `src/guide/essentials/computed.md`
- [x] `src/guide/essentials/conditional.md`
- [x] `src/guide/essentials/event-handling.md`
- [x] `src/guide/essentials/forms.md`
- [x] `src/guide/essentials/lifecycle.md`
- [x] `src/guide/essentials/list.md`
- [x] `src/guide/essentials/reactivity-fundamentals.md`
- [x] `src/guide/essentials/template-refs.md`

## Repo meta

- [x] `README.md`
- [x] `TRANSLATES.md`
- [x] `.vitepress/config.ts`

## Landing and shared pages

- [x] `src/index.md`
- [x] `src/translations/index.md`
- [x] `src/glossary/index.md`
- [x] `src/error-reference/index.md`
- [x] `src/examples/index.md`

## Guide

- [x] `src/guide/best-practices/accessibility.md`
- [x] `src/guide/best-practices/performance.md`
- [x] `src/guide/best-practices/production-deployment.md`
- [x] `src/guide/best-practices/security.md`
- [x] `src/guide/built-ins/keep-alive.md`
- [x] `src/guide/built-ins/suspense.md`
- [x] `src/guide/built-ins/teleport.md`
- [x] `src/guide/built-ins/transition-group.md`
- [x] `src/guide/built-ins/transition.md`
- [x] `src/guide/components/async.md`
- [x] `src/guide/components/attrs.md`
- [x] `src/guide/components/events.md`
- [x] `src/guide/components/props.md`
- [x] `src/guide/components/provide-inject.md`
- [x] `src/guide/components/registration.md`
- [x] `src/guide/components/slots.md`
- [x] `src/guide/components/v-model.md`
- [x] `src/guide/essentials/application.md`
- [x] `src/guide/essentials/class-and-style.md`
- [x] `src/guide/essentials/component-basics.md`
- [x] `src/guide/essentials/computed.md`
- [x] `src/guide/essentials/conditional.md`
- [x] `src/guide/essentials/event-handling.md`
- [x] `src/guide/essentials/forms.md`
- [x] `src/guide/essentials/lifecycle.md`
- [x] `src/guide/essentials/list.md`
- [x] `src/guide/essentials/reactivity-fundamentals.md`
- [x] `src/guide/essentials/template-refs.md`
- [x] `src/guide/essentials/template-syntax.md`
- [x] `src/guide/essentials/watchers.md`
- [x] `src/guide/extras/animation.md`
- [x] `src/guide/extras/composition-api-faq.md`
- [x] `src/guide/extras/reactivity-in-depth.md`
- [x] `src/guide/extras/reactivity-transform.md`
- [x] `src/guide/extras/render-function.md`
- [x] `src/guide/extras/rendering-mechanism.md`
- [x] `src/guide/extras/ways-of-using-vue.md`
- [x] `src/guide/extras/web-components.md`
- [x] `src/guide/introduction.md`
- [x] `src/guide/quick-start.md`
- [x] `src/guide/reusability/composables.md`
- [x] `src/guide/reusability/custom-directives.md`
- [x] `src/guide/reusability/plugins.md`
- [x] `src/guide/scaling-up/routing.md`
- [x] `src/guide/scaling-up/sfc.md`
- [x] `src/guide/scaling-up/ssr.md`
- [x] `src/guide/scaling-up/state-management.md`
- [x] `src/guide/scaling-up/testing.md`
- [x] `src/guide/scaling-up/tooling.md`
- [x] `src/guide/typescript/composition-api.md`
- [x] `src/guide/typescript/options-api.md`
- [x] `src/guide/typescript/overview.md`

## API

- [x] `src/api/application.md`
- [x] `src/api/built-in-components.md`
- [x] `src/api/built-in-directives.md`
- [x] `src/api/built-in-special-attributes.md`
- [x] `src/api/built-in-special-elements.md`
- [x] `src/api/compile-time-flags.md`
- [x] `src/api/component-instance.md`
- [x] `src/api/composition-api-dependency-injection.md`
- [x] `src/api/composition-api-helpers.md`
- [x] `src/api/composition-api-lifecycle.md`
- [x] `src/api/composition-api-setup.md`
- [x] `src/api/custom-elements.md`
- [x] `src/api/custom-renderer.md`
- [x] `src/api/general.md`
- [x] `src/api/index.md`
- [x] `src/api/options-composition.md`
- [x] `src/api/options-lifecycle.md`
- [x] `src/api/options-misc.md`
- [x] `src/api/options-rendering.md`
- [x] `src/api/options-state.md`
- [x] `src/api/reactivity-advanced.md`
- [x] `src/api/reactivity-core.md`
- [x] `src/api/reactivity-utilities.md`
- [x] `src/api/render-function.md`
- [x] `src/api/sfc-css-features.md`
- [x] `src/api/sfc-script-setup.md`
- [x] `src/api/sfc-spec.md`
- [x] `src/api/ssr.md`
- [x] `src/api/utility-types.md`

## Tutorial

- [x] `src/tutorial/index.md`
- [x] `src/tutorial/src/step-1/description.md`
- [x] `src/tutorial/src/step-10/description.md`
- [x] `src/tutorial/src/step-11/description.md`
- [x] `src/tutorial/src/step-12/description.md`
- [x] `src/tutorial/src/step-13/description.md`
- [x] `src/tutorial/src/step-14/description.md`
- [x] `src/tutorial/src/step-15/description.md`
- [x] `src/tutorial/src/step-2/description.md`
- [x] `src/tutorial/src/step-3/description.md`
- [x] `src/tutorial/src/step-4/description.md`
- [x] `src/tutorial/src/step-5/description.md`
- [x] `src/tutorial/src/step-6/description.md`
- [x] `src/tutorial/src/step-7/description.md`
- [x] `src/tutorial/src/step-8/description.md`
- [x] `src/tutorial/src/step-9/description.md`

## About

- [ ] `src/about/coc.md`
- [ ] `src/about/community-guide.md`
- [ ] `src/about/faq.md`
- [ ] `src/about/privacy.md`
- [ ] `src/about/releases.md`
- [ ] `src/about/team.md`

## Ecosystem

- [ ] `src/ecosystem/newsletters.md`
- [ ] `src/ecosystem/themes.md`

## Style guide

- [ ] `src/style-guide/index.md`
- [ ] `src/style-guide/rules-essential.md`
- [ ] `src/style-guide/rules-recommended.md`
- [ ] `src/style-guide/rules-strongly-recommended.md`
- [ ] `src/style-guide/rules-use-with-caution.md`

## Sponsor and partners

- [ ] `src/sponsor/index.md`
- [ ] `src/partners/[partnerId].md`
- [ ] `src/partners/all.md`
- [ ] `src/partners/index.md`

## Examples descriptions

- [ ] `src/examples/src/attribute-bindings/description.txt`
- [ ] `src/examples/src/cells/description.txt`
- [ ] `src/examples/src/circle-drawer/description.txt`
- [ ] `src/examples/src/conditionals-and-loops/description.txt`
- [ ] `src/examples/src/counter/description.txt`
- [ ] `src/examples/src/crud/description.txt`
- [ ] `src/examples/src/fetching-data/description.txt`
- [ ] `src/examples/src/flight-booker/description.txt`
- [ ] `src/examples/src/form-bindings/description.txt`
- [ ] `src/examples/src/grid/description.txt`
- [ ] `src/examples/src/handling-input/description.txt`
- [ ] `src/examples/src/hello-world/description.txt`
- [ ] `src/examples/src/list-transition/description.txt`
- [ ] `src/examples/src/markdown/description.txt`
- [ ] `src/examples/src/modal/description.txt`
- [ ] `src/examples/src/simple-component/description.txt`
- [ ] `src/examples/src/svg/description.txt`
- [ ] `src/examples/src/temperature-converter/description.txt`
- [ ] `src/examples/src/timer/description.txt`
- [ ] `src/examples/src/tree/description.txt`
