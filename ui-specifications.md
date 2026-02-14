# 🎨 UI SPECIFICATIONS — Kids Apparel Shopify Store
# Theme Target: Shopify Dawn (OS 2.0 Compatible)

---

# ✅ CORE UI PRINCIPLE

All UI components MUST be:

✔ Section-based  
✔ Block-driven  
✔ Setting-configurable  
✔ Dynamic-source compatible  
✔ Fully editable via Shopify Theme Editor  

❌ No hardcoded layout content  
❌ No static product placement  

---

# 🌈 DESIGN TOKENS

---

## Colors

--color-background-primary: #FFFFFF  
--color-background-secondary: #F5F5F5  
--color-text-primary: #333333  
--color-text-secondary: #777777  
--color-accent-primary: #FADADD  
--color-accent-secondary: #D6EAF8  
--color-sale: #FF8A80  
--color-success: #C8E6C9  

---

## Typography

Font Family:
- Primary: Sans Serif (Poppins / Inter / System UI)

Heading:
- Weight: 600 / 700

Body:
- Weight: 400

Button:
- Weight: 500 / 600

---

## Spacing Scale

--space-xs: 4px  
--space-sm: 8px  
--space-md: 16px  
--space-lg: 24px  
--space-xl: 40px  
--space-xxl: 64px  

---

## Border Radius

--radius-soft: 8px  
--radius-pill: 50px  

---

## Shadow System

--shadow-soft:
0 4px 12px rgba(0,0,0,0.05)

--shadow-hover:
0 6px 18px rgba(0,0,0,0.08)

---

---

# 🖥️ GLOBAL COMPONENTS

---

# 🔝 HEADER COMPONENT

---

## Behavior

✔ Sticky enabled (configurable)  
✔ Dropdown menus (desktop)  
✔ Hamburger menu (mobile)  

---

## Layout Structure

| Logo | Navigation | Icons |

Icons:

✔ Search  
✔ Account  
✔ Cart  

---

## Shopify Settings Required

✔ Logo Upload  
✔ Menu Selection  
✔ Sticky Toggle  
✔ Transparent Toggle  
✔ Announcement Bar Toggle  

---

---

# 🏠 HERO BANNER COMPONENT

---

## Layout

Full-width responsive banner

Content:

✔ Heading  
✔ Subtext  
✔ CTA Button  

---

## UI Rules

✔ Text overlay must remain readable  
✔ CTA must be visually dominant  

---

## Shopify Schema Requirements

```json
{
  "name": "Hero Banner",
  "settings": [
    { "type": "image_picker", "id": "image" },
    { "type": "text", "id": "heading" },
    { "type": "text", "id": "subtext" },
    { "type": "text", "id": "cta_label" },
    { "type": "url", "id": "cta_link" },
    { "type": "select", "id": "text_alignment" }
  ],
  "presets": [{ "name": "Hero Banner" }]
}
