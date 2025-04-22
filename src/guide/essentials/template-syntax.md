# Template Syntax {#template-syntax}

Vue sử dụng cú pháp template dựa trên HTML, cho phép bạn bind một cách khai báo một DOM đã được render đến dữ liệu của instance component phía dưới. Tất cả các template trong Vue đều là HTML hợp lệ về mặt cú pháp, có thể được parse bởi các trình duyệt và công cụ xử lý HTML tuân thủ tiêu chuẩn.

Bên dưới, Vue sẽ biên dịch các template thành các mã JavaScript được tối ưu hóa cao. Kết hợp với hệ thống phản ứng (reactivity system), Vue đủ khôn khéo để tự động xác định số lượng tối thiểu các component cần được render và áp dụng tối thiểu các thao tác cần sử dụng để thay đổi DOM mỗi khi trạng thái của ứng dụng thay đổi.

Nếu bạn đã quen thuộc với các khái niệm về Virtual DOM và thích sử dụng JavaScript thuần hơn, có thể [viết trực tiếp hàm render](/guide/extras/render-function) thay vì sử dụng template, thậm chí có thể dùng thêm JSX nếu muốn. Tuy nhiên, lưu ý rằng các hàm render sẽ không tận dụng được mức tối ưu hóa khi biên dịch như template.

## Text Interpolation {#text-interpolation}

Hình thức để bind dữ liệu cơ bản nhất là chèn nội dung văn bản (text interpolation) bằng cú pháp "Mustache" (hai dấu ngoặc kép):

```vue-html
<span>Message: {{ msg }}</span>
```

Cú pháp ngoặc kép đôi sẽ được thay thế bằng giá trị của thuộc tính msg từ instance của component tương ứng. Giá trị hiển thị cũng sẽ tự động cập nhật mỗi khi thuộc tính `msg` thay đổi.

## Raw HTML {#raw-html}

