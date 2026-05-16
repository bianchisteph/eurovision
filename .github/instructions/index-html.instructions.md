---
description: "Use when modifying index.html, working on UI components, styling, animations, or Tailwind classes. Covers the single-file app structure and Eurovision theme conventions."
applyTo: "index.html"
---
# index.html — Convention SPA fichier unique

- Tout le code (HTML, CSS, JS) est dans ce fichier unique — ne pas proposer de split
- CSS custom dans le bloc `<style>` en tête, après Tailwind config
- JavaScript dans le bloc `<script>` en fin de body
- Sections HTML délimitées par des commentaires `<!-- ======================== SCREEN: ... ======================== -->`
- Sections JS délimitées par des commentaires `// =====================================================================`
- Trois écrans : `screen-config`, `screen-login`, `screen-app`
- Trois onglets : `tab-prono`, `tab-classement`, `tab-admin`
- Palette : rose `#e91e8c`, violet `#7b2ff7`, bleu `#1e3a8a`, or `#fbbf24`, dark `#0f0b1e`
- Classes custom : `euro-gradient`, `euro-gradient-text`, `card-glow`, `slide-up`, `fade-in`
- Toujours échapper les entrées utilisateur avec `esc()` avant insertion dans le DOM
