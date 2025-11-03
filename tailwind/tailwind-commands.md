# 🌈 Tailwind CSS – Commands & Usage Guide

**Official Website:** <https://v3.tailwindcss.com>

---

## 🚀 Installation

Follow the official setup guide:  
👉 <https://v3.tailwindcss.com/docs/installation>

**Recommended VS Code Extension:**  
- 🧩 *Tailwind CSS IntelliSense* by Tailwind Labs

---

### 🧹 Prettier Integration

Install the Tailwind Prettier plugin (for automatic class sorting):

```bash
npm install -D prettier prettier-plugin-tailwindcss
```

**Add a config file** `.prettierrc` to your project root:

```json
{
  "plugins": ["prettier-plugin-tailwindcss"]
}
```

---

## 📖 Keep Docs Open While Working

Tailwind documentation is your best friend:  
<https://v3.tailwindcss.com/docs/preflight>

---

## 🎨 Styling Basics

Tailwind CSS classes follow the pattern:  
`component` - `style` - `intensity`  
(optional opacity with `/` suffix)

Example:  
`text-green-500` → color intensity 500  
`text-green-500/80` → 80% opacity  

---

### 🎨 Colors

Docs: <https://v3.tailwindcss.com/docs/customizing-colors>

Examples:

```html
<p class="text-green-500">text-green-500</p>
<p class="bg-indigo-400 text-white">bg-indigo-400</p>
```

💡 Color intensities range from 50 → 950.

---

## ✍️ Typography

| Property | Example |
|-----------|----------|
| Text color | `text-sky-300` |
| Alignment | `text-left`, `text-center`, `text-right` |
| Font size | `text-xs` → `text-9xl` |
| Font weight | `font-thin`, `font-bold`, `font-black` |
| Case | `uppercase`, `lowercase`, `capitalize` |
| Letter spacing | `tracking-tight`, `tracking-wider`, `tracking-[5px]` |

**Custom values (escape hatch):**

```html
<p class="text-[100px]">Custom text size</p>
```

---

## 📦 Box Model: Spacing, Borders, Display

**Margin / Padding**

```html
<div class="mt-4 mb-2 mx-6 p-4 py-2 px-8"></div>
```

**Spacing between elements**

```html
<div class="flex space-x-4">...</div>
<div class="grid space-y-3">...</div>
```

**Borders & Colors**

```html
<div class="border border-stone-300 border-l-4"></div>
```

**Display**

`block`, `inline`, `flex`, `grid`, `hidden`

---

## 📱 Responsive Design

Tailwind uses a **mobile-first** approach.

| Prefix | Min Width | CSS |
|--------|------------|-----|
| `sm:` | 640px | `@media (min-width: 640px)` |
| `md:` | 768px | `@media (min-width: 768px)` |
| `lg:` | 1024px | `@media (min-width: 1024px)` |
| `xl:` | 1280px | `@media (min-width: 1280px)` |
| `2xl:` | 1536px | `@media (min-width: 1536px)` |

Example:

```html
<p class="text-base md:text-xl lg:text-2xl">Responsive text</p>
```

---

## 🧱 Flexbox

Common pattern:

```html
<div class="flex items-center justify-between"></div>
```

---

## 🧩 CSS Grid

```html
<div class="grid grid-rows-3 grid-cols-2 gap-4"></div>
```

Custom row sizes:

```html
<div class="grid grid-rows-[auto_1fr_auto]"></div>
```

Height helpers:  
`h-full`, `h-screen`, `h-min`, `h-max`, `h-fit`, `h-1/4`

---

## 🔘 Buttons: States & Transitions

```html
<button
  class="rounded-full bg-yellow-500 px-4 py-3 font-semibold uppercase tracking-wide text-stone-800 
         transition-colors duration-300 hover:bg-yellow-300 focus:outline-none 
         focus:ring focus:ring-yellow-300 focus:ring-offset-2 active:bg-yellow-600 disabled:cursor-not-allowed">
  Click Me
</button>
```

**State prefixes:**  
`hover:`, `focus:`, `active:`, `disabled:`, `focus-visible:`

---

## 🧾 Forms

```html
<input
  type="text"
  class="w-full rounded-full border border-stone-200 px-4 py-2 text-sm transition-all duration-300 
         placeholder:text-stone-400 focus:outline-none focus:ring focus:ring-yellow-400 
         focus:ring-opacity-50 md:px-6 md:py-3"
/>
```

---

## ♻️ Reusing Styles with @apply

Define reusable classes in `index.css`:

```css
@layer components {
  .cool-input {
    @apply w-full rounded-full border border-stone-200 px-4 py-2 text-sm transition-all duration-300 
           placeholder:text-stone-400 focus:outline-none focus:ring focus:ring-yellow-400 
           focus:ring-opacity-50 md:px-6 md:py-3;
  }
}
```

Then use in React:

```jsx
<input type="text" className="cool-input" />
```

💡 Prefer React components for maintainability.

---

## ⚛️ Reusing Styles with React Components

```jsx
function Button({ children, disabled }) {
  return (
    <button
      disabled={disabled}
      className="rounded-full bg-yellow-500 px-4 py-3 font-semibold uppercase tracking-wide 
                 text-stone-800 transition-colors duration-300 hover:bg-yellow-300 
                 focus:outline-none focus:ring focus:ring-yellow-300 focus:ring-offset-2 
                 disabled:cursor-not-allowed"
    >
      {children}
    </button>
  );
}
export default Button;
```

Use it:

```jsx
<Button>Call to Action</Button>
```

---

## 📍 Positioning & Z-Index

Example: Centered overlay

```html
<div class="absolute inset-0 flex items-center justify-center bg-blue-300/20 backdrop-blur-sm">
  <p>Loading...</p>
</div>
```

Custom CSS example (using theme values):

```css
.my-bg {
  background: no-repeat linear-gradient(theme(colors.stone.700) 0 0);
}
```

---

## ⚙️ Tailwind Configuration

Docs: <https://v3.tailwindcss.com/docs/configuration>

Example `tailwind.config.js`:

```js
/** @type {import('tailwindcss').Config} */
export default {
  content: ["./index.html", "./src/**/*.{js,ts,jsx,tsx}"],
  theme: {
    fontFamily: {
      sans: ["Roboto", "sans-serif"],
      serif: ["Merriweather", "serif"],
    },
    extend: {
      fontSize: {
        big: ["4.8rem", { lineHeight: "2" }],
      },
    },
  },
  plugins: [],
};
```

---

## 🌒 Dark Mode

Enable in config:

```js
module.exports = {
  darkMode: 'class', // or 'media'
}
```

Usage:

```html
<div class="bg-white text-black dark:bg-gray-900 dark:text-white"></div>
```

---

## 🧩 Plugins & Extras

Useful official plugins:
- `@tailwindcss/forms`
- `@tailwindcss/typography`
- `@tailwindcss/aspect-ratio`
- `@tailwindcss/container-queries`

Install example:

```bash
npm install -D @tailwindcss/forms
```

Enable in config:

```js
plugins: [require('@tailwindcss/forms')],
```

---

## 🧠 Helpful Notes

- Use emojis as favicons → <https://css-tricks.com/emoji-as-a-favicon/>
- Design “mobile first” → scale up with `sm:`, `md:`, etc.
- Check out **Tailwind Play** for live testing: <https://play.tailwindcss.com>