Ngoặc kép đôi diễn giải dữ liệu dưới dạng plain text, chứ không phải HTML. Để sử dụng một HTML thật sự, bạn cần sử dụng [chỉ thị `v-html`](/api/built-in-directives#v-html):

```vue-html
<p>Using text interpolation: {{ rawHtml }}</p>
<p>Using v-html directive: <span v-html="rawHtml"></span></p>
```

<script setup>
  const rawHtml = '<span style="color: red">This should be red.</span>'
</script>

<div class="demo">
  <p>Using text interpolation: {{ rawHtml }}</p>
  <p>Using v-html directive: <span v-html="rawHtml"></span></p>
</div>

Tại đây chúng ta bắt gặp một khái niệm mới. Thuộc tính `v-html` mà bạn thấy được gọi là một **chỉ thị** (directive). Các chỉ thị đều được đặt tiền tố `v-` để cho biết rằng đây là những thuộc tính đặc biệt do Vue cung cấp. Như bạn có thể đoán, chúng áp dụng các hành vi phản ứng đặc biệt lên DOM sau khi được render. Trong trường hợp này, chúng ta đang nói với Vue rằng: "hãy giữ nội dung HTML bên trong phần tử này luôn được cập nhật theo thuộc tính `rawHtml` của instance hiện tại."

Nội dung của `span` sẽ được thay thế bằng giá trị của thuộc tính `rawHtml`, được chèn dưới dạng HTML thuần - các dữ liệu được bind sẽ bị bỏ qua. Chú ý là bạn không thể sử dụng `v-html` để soạn thảo các phần template, bởi vì Vue không phải là một engine tạo template dựa trên chuỗi. Thay vào đó, component nên được xem là đang vị cơ bản cho việc tái sử dụng hay kết hợp UI.

:::warning Security Warning
Việc hiển thị HTML tùy ý một cách động trên trang web của bạn có thể rất nguy hiểm vì nó dễ dẫn đến [lỗ hổng XSS](https://en.wikipedia.org/wiki/Cross-site_scripting). Chỉ sử dụng `v-html` trên nội dung đáng tin cậy và **không bao giờ** sử dụng trên nội dung do người dùng cung cấp.
:::

## Attribute Bindings {#attribute-bindings}

Cú pháp Ngoặc kép đôi không thể được sử dụng bên trong các thuộc tính HTML. Thay vào đó, sử dụng [chỉ thị `v-bind`](/api/built-in-directives#v-bind):

```vue-html
<div v-bind:id="dynamicId"></div>
```

Chỉ thị `v-bind` sẽ yêu cầu Vue giữ cho thuộc tính `id` của phần tử đồng bộ với thuộc tính `dynamicId` của chính phần tử đó. Nếu giá trị được liên kết là `null` hoặc `undefined`, thì thuộc tính sẽ bị xóa khỏi phần tử được render.

### Shorthand {#shorthand}

Bởi vì `v-bind` được sử dụng phổ biến, cho nên nó có một cú pháp viết tắt như sau:

```vue-html
<div :id="dynamicId"></div>
```

Thuộc tính bắt đầu với `:` có thể trông khá là lạ đối với HTML thông thường, nhưng nó thực sự là một kí tự khả dụng để làm tên thuộc tính và tất cả các browser hỡ trợ Vue có thể parse nó một cách chính xác. Thêm vào đó, chúng sẽ không xuất hiện ở markup được render ra. Cú pháp viết tắt là tùy chọn, nhưng bạn sẽ bắt gặp nó khi bạn tìm hiểu thêm về nó sau.

> Trong phần còn lại của bài hướng dẫn, chúng ta sẽ sử dụng cú pháp viết tắt trong các đoạn code ví dụ, vì nó là cách dùng phổ biến nhất đối với Vue developer.

### Same-name Shorthand {#same-name-shorthand}

- Chỉ hỗ trợ cho phiên bản 3.4+

Nếu thuộc tính có cùng tên với giá trị JavaScript được đính kèm, cú pháp còn có thể viết gọn hơn nữa để bỏ qua giá trị thuộc tính:

```vue-html
<!-- same as :id="id" -->
<div :id></div>

<!-- this also works -->
<div v-bind:id></div>
```

Điều này tương tự như cú pháp viết tắt thuộc tính khi khai báo đối tượng trong JavaScript. Lưu ý đây là tính năng chỉ khả dụng trong Vue 3.4 trở lên.

### Boolean Attributes {#boolean-attributes}

[Thuộc tính Boolean](https://html.spec.whatwg.org/multipage/common-microsyntaxes.html#boolean-attributes) là các thuộc tính có thể chứa giá trị là true / false thông qua sự hiện diện của chúng trên một phần tử. Chẳng hạn, [`disabled`](https://developer.mozilla.org/en-US/docs/Web/HTML/Attributes/disabled) là một trong số các thuộc tính boolean phổ biến nhất được sử dụng rộng rãi.

`v-bind` sẽ hoạt động khác một chút trong trường hợp này:

```vue-html
<button :disabled="isButtonDisabled">Button</button>
```

Thuộc tính `disabled` sẽ được thêm vào nếu `isButtonDisabled` là [giá trị truthy](https://developer.mozilla.org/en-US/docs/Glossary/Truthy). Nó cũng sẽ được thêm vào nếu giá trị là một chuỗi rỗng, để duy trì tính nhất quán với `<button disabled="">`. Ngược lại nếu là [giá trị falsy](https://developer.mozilla.org/en-US/docs/Glossary/Falsy) thì thuộc tính sẽ bị loại bỏ.

### Dynamically Binding Multiple Attributes {#dynamically-binding-multiple-attributes}

Nếu bạn có một JavaScript object chứa nhiều thuộc tính như sau:

<div class="composition-api">

```js
const objectOfAttrs = {
  id: 'container',
  class: 'wrapper',
  style: 'background-color:green'
}
```

</div>
<div class="options-api">

```js
data() {
  return {
    objectOfAttrs: {
      id: 'container',
      class: 'wrapper'
    }
  }
}
```

</div>

Bạn có thể bind chúng vào một phần tử duy nhất bằng cách sử dụng `v-bind` mà không có tham số:

```vue-html
<div v-bind="objectOfAttrs"></div>
```

## Using JavaScript Expressions {#using-javascript-expressions}

Cho đến hiện tại chúng ta chỉ mới ràng buộc tới các thuộc tính khóa đơn giản cho template. Nhưng Vue thực tế hỗ trợ việc viết các biểu thức JavaScript vào bên trong các dữ liệu được ràng buộc:

```vue-html
{{ number + 1 }}

{{ ok ? 'YES' : 'NO' }}

{{ message.split('').reverse().join('') }}

<div :id="`list-${id}`"></div>
```

Những biểu thức này sẽ được xem như là mã JavaScript với phạm vi dữ liệu nằm bên trong component instance hiện tại.

Trong Vue template, biểu thức JavaScript có thể nằm tại các vị trí sau:

- Bên trong dấu ngoặc kép đôi.
- Là giá trị thuộc tính của bất kì chỉ thị Vue nào (các thuộc tính bắt đầu với `v-`)

### Expressions Only {#expressions-only}

Mỗi ràng buộc chỉ có thể chứa **một biểu thức duy nhất**. Biểu thức là một đoạn mã có thể được đánh giá thành một giá trị. Cách kiểm tra đơn giản là liệu nó có thể được sử dụng sau `return` hay không.

Vì thế, các trường hợp dưới đây sẽ **KHÔNG** hợp lệ:

```vue-html
<!-- this is a statement, not an expression: -->
{{ var a = 1 }}

<!-- flow control won't work either, use ternary expressions -->
{{ if (ok) { return message } }}
```

### Calling Functions {#calling-functions}

Có thể gọi phương thức component-exposed ra bên trong biểu thức ràng buộc:

```vue-html
<time :title="toTitleDate(date)" :datetime="date">
  {{ formatDate(date) }}
</time>
```

:::tip
Hàm được gọi trong một biểu thức ràng buộc sẽ bị gọi đến mỗi khi component đó được cập nhật, vì vậy nên chúng **không nên** có bất kì side effect nào, chẳng hạn như thay đổi dữ liệu hoặc tạo ra một tác vụ bất đồng bộ nào đó.
:::

### Restricted Globals Access {#restricted-globals-access}

Template expressions are sandboxed and only have access to a [restricted list of globals](https://github.com/vuejs/core/blob/main/packages/shared/src/globalsAllowList.ts#L3). The list exposes commonly used built-in globals such as `Math` and `Date`.

Globals not explicitly included in the list, for example user-attached properties on `window`, will not be accessible in template expressions. You can, however, explicitly define additional globals for all Vue expressions by adding them to [`app.config.globalProperties`](/api/application#app-config-globalproperties).

## Directives {#directives}

Directives are special attributes with the `v-` prefix. Vue provides a number of [built-in directives](/api/built-in-directives), including `v-html` and `v-bind` which we have introduced above.

Directive attribute values are expected to be single JavaScript expressions (with the exception of `v-for`, `v-on` and `v-slot`, which will be discussed in their respective sections later). A directive's job is to reactively apply updates to the DOM when the value of its expression changes. Take [`v-if`](/api/built-in-directives#v-if) as an example:

```vue-html
<p v-if="seen">Now you see me</p>
```

Here, the `v-if` directive would remove or insert the `<p>` element based on the truthiness of the value of the expression `seen`.

### Arguments {#arguments}

Some directives can take an "argument", denoted by a colon after the directive name. For example, the `v-bind` directive is used to reactively update an HTML attribute:

```vue-html
<a v-bind:href="url"> ... </a>

<!-- shorthand -->
<a :href="url"> ... </a>
```

Here, `href` is the argument, which tells the `v-bind` directive to bind the element's `href` attribute to the value of the expression `url`. In the shorthand, everything before the argument (i.e., `v-bind:`) is condensed into a single character, `:`.

Another example is the `v-on` directive, which listens to DOM events:

```vue-html
<a v-on:click="doSomething"> ... </a>

<!-- shorthand -->
<a @click="doSomething"> ... </a>
```

Here, the argument is the event name to listen to: `click`. `v-on` has a corresponding shorthand, namely the `@` character. We will talk about event handling in more detail too.

### Dynamic Arguments {#dynamic-arguments}

It is also possible to use a JavaScript expression in a directive argument by wrapping it with square brackets:

```vue-html
<!--
Note that there are some constraints to the argument expression,
as explained in the "Dynamic Argument Value Constraints" and "Dynamic Argument Syntax Constraints" sections below.
-->
<a v-bind:[attributeName]="url"> ... </a>

<!-- shorthand -->
<a :[attributeName]="url"> ... </a>
```

Here, `attributeName` will be dynamically evaluated as a JavaScript expression, and its evaluated value will be used as the final value for the argument. For example, if your component instance has a data property, `attributeName`, whose value is `"href"`, then this binding will be equivalent to `v-bind:href`.

Similarly, you can use dynamic arguments to bind a handler to a dynamic event name:

```vue-html
<a v-on:[eventName]="doSomething"> ... </a>

<!-- shorthand -->
<a @[eventName]="doSomething"> ... </a>
```

In this example, when `eventName`'s value is `"focus"`, `v-on:[eventName]` will be equivalent to `v-on:focus`.

#### Dynamic Argument Value Constraints {#dynamic-argument-value-constraints}

Dynamic arguments are expected to evaluate to a string, with the exception of `null`. The special value `null` can be used to explicitly remove the binding. Any other non-string value will trigger a warning.

#### Dynamic Argument Syntax Constraints {#dynamic-argument-syntax-constraints}

Dynamic argument expressions have some syntax constraints because certain characters, such as spaces and quotes, are invalid inside HTML attribute names. For example, the following is invalid:

```vue-html
<!-- This will trigger a compiler warning. -->
<a :['foo' + bar]="value"> ... </a>
```

If you need to pass a complex dynamic argument, it's probably better to use a [computed property](./computed), which we will cover shortly.

When using in-DOM templates (templates directly written in an HTML file), you should also avoid naming keys with uppercase characters, as browsers will coerce attribute names into lowercase:

```vue-html
<a :[someAttr]="value"> ... </a>
```

The above will be converted to `:[someattr]` in in-DOM templates. If your component has a `someAttr` property instead of `someattr`, your code won't work. Templates inside Single-File Components are **not** subject to this constraint.

### Modifiers {#modifiers}

Modifiers are special postfixes denoted by a dot, which indicate that a directive should be bound in some special way. For example, the `.prevent` modifier tells the `v-on` directive to call `event.preventDefault()` on the triggered event:

```vue-html
<form @submit.prevent="onSubmit">...</form>
```

You'll see other examples of modifiers later, [for `v-on`](./event-handling#event-modifiers) and [for `v-model`](./forms#modifiers), when we explore those features.

And finally, here's the full directive syntax visualized:

![directive syntax graph](./images/directive.png)

<!-- https://www.figma.com/file/BGWUknIrtY9HOmbmad0vFr/Directive -->
