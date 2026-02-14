# Project: Kids Apparel Shopify Store

# 🛠️ SHOPIFY LIQUID SYNTAX GUIDELINES

---

## Critical Liquid Rules (DO NOT IGNORE)

### 1. Snippet & Section Rendering

**CORRECT - Use `render` for snippets:**
```liquid
{%- render 'cart-drawer' -%}
{%- render 'header' -%}
{%- render 'footer' -%}
```

**INCORRECT - Do NOT use `include`:**
```liquid
{% include 'cart-drawer' %}        ❌ WRONG - This breaks Shopify
{% include 'header' %}              ❌ WRONG
```

**Why?**
- `render` is the modern Shopify standard
- `render` properly scopes variables
- `render` integrates with Shopify's asset system
- Whitespace control `-` prevents extra spaces in output

### 2. Asset Linking (CSS/JS)

**CORRECT:**
```liquid
{{ 'design-tokens.css' | asset_url | stylesheet_tag }}
{{ 'constants.js' | asset_url }}" defer="defer"></script>
<script src="{{ 'cart.js' | asset_url }}" defer="defer"></script>
```

**INCORRECT:**
```liquid
<link rel="stylesheet" href="/assets/design-tokens.css">     ❌
<script src="/assets/constants.js"></script>                ❌
```

### 3. Inline Assets (SVGs, Icons)

**CORRECT:**
```liquid
{{ 'icon-close.svg' | inline_asset_content }}
```

This directly includes SVG content from assets folder.

### 4. Script Tag Structure

**CORRECT:**
```liquid
<script>
  window.shopUrl = '{{ request.origin }}';
  window.routes = {
    cart_url: '{{ routes.cart_url }}',
  };
</script>

<script src="{{ 'cart.js' | asset_url }}" defer="defer"></script>
```

**INCORRECT:**
```liquid
{{ shop.script_tag_attribute }} type="text/javascript">    ❌
  // content
</script>
```

### 5. Liquid Comments in Production

**CORRECT:**
```liquid
{% comment %}
  Renders cart drawer
  Usage: {% render 'cart-drawer' %}
{% endcomment %}
```

These comments are stripped before delivery to browser.

### 6. Whitespace Control (Important!)

**Use whitespace stripping for clean output:**
```liquid
{%- render 'cart-drawer' -%}    ✔ No extra whitespace
{% render 'cart-drawer' %}       ✔ Some whitespace (acceptable for clarity)
```

The `-` character removes whitespace:
- `{%-` = remove space before
- `-%}` = remove space after

### 7. Conditional Rendering

**CORRECT:**
```liquid
{%- if settings.cart_type == 'drawer' -%}
  {%- render 'cart-drawer' -%}
{%- endif -%}

{%- if cart == empty -%}
  <p>Your cart is empty</p>
{%- endif -%}
```

### 8. Settings & Global Objects

**CORRECT:**
```liquid
{% if settings.favicon != blank %}
  <link rel="icon" href="{{ settings.favicon | image_url: width: 32 }}">
{% endif %}

{{ shop.name }}
{{ request.locale.iso_code }}
{{ canonical_url }}
{{ routes.cart_url }}
```

### 9. File Structure Rules

**Layout files (`layout/theme.liquid`):**
- Use `render` for snippets: `{%- render 'header' -%}`
- Use sections groups: `{% sections 'header-group' %}`
- Load CSS/JS properly with `asset_url`
- One `{{ content_for_layout }}` for page content

**Section files (`sections/*.liquid`):**
- Must include `{% schema %}` block at end
- Can have settings, presets, default content
- Called via `{% sections 'group-name' %}`

**Snippet files (`snippets/*.liquid`):**
- No schema block needed
- Rendered via `{%- render 'snippet-name' -%}`
- Can accept parameters: `{% render 'product-card', product: product %}`
- Keep self-contained (include CSS & JS inline)

**Template files (`templates/*.liquid`):**
- Use sections and snippets for components
- Access page-specific objects: `{{ page.title }}`
- Use proper template-specific logic

### 10. Mobile Menu & Interaction Patterns

**CORRECT - Use data attributes for JS hooks:**
```liquid
<button data-menu-toggle aria-expanded="false">Menu</button>
<nav data-mobile-menu aria-hidden="true">
  <!-- content -->
</nav>

<script>
  document.querySelector('[data-menu-toggle]').addEventListener('click', function() {
    this.setAttribute('aria-expanded', 
      this.getAttribute('aria-expanded') === 'true' ? 'false' : 'true');
  });
</script>
```

### 11. Accessibility in Liquid

**CORRECT:**
```liquid
<a href="{{ product.url }}" class="product-link">
  {{ product.title }}
</a>

<button aria-label="Close cart">×</button>

{% if product.available %}
  <span aria-label="In stock">Available</span>
{% endif %}
```

