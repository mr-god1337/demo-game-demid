# DemidGame — Precision. Reforged.

Иммерсивный сайт премиального бренда кастомных игровых контроллеров. Тёмная кинематографичная сцена, скролл-управляемая 3D-модель, интерактивная студия кастомизации и геймифицированная воронка заказа.

## Стек

- **React 19** + **Vite 8**
- **React Three Fiber 9** + **@react-three/drei 10** + **three.js**
- **Tailwind CSS 4** (`@tailwindcss/vite`)
- Процедурные 3D-ассеты: геометрия геймпада, карбон, металл, гравировка, стикеры и glow-текстуры генерируются в canvas — внешние модели и HDR не нужны.

## Запуск

```bash
npm install
npm run dev
```

Сборка и локальный предпросмотр:

```bash
npm run build
npm run preview
```

> В этом окружении Node установлен портативно. Если `node` не найден в PATH:
>
> ```powershell
> $env:Path = "$env:LOCALAPPDATA\nodejs-portable\node-v24.22.1-win-x64;$env:Path"
> npm.cmd run dev
> ```

## Страницы

| Hash | Страница | Что внутри |
| --- | --- | --- |
| `#home` | Главная | Скролл-управляемый 3D-геймпад, hero-заголовок, манифест, фичи, 3D-превью студии, отзывы, CTA |
| `#studio` | Design Studio | 360°-орбита, zoom, клик по деталям, цвета, 4 финиша, гравировка, стикеры, XP, живая цена, черновик заказа |
| `#prices` | Цены | Тарифы CORE / PRO / LEGEND, конструктор цены, add-ons, FAQ |
| `#shipping` | Доставка | Маршруты, сроки, упаковка, FAQ |
| `#process` | Как это работает | Геймифицированный таймлайн из 6 этапов с XP и прогрессом |
| `#contacts` | Контакты | Валидируемая форма, email, телефон, Telegram, карточка менеджера Макса |

Переходы выполняются через hash-routing без полной перезагрузки страницы. При прямом открытии `/#studio` страница сразу монтируется в нужном состоянии.

## Кастомизация

### Контакты

Все контактные данные собраны в `src/lib/site.js` в объекте `CONTACTS`:

```js
export const CONTACTS = {
  email: 'hello@demidgame.com',
  phone: '+7 (999) 123-45-67',
  phoneHref: '+79991234567',
  telegram: '@demidgame',
  telegramUrl: 'https://t.me/demidgame',
  manager: 'Макс (Maks)',
  managerRole: 'Основатель · личный менеджер',
  hours: 'Пн–Сб · 10:00–21:00 МСК',
}
```

> Это демо-заглушки. Замените их перед публикацией.

### Конфигуратор

- `src/lib/gamepad.js` — палитра, финиши, детали, пресеты, стикеры, базовые цены и формула `calcPrice`.
- `src/components/three/Gamepad.jsx` — процедурная модель, кликабельные группы деталей, материалы и декали.
- `src/components/three/textures.js` — canvas-текстуры: карбон, брашированный металл, брендинг, гравировка, стикеры.
- `src/pages/Studio.jsx` — состояние конфигурации, XP/уровни, достижения, панель редактирования, модальное окно заказа.

Материалы обновляются в реальном времени: изменение цвета/финиша в панели сразу меняет `meshPhysicalMaterial` на выбранной детали.

### Визуальный стиль

Токены темы и утилиты (glassmorphism, neon glow, reveal-анимации, прелоадер, таймлайн, скроллбар) находятся в `src/index.css` в блоках `@theme` и `@layer utilities`.

## Структура

```text
src/
  App.jsx                  # hash-routing, preloader, layout shell
  components/
    Navbar.jsx             # навигация, мобильное меню, scroll-progress
    Footer.jsx             # футер, контакты, подписка
    Preloader.jsx          # 3D-анимация логотипа и прогресс загрузки
    Toast.jsx              # уведомления и достижения
    UI.jsx                 # PageHero, SectionHeading, Accordion, Marquee
    three/
      Gamepad.jsx          # 3D-контроллер
      HeroScene.jsx        # скролл-сцена главной
      StudioScene.jsx      # сцена кастомизатора + OrbitControls
      ShowcaseScene.jsx    # 3D-превью на главной
      SceneParts.jsx       # свет, environment, glow, камеры
      textures.js          # canvas-текстуры
  lib/
    gamepad.js             # данные и цены конфигуратора
    scroll.js              # scroll/pointer state без ререндеров React
    site.js                # навигация и контакты
  pages/                   # Home, Studio, Prices, Shipping, Process, Contacts
```

## Производительность и доступность

- 3D-сцены используют `dpr`-лимиты, `ContactShadows` и лёгкий `Environment` на Lightformers.
- Второстепенные 3D-сцены монтируются только при попадании во viewport (`IntersectionObserver`).
- Прелоадер имеет предохранитель по времени и кнопку Skip; прогресс не зависит от `requestAnimationFrame` в фоновой вкладке.
- Учитывается `prefers-reduced-motion`; у интерактивных элементов есть aria-атрибуты и focus-visible.
- Формы валидируются на клиенте; отправка контактов сейчас симулируется — подключите свой API/Formspree/Telegram-бот в `src/pages/Contacts.jsx` (функция `submit`).

## Деплой

Сайт статический: после `npm run build` опубликуйте содержимое `dist/`. Для SPA-хостинга hash-routing не требует rewrite-правил.
