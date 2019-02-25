---
title: Đăng kí Component
type: guide
order: 101
---

> Nếu như chưa làm quen với component bạn có thể bắt đầu từ phần [Cơ bản về component](components.html).

## Tên Component

Component được đặt tên khi đăng kí. Ví dụ khi đăng kí ở cấp toàn cục, ta thường thấy cú pháp:

```js
Vue.component('my-component-name', { /* ... */ })
```

Tên của component là đối số đầu tiên của `Vue.component`.

Tên của component được đặt theo mục đích sử dụng. Khi sử dụng trực tiếp component trên DOM (trái ngược với trong chuỗi template hay [trong một file component](single-file-components.html)), chúng tôi khuyến khích tuân thủ theo [quy định của W3C](https://www.w3.org/TR/custom-elements/#concepts) để đặt tên cho các thẻ (viết thường và phải nối bằng gach ngang). Điều này sẽ giúp tránh khỏi nguy cơ xung đột với các HTML elements hiện tại cũng như trong tương lai.

Bạn có thể xem những khuyến cáo khác cho việc đặt tên một component tại [hướng dẫn](../style-guide/#Base-component-names-strongly-recommended).

### Quy tắc đặt tên

Bạn có hai lựa chọn khi đặt tên cho một component:

#### Dùng kebab-case

```js
Vue.component('my-component-name', { /* ... */ })
```

Khi định nghĩa một component theo kiểu kebab-case, bạn cũng phải sử dụng kebab-case khi tham chiếu đến element của nó, giống như `<my-component-name>`.

#### Dùng PascalCase

```js
Vue.component('MyComponentName', { /* ... */ })
```

Khi định nghĩa một component với kiểu PascalCase, có thể sử dụng cả hai cách để tạo tham chiếu tới element của nó. Điều này có nghĩa là cả hai cách viết `<my-component-name>` và `<MyComponentName>` đều được chấp nhận. Tuy nhiên, cần chú ý là chỉ có những tên của component với kebab-case mới sử dụng được trên DOM (ví dụ trên file templates)

## Đăng kí component toàn cục

Cho tới nay, chúng ta mới tạo ra những component sử dụng cú pháp `Vue.component`:

```js
Vue.component('my-component-name', {
  // ... các tuỳ biến ...
})
```

Những component này được **đăng kí ở cấp toàn cục**. Điều này có nghĩa là sau khi đăng kí, một component có thể sử dụng trong template của bất cứ đối tượng Vue gốc (`new Vue`) nào. Ví dụ:

```js
Vue.component('component-a', { /* ... */ })
Vue.component('component-b', { /* ... */ })
Vue.component('component-c', { /* ... */ })

new Vue({ el: '#app' })
```

```html
<div id="app">
  <component-a></component-a>
  <component-b></component-b>
  <component-c></component-c>
</div>
```

Điều này còn áp dụng cho tất cả những components con, tức là tất cả ba component trên đều có thể sử dụng _lẫn nhau_.

## Đăng kí ở cấp cục bộ

Việc đăng kí component toàn cục thường không phải là một biện pháp lí tưởng. Ví dụ, nếu bạn sử dụng hệ thống xây dựng hệ thống như Webpack, đăng kí toàn cục cho tất cả các component có nghĩa là kể cả bạn không sử dụng một component nữa, nó vẫn có thể được thêm vào bản build cuối cùng. Nó không cần thiết và làm gia tăng khối lượng của JavaScript mà người dùng cần phải tải xuống.

Trong những trường hợp này, bạn có thể định nghĩa component như là những JavaScript objects:

```js
var ComponentA = { /* ... */ }
var ComponentB = { /* ... */ }
var ComponentC = { /* ... */ }
```

Sau khi định nghĩa các component, bạn có thể sử dụng trong tuỳ chọn `components`:

```js
new Vue({
  el: '#app'
  components: {
    'component-a': ComponentA,
    'component-b': ComponentB
  }
})
```

Cho mỗi thuộc tính trong đối tượng component, key sẽ là tên cuả custom element, trong khi giá trị sẽ bao gồm tuỳ chọn của đối tượng cho component.

Chú ý rằng **component được đăng kí cục bộ sẽ _không_ sẵn sàng để sử dụng trong các subcomponents**. Ví dụ, nếu như bạn muốn sử dụng `ComponentA` trong `ComponentB`, bạn phải sử dụng như sau:

```js
var ComponentA = { /* ... */ }

var ComponentB = {
  components: {
    'component-a': ComponentA
  },
  // ...
}
```

Hoặc nếu bạn sử dụng ES2015 modules, như là Babel hay Webpack, cú pháp có thể giống như sau:

```js
import ComponentA from './ComponentA.vue'

export default {
  components: {
    ComponentA
  },
  // ...
}
```

Chú ý rằng trong ES2015+, đặt tên biến như `ComponentA` bên trong một object là viết tắt của `ComponentA: ComponentA`, trường hợp này tên của biến đồng thời là:

- Tên của custom element để sử dụng trong template, và
- Tên của biến bao gồm các tuỳ chọn của component

## Hệ thống Module

Nếu bạn không sử dụng module systems với `import`/`require`, bạn có thể bỏ qua mục này, nếu bạn có sử dụng thì chúng tôi có một vài hướng dẫn và lời khuyên cho bạn

### Đăng kí ở cấp cục bộ trong hệ thống Module 

Nếu bạn sử dụng module system, giống như là Babel và Webpack. Trong trường hợp này, chúng tôi khuyến khích tạo ra môt `components`, với mỗi component có một file riêng.

Và bạn sẽ cần phải import từng component mà bạn muốn sử dụng, trước khi bạn đăng kí cục bộ. Ví dụ, giả thuyết trong file `ComponentB.js` hoặc `ComponentB.vue`:

```js
import ComponentA from './ComponentA'
import ComponentC from './ComponentC'

export default {
  components: {
    ComponentA,
    ComponentC
  },
  // ...
}
```

Cả hai `ComponentA` và `ComponentC` có thể sử dụng trong template của `ComponentB`.

### Tự động đăng kí toàn cục của những base component

Rất nhiều component sẽ được sử dụng chung, có thể chỉ là bao gồm một element như là một input hay một nút. CHúng ta đôi khi gọi chúng nhưng là một [base components](../style-guide/#Base-component-names-strongly-recommended) và chúng sẽ được sử dụng thường xuyên giữa các component.

Kết quả là có nhiều component có thể bao gồm một danh sách dài các base components:

```js
import BaseButton from './BaseButton.vue'
import BaseIcon from './BaseIcon.vue'
import BaseInput from './BaseInput.vue'

export default {
  components: {
    BaseButton,
    BaseIcon,
    BaseInput
  }
}
```

Chỉ để hỗ trợ cho một vài markup trong template:

```html
<BaseInput 
    v-model='searchText' 
    @keydown.enter='search' />
<BaseButton @click='search'>
  <BaseIcon name='search' />
</BaseButton>
```

May thay, nếu bạn đang sử dụng Webpack (hoặc [Vue CLI 3+](https://github.com/vuejs/vue-cli), công cụ sử dụng Webpack), bạn có thể sử dụng `require.context` cho đăng kí toàn cục với những base component phổ biến. Một ví dụ cho thấy cách bạn có thể dùng để sử dụng thêm vào các base component toàn cục trong file bắt đầu của ứng dụng ( ví dụ: `src/main.js`)

```js
import Vue from 'vue'
import upperFirst from 'lodash/upperFirst'
import camelCase from 'lodash/camelCase'

const requireComponent = require.context(
  // The relative path of the components folder
  './components',
  // Whether or not to look in subfolders
  false,
  // The regular expression used to match base component filenames
  /Base[A-Z]\w+\.(vue|js)$/
)

requireComponent.keys().forEach(fileName => {
  // Get component config
  const componentConfig = requireComponent(fileName)

  // Get PascalCase name of component
  const componentName = upperFirst(
    camelCase(
      // Strip the leading `'./` and extension from the filename
      fileName.replace(/^\.\/(.*)\.\w+$/, '$1')
    )
  )

  // Register component globally
  Vue.component(
    componentName,
    // Look for the component options on `.default`, which will
    // exist if the component was exported with `export default`,
    // otherwise fall back to module's root.
    componentConfig.default || componentConfig
  )
})
```

Nhớ rằng **bạn phải đăng kí component cấp toàn cục trước khi khởi tạo đối tượng Vue gốc (`new Vue`)**. [Đây là một ví dụ](https://github.com/chrisvfritz/vue-enterprise-boilerplate/blob/master/src/components/_globals.js) trong một ngữ cảnh thực tế.
