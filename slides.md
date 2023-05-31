---
# try also 'default' to start simple
theme: default
# https://sli.dev/custom/highlighters.html
highlighter: shiki
# show line numbers in code blocks
lineNumbers: false

colorSchema: light
---

<div class="mb-3">
  <img src="/images/vue-logo.svg" class="w-24" />
</div>

# Vue 3 Composition API

Options va composition API farqlari. Composition API afzalliklari.

<div class="uppercase text-sm tracking-widest">Webfazliddin</div> 

<!--
The last comment block of each slide will be treated as slide notes. It will be visible and editable in Presenter Mode along with the slide. [Read more in the docs](https://sli.dev/guide/syntax.html#notes)
-->

---
layout: two-cols
---

# Options api nima?

Options api Vue 2 da qo'llaniladigan asosiy kod yozish stili

Dasturchilar Vue komponentga bir qancha option'larni berish orqali veb dasturlarni yaratishlari mumkin.

```js{3,7,8,15|all}
export default {
  name: 'Counter',
  data() {
    return {
      count: 0
    }
  },
  methods: {
    increment() {
      this.count++;
    },
    decrement() {
      this.count--;
    }
  }
}
```

::right::

<div class="h-full flex items-center justify-center">
  <Counter />
</div>
<!--
You can have `style` tag in markdown to override the style for the current page.
Learn more: https://sli.dev/guide/syntax#embedded-styles
-->

---

# Options API da code reuse qilish usullari

- Mixins
- Renderless components
- Filters

Filterlar Vue 3 da mavjud emas.

---

# Mixin'lar

Mixinlar Options API dagi asosiy "logic reuse" qilish usuli hisoblanadi. 

<div class="grid grid-cols-2 gap-x-2">
<div>

```js
const myMixin1 = {
  created: function () {
    this.hello();
  },
  methods: {
    hello: function () {
      console.log("hello from mixin 1!");
    },
  },
};
```

</div> 
<div>

```js
const myMixin2 = {
  created: function () {
    this.hello();
  },
  methods: {
    hello: function () {
      console.log("hello from mixin 2!");
    },
  },
};
```

</div>
</div>

<v-click>

```js
export default {
  name: 'MyComponent',
  mixins: [myMixin1, myMixin2]
};                              // hello from mixin 2!
```

</v-click>

---
layout: image-right
image: /images/mixin.webp
---

# Mixin'larning kamchiliklari

<v-clicks>

- Unclear source of properties
- Namespace collisions
- Implicit cross-mixin communication

</v-clicks>

---
layout: two-cols
class: "p-2"
---

# Renderless component'lar

```html
<FetchApi
  url="https://jsonplaceholder.typicode.com/todos/1"
  v-slot="{ isLoading, data }"
>
  <div v-if="isLoading">Loading...</div>
  <div>Todo: {{ data.title }}</div>
</FetchApi>
```

Renderless component'lar asosan template ga bog'langan bo'ladi. 

Renderless component o'z ichidagi bola componentga slot orqali reactive state ni yuboradi.

Renderless componentning vazifasi hech narsani render qilmasdan ma'lum bir vazifani bajarish va kerakli reactive ma'lumotlarni bola elementlarga berish.

::right::

<div class="pt-18">
  <FetchApi 
    url="https://jsonplaceholder.typicode.com/todos/1" 
    v-slot="{ isLoading, data }">
    <div v-if="isLoading">Loading...</div>
    <div v-else>
      Todo: {{ data.title }}
    </div>
  </FetchApi>
</div>

---

# Composition API nima?

Composition API - componentda option'larni yozish o'rniga mavjud funksiyalarni import qilish va ulardan foydalanish. Bu funksiyalar composable'lar deyiladi.

Composition API quyidagilardan iborat:

- **Reactivity API:** `ref()`, `reactive()`, `computed()`, `watch()` va boshqa funksiyalar orqali reactive state hosil qilish va uni boshqarish.
- **Lifecycle hooks:** `onMounted()`, `onUnmounted()` va boshqa funksiyalar orqali Vue componentning lifecycle hooklaridan foydalanish.
- **Dependency injection:** `provide()`, `inject()`

---

# Composition API ga misol. setup option

```js{4,11|5|7-8|10|all}
import { ref } from "vue";

export default {
  setup() {
    const count = ref(0);

    const increment = () => count.value++;
    const decrement = () => count.value--;

    return { count, increment, decrement };
  },
};
```

<div v-click="5">

```html
<template>
  <div>
    <button @click="increment">-</button>
    <span>{{ count }}</span>
    <button @click="decrement">+</button>
  </div>
</template>
```

</div>

---

# script setup

<div class="grid grid-cols-2 gap-x-2">

```html
<script>
import { ref } from "vue";

export default {
  setup() {
    const count = ref(0);

    const increment = () => count.value++;
    const decrement = () => count.value--;

    return { count, increment, decrement };
  },
};
</script>
```

```html
<script setup>
import { ref } from "vue";

const count = ref(0);

const increment = () => count.value++;
const decrement = () => count.value--;
</script>
```

</div>

---
class: space-y-2
---

# ref

- primitiv o'zgaruvchilarni reaktiv qilish mumkin.
- obyektlarni ham reaktiv qilish mumkin.
- reassign qilish imkoniyati
- `.value` qilib ishlatish kerak

```js
const count = ref(1);

count.value = 10;
```
<v-click>

template'da `.value` shart emas.

```html
<template>
  <div>
    {{ count }}
  </div>
</template>
```

</v-click>

---
class: space-y-2
---

# reactive

- Obyektlarni reaktiv qilish mumkin.
- `.value` qilish shart emas.
- reassign qilib bo'lmaydi.

```js
const user = reactive({
  name: 'Ali',
  age: 30,
  gender: 'male'
});

user.name = 'Bilol';
user.age = 35;
```

---

# ref vs reactive

<div class="grid grid-cols-2 gap-x-2 mt-4">

<div>

```js
const user = ref({
  name: 'Ali',
  age: 25
});

user.value.age = 30;

user.value = {
  name: 'Vali',
  age: 32
}; // reactive
```

</div>

<div>

```js
const user = reactive({
  name: 'Ali',
  age: 25
});

user.age = 30;

user = {
  name: 'Vali',
  age: 32
};  // reactive emas
```

</div>

</div>


<div v-click class="my-3">Quyidagi savollar tug'ilishi mumkin:</div>


<v-clicks>

- Nega ref va reactive? Bitta refni o'zi yetarli emasmi?
- Nega ref ni `.value` qilib ishlatamiz, lekin reactive da to'g'ridan-to'g'ri ishlataveramiz?
- Bu ikkalasining farqi nimada va qachon qay birini ishlatish kerak?

</v-clicks>

---
layout: two-cols
class: px-2
---

# watch ref bilan

```js
import { ref, watch } from 'vue';

const page = ref(1);

watch(page, (value) => {
  console.log('Page: ', value);
});
```

refni watchga to'g'ridan to'g'ri berish mumkin.

::right::

# watch reactive bilan

```js
import { reactive, watch } from 'vue';

const pagination = reactive({
  page: 1,
  limit: 10
});

watch(() => pagination.page, (value) => {
  console.log('Page: ', value);
})
```

reactive'ni ichidagi property'ni to'g'ridan to'g'ri berib bo'lmaydi watch'ga

---

# computed

```js
import { reactive, computed } from 'vue';

const user = reactive({
  firstName: 'Ali',
  lastName: 'Valiyev'
});

const fullName = computed(() => {
  return user.firstName + ' ' + user.lastName;
});
```

```html
<template>
  <div>
    {{ fullName }}
  </div>
</template>
```

---

# Lifecycle hooks

```js
import { onDeactivated } from 'vue';

export const useInterval = (callback, interval) => {
  const intervalId = setInterval(callback, interval);

  onDeactivated(() => {
    clearInterval(intervalId);
  });
};
```

<v-click>

```js
import { useInterval } from '@/composables/useInterval';

useInterval(() => {
  console.log(new Date());
}, 1000);
```

</v-click>

---

# Nega composition API?

- Composition API da code reuse qilish yaxshiroq.
- Composition APIda mixin'lardagi barcha muammolar hal qilingan.
- Kodni strukturalash qulay.
- Typescript support yaxshi.
- script setup ishlatilganda build hajmi kamroq bo'ladi.

---

# Composition vs Options API

<img src="https://vuejs.org/assets/composition-api-after.e3f2c350.png" class="h-full object-cover" />


---
layout: center
class: text-center
---

<div class="flex justify-center mb-4">
  <img 
    src="https://d33wubrfki0l68.cloudfront.net/a5780e53fee68ddd1cd73a00484151d2d052cb4d/b7469/logo-vertical.png" 
    alt="Pinia" 
    class="w-24" 
  />
</div> 

# VueUse - tayyor composable funksiyalar

https://vueuse.org/

---
layout: center
class: text-center space-y-2
---

<div class="flex justify-center mb-4">
  <img src="https://pinia.vuejs.org/logo.svg" alt="Pinia" class="w-24" />
</div> 

# Pinia

The Vue Store that you will enjoy using

https://pinia.vuejs.org

---
layout: center
class: text-center
---

<div class="flex justify-center mb-4">
  <img src="https://www.naiveui.com/assets/naivelogo.93278402.svg" alt="Naive UI" class="w-24" />
</div>

# Naive UI - Vue 3 component library
https://www.naiveui.com/

---
layout: center
class: text-center
---
<div class="flex justify-center mb-4">
  <img src="https://d33wubrfki0l68.cloudfront.net/9a47dde680cca08e326c07824009ed1adc29626e/6a1c0/logo-title.png" class="w-24" />
</div>

# Slidev - Presentation Slides for Developers
https://sli.dev

Ushby slayd Slidev yordamida yasalgan :)

---
layout: center
class: text-center
---

# E'tiboringiz uchun rahmat!

Endi live coding.
