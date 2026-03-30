# Slot {#slots}

Ngoài việc truyền dữ liệu qua props, component cha cũng có thể truyền các đoạn template xuống con qua **slot**:

<div class="sfc">

```vue-html
<ChildComp>
  Đây là một số nội dung slot!
</ChildComp>
```

</div>
<div class="html">

```vue-html
<child-comp>
  Đây là một số nội dung slot!
</child-comp>
```

</div>

Trong component con, nó có thể render nội dung slot từ cha bằng cách dùng element `<slot>` làm outlet:

<div class="sfc">

```vue-html
<!-- trong template con -->
<slot/>
```

</div>
<div class="html">

```vue-html
<!-- trong template con -->
<slot></slot>
```

</div>

Nội dung bên trong outlet `<slot>` sẽ được coi là nội dung "dự phòng": nó sẽ được hiển thị nếu cha không truyền bất kỳ nội dung slot nào:

```vue-html
<slot>Nội dung dự phòng</slot>
```

Hiện tại chúng ta không truyền bất kỳ nội dung slot nào cho `<ChildComp>`, vì vậy bạn sẽ thấy nội dung dự phòng. Hãy cung cấp một số nội dung slot cho con trong khi tận dụng state `msg` của cha.
