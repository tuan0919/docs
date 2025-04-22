# Tạo dự án Vue {#creating-a-vue-application}

## Application instance {#the-application-instance}

Mỗi dự án vue đều bắt đầu với việc tạo một **application instance** mới với hàm [`createApp`](/api/application#createapp):

```js
import { createApp } from 'vue'

const app = createApp({
  /* root component options */
})
```

## Root Component {#the-root-component}

Object được truyền vào `createApp` bản chất là một component. Mỗi chương trình sẽ yêu cầu một "root component" mà có thể chứa các component khác như là con của nó.

Nếu bạn đang sử dụng Single-File Component, chúng ta thường sẽ import root component từ một file khác:

```js
import { createApp } from 'vue'
// import the root component App from a single-file component.
import App from './App.vue'

const app = createApp(App)
```

Mặc dù nhiều ví dụ trong bài hướng dẫn chỉ sử dụng một component duy nhất, đa số các ứng dụng thực tế sẽ có một tập hợp các component được tổ chức ở dạng cây và có thể tái sử dụng. Chẳng hạn, cấu trúc component dạng cây của một ứng dụng TodoList có thể trông như sau:

```
App (root component)
├─ TodoList
│  └─ TodoItem
│     ├─ TodoDeleteButton
│     └─ TodoEditButton
└─ TodoFooter
   ├─ TodoClearButton
   └─ TodoStatistics
```

Ở các phần sau của bài viết, chúng ta sẽ thảo luận làm thế nào để khai báo và tập hợp nhiều component lại với nhau. Trước khi làm vậy, chúng ta sẽ tập trung vào chuyện gì sẽ xảy ra bên trong một component đơn lẻ.

## Mounting the App {#mounting-the-app}

Một application instance sẽ không render bất cứ thứ gì cho đến khi hàm `.mount()` của nó được gọi. Nó trông chờ một tham số "container", có thể là một phần tử DOM hoặc một chuỗi selector string:

```html
<div id="app"></div>
```

```js
app.mount('#app')
```

Nội dung root component của chương trình sẽ được render bên trong phần tử contrainer. Bản thân phần tử container không được xem như là một phần của chương trình.

Hàm `.mount()` nên luôn được gọi mỗi khi toàn bộ cấu hình cũng như quá trình đăng ký asset trong chương trình được hoàn thành. Ngoài ra cũng cần lưu ý đến giá trị trả về của hàm này, không giống với các hàm dùng để đăng ký asset, giá trị trả về sẽ là bản thân instance root component thay vì application instance.

### In-DOM Root Component Template {#in-dom-root-component-template}

Template cho phần tử root thường là một phần nội dung của chính nó, nhưng chúng ta cũng có thể cung cấp một template riêng biệt bằng cách ghi trực tiếp vào mount container:

```html
<div id="app">
  <button @click="count++">{{ count }}</button>
</div>
```

```js
import { createApp } from 'vue'

const app = createApp({
  data() {
    return {
      count: 0
    }
  }
})

app.mount('#app')
```

Vue sẽ tự động dùng `innerHTML` của container như là template nếu root component không có tùy chọn `template` trước đó.

Template trong DOM (In-DOM templates) thường được sử dụng trong các ứng dụng [dùng Vue mà không qua bước build](/guide/quick-start.html#using-vue-from-cdn) (sử dụng Vue trực tiếp từ CDN). Chúng cũng có thể được sử dụng kết hợp với các framework phía server, trong đó template gốc có thể được tạo ra động bởi server.

## Cấu hình chương trình {#app-configurations}

Instance chương trình expose một đối tượng `.config` cho phép chúng ta cấu hình một vài tùy chọn ở cấp độ chương trình, chẳng hạn, khai báo một error handler cấp độ chương trình sẽ có nhiệm vụ bắt toàn bộ lỗi từ các component hậu duệ:

```js
app.config.errorHandler = (err) => {
  /* handle error */
}
```

Application instance cũng cung cấp một số hàm cho phép đăng ký các assets có phạm vi bên trong chương trình. Chẳng hạn, đăng ký một component:

```js
app.component('TodoDeleteButton', TodoDeleteButton)
```

Điều này sẽ khiến cho `TodoDeleteButton` khả dụng ở bất kì đâu trong ứng dụng của chúng ta. Chúng ta sẽ thảo luận về việc đăng ký component và các loại assets khác nhau ở các phần sau của bài hướng dẫn. Bạn có thể tra cứu toàn bộ danh sách của application instance APIs tại [API reference](/api/application).

Hãy đảm bảo áp dụng toàn bộ cấu hình chương trình trước khi mount chương trình!

## Multiple application instances {#multiple-application-instances}

Bạn không chỉ bị giới hạn trong việc chỉ có một application instance duy nhất trong cùng một trang. API `createApp` cho phép nhiều Vue application có thể cùng tồn tại bên trogn cùng một trang, mỗi instance sẽ có phạm vi cấu hình riêng cũng như global assets riêng.

```js
const app1 = createApp({
  /* ... */
})
app1.mount('#container-1')

const app2 = createApp({
  /* ... */
})
app2.mount('#container-2')
```

Nếu bạn đang sử dụng Vue để bổ sung tính năng cho HTML được render từ phía server, và chỉ cần Vue kiểm soát một vài phần cụ thể trên một trang lớn, thì không nên mount một instance Vue duy nhất cho toàn bộ trang.

Thay vào đó, hãy tạo nhiều instance nhỏ của Vue và mount chúng vào đúng những phần tử mà chúng chịu trách nhiệm.
