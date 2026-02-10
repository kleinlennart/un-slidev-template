---
theme: default
title: PU Team Retreat
transition: go-forward | go-backward
fonts:
  sans: Roboto
  mono: Roboto Mono
themeConfig:
  primary: "#009edb"
src: ./pages/00-title_page.md
---

---

# Agenda

- Your content here
- Your content here
- Your content here
- Your content here

---

# Slide Title

Your content here

Your content here

---

# Plot Example

Say something

<BarChart 
  :data="[
    { label: 'Q1', value: 65, lightness: 60 },
    { label: 'Q2', value: 82, lightness: 52 },
    { label: 'Q3', value: 78, lightness: 47 },
    { label: 'Q4', value: 95, lightness: 43 }
  ]"
  :height="350"
/>
