- [[Script for MES Membership Card AY 24-25]]
- [[Custom dmenu Scripts]]
- [[Quickly Get Textbook Engineering Table Values Tip]]

```js
// change js to dataviewjs to 
const autoto = dv.pages()
    .where(p => p.file.tags && p.file.tags.includes("#automation"))
    .map(p => `- [[${p.file.name}]]`)
    .join("\n");

// Copy links to clipboard; change parameter depending on what you want to sync
navigator.clipboard.writeText(autoto).then(() => {
    dv.paragraph("Links copied to clipboard!");
}).catch(err => {
    dv.paragraph("Failed to copy: " + err);
});

```