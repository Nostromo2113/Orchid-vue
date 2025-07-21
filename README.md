# Установка Vue 3 в Orchid

## 1. Установка Vue3
```bash
npm install vue@^3 @vitejs/plugin-vue
```

## 2. Создаем Vue компонент. Например TestComponent.vue
На все события обязательно вешать event.preventDefault()
```vue
<template>
  <button @click.prevent="handleClick">Click me</button>
</template>

<script setup>
function handleClick() {
  console.log('Clicked')
}
</script>
```

## 3. Stimulus контроллер
```js
import { Controller } from '@hotwired/stimulus'
import { createApp } from 'vue'

export default class extends Controller {
  static values = { component: String, props: Object }

  async connect() {
    const { default: Comp } = await import(`../vue/${this.componentValue}.vue`)
    this.app = createApp(Comp, this.propsValue).mount(this.element)
  }

  disconnect() {
    this.app?.unmount()
  }
}
```

## 4. Blade компонент для отображения Vue компонента
```blade
<div data-controller="vue"
     data-vue-component-value="UserCard"
     data-vue-props-value='@json($user)'>
</div>
```

## 5. Сборка Vite
```js
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import laravel from 'laravel-vite-plugin'

export default defineConfig({
  plugins: [
    laravel({ input: ['resources/js/app.js'] }),
    vue()
  ]
})
```

## 6. Монтирование Stimulus контроллера в app.js
```js
import { Application } from '@hotwired/stimulus'
import VueController from './controllers/vue_controller'

const app = Application.start()
app.register('vue', VueController)
```

## 7. Запуск сборки
```bash
npm run dev
```