### 12. Common Liquid Filters (Use These!)

```liquid
{{ product.title | capitalize }}
{{ price | money }}
{{ text | strip_html | truncatewords: 20 }}
{{ image | image_url: width: 200 }}
{{ collection.products | where: 'available', true }}
{{ date | date: '%B %d, %Y' }}
{{ settings.logo | image_url: width: 200, height: 200 }}
```

### 13. Loop Patterns

**CORRECT:**
```liquid
{% for product in collection.products limit: 12 %}
  <div class="product-card">
    <img src="{{ product.featured_image | image_url: width: 300 }}" alt="{{ product.title }}">
    <h3>{{ product.title }}</h3>
  </div>
  
  {% if forloop.last %}
    <p>Last product shown</p>
  {% endif %}
{% endfor %}
```

### 14. Settings Schema (Required for sections)

**CORRECT:**
```liquid
{% schema %}
{
  "name": "Header",
  "settings": [
    {
      "type": "image_picker",
      "id": "logo",
      "label": "Logo"
    },
    {
      "type": "color",
      "id": "background_color",
      "label": "Background Color",
      "default": "#ffffff"
    }
  ]
}
{% endschema %}
```

Snippets DO NOT need schema blocks.

---

## File Layout Template (Copy This!)

**layout/theme.liquid:**
```liquid
<!doctype html>
<html lang="{{ request.locale.iso_code }}">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  
  {%- if settings.favicon -%}
    <link rel="icon" href="{{ settings.favicon | image_url: width: 32 }}">
  {%- endif -%}
  
  {{ 'design-tokens.css' | asset_url | stylesheet_tag }}
  {{ content_for_header }}
</head>

<body>
  <a href="#main-content" class="skip-to-content">Skip to content</a>
  
  {%- render 'header' -%}
  {%- render 'cart-drawer' -%}
  
  <main id="main-content" role="main">
    {{ content_for_layout }}
  </main>
  
  {%- render 'footer' -%}
  
  <script src="{{ 'constants.js' | asset_url }}" defer></script>
  <script src="{{ 'global.js' | asset_url }}" defer></script>
</body>
</html>
```

---

## Common Mistakes to Avoid

1. ❌ Using `{% include %}` instead of `{% render %}`
2. ❌ Linking assets without `asset_url` filter
3. ❌ Missing `schema` block in sections
4. ❌ Adding `schema` block to snippets
5. ❌ Not stripping whitespace with `-`
6. ❌ Using hardcoded `/assets/` paths
7. ❌ Missing `defer` on script tags
8. ❌ Not using proper Shopify objects (like `routes.cart_url`)
9. ❌ Forgetting `{{ content_for_layout }}` in main layout
10. ❌ Not using data attributes for JS hooks

---

# 🎨 DESIGN SYSTEM & UI GUIDELINES

---

## 🌈 Color System (Inspired by Baby Apparel Aesthetic)

### Primary Palette (Soft / Baby-Friendly)

Primary Brand Color:
- Soft Blush Pink → #FADADD  
  Usage: Highlights, buttons, badges, accents

Secondary Brand Color:
- Powder Blue → #D6EAF8  
  Usage: Secondary accents, hover states, backgrounds

Neutral Base:
- Pure White → #FFFFFF  
  Usage: Main backgrounds

Soft Neutral:
- Light Warm Grey → #F5F5F5  
  Usage: Section backgrounds / Cards / Dividers

Text Primary:
- Charcoal Soft Black → #333333

Text Secondary:
- Muted Grey → #777777

Success / Stock Indicator:
- Soft Mint → #C8E6C9

Sale / Attention:
- Soft Coral → #FF8A80

---

## 🎯 Color Usage Rules

✔ Avoid harsh, saturated colors  
✔ Prefer pastel, soft tones  
✔ Maintain airy, gentle visual tone  
✔ Use white space generously  
✔ Backgrounds must feel light & breathable

---

## 🔤 Typography System

Primary Font:
- Clean Sans Serif (Recommended: Poppins / Inter / Montserrat)

Heading Style:
- Weight: SemiBold / Bold
- Color: #333333

Body Text:
- Weight: Regular
- Color: #555555

Secondary Text:
- Color: #777777

Buttons:
- Weight: Medium / SemiBold

---

## 📐 Spacing & Layout Principles

✔ Use consistent spacing scale:

- XS → 4px
- SM → 8px
- MD → 16px
- LG → 24px
- XL → 40px
- XXL → 64px

✔ Generous vertical spacing between sections  
✔ Avoid cluttered layouts  
✔ Maintain visual breathing room

---

# 🖥️ GLOBAL UI STRUCTURE

---

## 🔝 Header / Navigation

### Layout:
- Clean white background
- Subtle shadow OR thin divider line
- Sticky on scroll

