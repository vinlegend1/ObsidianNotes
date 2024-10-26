## Supplementary Articles
- [[BEP - the Best Efficiency Point of a Pump]]
- [[Centrifugal Pumps - Capacity Modulation]]
- [[Centrifugal Pumps]]
- [[Pumps - Affinity Laws]]
- [[Pumps - NPSH (Net Positive Suction Head)]]
- [[Pumps - Suction Specific Speed]]
## Fluid Mechanics from Old Remnote Notes
- [[Control Volume Relations]]
- [[Differential Relations for Fluid Particles]]
- [[Dimensional Analysis and Similarity]]
- [[Hydrostatics on Curved Surfaces]]
- [[Hydrostatics]]
- [[Intro to Fluid Mechanics]]
- [[Pascal's Principle]]
- [[Pressure Distribution in a Fluid]]
- [[Viscous Internal Flow]]

## Dynamic Links

```js
// change js to dataviewjs to 
const omnivore = dv.pages('"Omnivore"')
    .where(p => p.file.tags && p.file.tags.includes("#fluid_mechanics"))
    .map(p => `- [[${p.file.name}]]`)
    .join("\n");
const remnote = dv.pages('"STEM/Engineering/Fluid Mechanics"')
    .map(p => `- [[${p.file.name}]]`)
    .join("\n");

// Copy links to clipboard; change parameter depending on what you want to sync
navigator.clipboard.writeText(remnote).then(() => {
    dv.paragraph("Links copied to clipboard!");
}).catch(err => {
    dv.paragraph("Failed to copy: " + err);
});

```
