# Welcome to [Slidev](https://github.com/slidevjs/slidev)!

To start the slide show:

- `pnpm install`
- `pnpm dev`
- visit <http://localhost:3030>

Edit the [slides.md](./slides.md) to see the changes.

Learn more about Slidev at the [documentation](https://sli.dev/).

## Deployment

GitHub Pages: https://kleinlennart.github.io/un-slidev-template/

## Export

### Slide Export

Export slides to PDF (default format):

```bash
pnpm slidev export --timeout 60000 --wait 1000
```

Example output: [slides-export.pdf](slides-export.pdf)

**Other export formats:**

- PPTX: `pnpm slidev export --format pptx`
- PNG: `pnpm slidev export --format png`
- Markdown: `pnpm slidev export --format md`
