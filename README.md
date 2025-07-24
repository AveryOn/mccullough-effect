# mccullough-effect

Простое Vue 3 приложение для запуска эффекта Маккалфа — чередующиеся горизонтальные и вертикальные цветные полосы, вызывающие стойкое пост-цветовое восприятие (afterimage).

## 🚀 Функции

- ⏱ Таймер с настраиваемыми минутами и секундами
- 🔁 Переключение между горизонтальными и вертикальными полосами по интервалу
- ✅ Возможность включить "check" режим для проверки эффекта
- ♻ Динамическое обновление периода переключения
- ♾ Бесконечный режим (∞:∞), если не указано время

## 🛠 Стек

- Vue 3 (Composition API)
- TypeScript
- moment.js

## 🧠 Предупреждение

> Эффект Маккалфа может вызывать временные изменения в цветовом восприятии, особенно при длительном просмотре (больше 5 минут). Используй на свой страх и риск. Не уводи глаза резко. Не включай в тёмной комнате. Не залипай 30 минут как автор.

## 🧾 Запуск

This template should help get you started developing with Vue 3 in Vite.

## Recommended IDE Setup

[VSCode](https://code.visualstudio.com/) + [Volar](https://marketplace.visualstudio.com/items?itemName=Vue.volar) (and disable Vetur).

## Type Support for `.vue` Imports in TS

TypeScript cannot handle type information for `.vue` imports by default, so we replace the `tsc` CLI with `vue-tsc` for type checking. In editors, we need [Volar](https://marketplace.visualstudio.com/items?itemName=Vue.volar) to make the TypeScript language service aware of `.vue` types.

## Customize configuration

See [Vite Configuration Reference](https://vite.dev/config/).

## Project Setup

```sh
npm install
```

### Compile and Hot-Reload for Development

```sh
npm run dev
```

### Type-Check, Compile and Minify for Production

```sh
npm run build
```

### Lint with [ESLint](https://eslint.org/)

```sh
npm run lint
```