### Components:
✔ Logo (Left)  
✔ Main Navigation (Center / Right)  
✔ Icons (Right):
   - Search
   - Account
   - Cart

### Interaction:
✔ Dropdown menus with soft animation  
✔ Hover highlight using brand pastel color  
✔ No aggressive hover effects

---

## 🏠 Home Page Layout

### 1️⃣ Hero Section

✔ Large lifestyle banner  
✔ Soft imagery (kids / pastel tones / natural light)

Overlay Content:
- Headline (Short, emotional)
- Subtext (Optional)
- CTA Button

CTA Style:
- Rounded edges
- Soft brand color fill
- Subtle hover effect

---

### 2️⃣ Featured Categories

✔ Visual cards (NOT text links)

Card Structure:
- Category image
- Category name
- Entire card clickable

Hover:
✔ Soft zoom OR shadow lift

---

### 3️⃣ Product Carousel / Grid

✔ Clean product cards  
✔ Even spacing  
✔ No visual noise

---

## 🛍️ Product Card UI

### Structure:

✔ Product Image  
✔ Product Name  
✔ Price  
✔ Optional Badge:
   - Sale
   - New
   - Sold Out

✔ Add to Cart Button OR Quick Add

---

### Visual Rules:

✔ Rounded corners  
✔ Soft shadow (very subtle)  
✔ Consistent aspect ratio  
✔ Avoid heavy borders

Hover Interaction:

✔ Image swap OR subtle zoom  
✔ Button fade-in optional  
✔ Shadow elevation slight

---

## 📦 Collection / Category Page

### Layout:

✔ Grid layout  
✔ Filters panel (Left desktop / Drawer mobile)

Filters:
- Size
- Price
- Color
- Availability

Sorting:
✔ Dropdown → Clean minimal style

---

## 📄 Product Detail Page (PDP)

---

### Layout Structure:

Left:
✔ Image Gallery (Carousel / Thumbnails)

Right:
✔ Product Title  
✔ Price  
✔ Size Selector  
✔ Quantity Selector  
✔ Add to Cart Button  
✔ Description  
✔ Size Guide  
✔ Shipping Info  

---

### Buttons:

Primary CTA:
✔ Soft brand color  
✔ Rounded  
✔ Large touch-friendly height  

Secondary CTA:
✔ Outline style  

---

### Variant Selector:

✔ Pill-shaped buttons OR dropdown  
✔ Clear selected state  
✔ No harsh outlines  

---

## 🛒 Cart Experience

✔ Slide-in drawer preferred  
✔ Soft animation  
✔ Clear hierarchy  

Components:
✔ Product summary  
✔ Quantity control  
✔ Remove option  
✔ Checkout CTA  

---

# ✨ MICROINTERACTIONS & POLISH

---

✔ Soft transitions everywhere (150–250ms)

✔ Button Hover:
- Slight darken OR subtle glow

✔ Card Hover:
- Soft lift / shadow increase

✔ Add to Cart:
- Toast OR mini animation

✔ Loading:
- Skeleton loaders preferred

✔ Empty States:
- Friendly illustration + text

---

# 📱 RESPONSIVE DESIGN RULES

---

✔ Mobile-first priority

Header:
✔ Collapsible hamburger menu

Filters:
✔ Drawer / Modal

Buttons:
✔ Full-width on mobile

Spacing:
✔ Slightly reduced but breathable

Images:
✔ Maintain clarity & proportions

---

# 🧸 VISUAL STYLE & IMAGERY

---

✔ Soft lighting  
✔ Pastel tones  
✔ Natural textures  
✔ Minimal harsh contrast  
✔ Lifestyle > Studio feel  

Avoid:
❌ Dark moody visuals  
❌ Heavy shadows  
❌ Loud gradients  

---

# 🎯 DESIGN TONE

---

✔ Clean  
✔ Soft  
✔ Premium yet playful  
✔ Elegant & breathable  
✔ Parent-trust oriented  

---

# ♿ ACCESSIBILITY RULES

---

✔ Maintain text contrast  
✔ Clickable targets ≥ 44px  
✔ Clear focus states  
✔ Readable font sizes  

---

# ✅ UI DO’S & DON’TS

---

DO:

✔ Use white space generously  
✔ Maintain visual softness  
✔ Keep layouts simple  
✔ Use pastel accents  

DON’T:

❌ Overcrowd interface  
❌ Use harsh colors  
❌ Add aggressive animations  
❌ Use heavy borders  

---

# 🎯 FINAL GOAL

Create a visually soft, modern, premium kids apparel store that:

✔ Feels trustworthy  
✔ Feels premium  
✔ Is easy to browse  
✔ Converts smoothly  
✔ Works beautifully on mobile  
✔ Integrates seamlessly with Shopify
