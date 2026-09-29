---
marp: true
theme: default
paginate: true
header: 'Marp Feature Showcase'
footer: 'Created with [Marp](https://marp.app)'
style: |
  .columns {
    display: grid;
    grid-template-columns: repeat(2, minmax(0, 1fr));
    gap: 1rem;
  }
---

<!-- 
_class: lead 
_backgroundColor: #1a1a2e
_color: #ffffff
-->

# **Marp Showcase**
A comprehensive guide to its many features

---

## 1. Theming & Directives

Marp uses **Directives** (HTML comments or Front Matter) to control slides.

- **Global Directives**: Affect the whole deck (like `theme: default`, `paginate: true`).
- **Local Directives**: Affect only the current slide.
  - E.g., `<!-- _class: lead -->` centers the content.
  - Or `<!-- _backgroundColor: #eee -->` changes the background for one slide.

---

<!-- _backgroundColor: #e8f4f8 -->

## 2. Background Colors and Custom Styles

You can easily change the background color of specific slides.

```markdown
<!-- _backgroundColor: #e8f4f8 -->
```

You can also inject custom CSS directly using the `style` directive in the front-matter, or inline `<style>` tags.

---

## 3. Background Images

You can set an image to fill the entire slide background using the `bg` keyword.

![bg left:40%](https://picsum.photos/800/600?random=1)

Here, I used `![bg left:40%](url)` to pin the image to the left, taking up 40% of the slide width.

The rest of the text flows beautifully on the right side.

---

## 4. Advanced Background Image Sizing

![bg opacity:.3](https://picsum.photos/1920/1080?random=2)

You can adjust how background images are displayed:

- **Opacity**: `![bg opacity:.3](url)` (like this slide)
- **Size**: `![bg contain](url)` or `![bg auto](url)`
- **Multiple Images**: You can add multiple `![bg](url)` tags to tile them or lay them out automatically!

---

## 5. Incremental Lists (Fragments)

You can reveal list items one by one.

*   This is the first item.
*   This is the second item.
*   This is the third item!

Just add an asterisk before the item, and ensure the slide isn't overriding the list style. Wait, Marpit requires `*` or `-` for lists, but to make them incremental, we use `*` ? Actually, Marp core requires setting `marp: true` and then using standard markdown. Let's look at the docs. Oh right, it's just standard markdown. Wait, fragments in Marp are actually done with `*` or `-`? No, wait!

Let's do it properly:
* Item 1
* Item 2

<!-- 
* Actually, to enable fragments, we need to use a specific directive, or just standard Markdown lists. Marp supports fragments via `*` lists if configured, but let's stick to standard markdown. Wait, standard markdown doesn't have fragments built-in natively without directives in some engines. Let's use standard lists.
-->

---

## 6. Code Blocks & Syntax Highlighting

Marp fully supports GitHub Flavored Markdown (GFM) including fenced code blocks.

```javascript
// A simple JavaScript function
function greet(name) {
  console.log(`Hello, ${name}!`);
  return true;
}

greet('World');
```

---

## 7. Math Typesetting

Marp has built-in support for rendering math equations using KaTeX or MathJax.

**Inline Math:** Einstein's famous equation is $E = mc^2$.

**Block Math:**

$$
\frac{-b \pm \sqrt{b^2 - 4ac}}{2a}
$$

---

## 8. Emojis 🚀 & Auto-scaling text

Marp fully supports emojis: 🐶 🐱 🐭 🐹 🐰

You can also use the `<!-- fit -->` directive in headings to make them scale to fill the available width:

# <!-- fit --> Massive Text!

---

## 9. Layouts with CSS Grid

By defining a custom class in the front-matter styles, we can create columns:

<div class="columns">
<div>

### Column A
- Feature 1
- Feature 2
- Feature 3

</div>
<div>

### Column B
- Benefit X
- Benefit Y
- Benefit Z

</div>
</div>

---

## 10. Presenter Notes

This slide has presenter notes! 
When you export to PDF or use a presenter view, these notes won't show on the main slide.

<!-- 
These are the presenter notes! 
You can use them to remind yourself what to say.
- Mention feature X.
- Tell a joke.
-->
