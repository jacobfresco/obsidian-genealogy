<%*
const huidigenaam = tp.config.target_file.basename;
const onderwerp = await tp.system.prompt("Onderwerp van het oncerzoek?", huidigenaam);
const nieuweNaam = `${onderwerp}`;
const datum = tp.date.now("DD-MM-YYYY");

const input = await tp.system.prompt("Voer tags in (gescheiden door komma's, zonder #)","genealogie,onderzoekstaak");
if (!input) return;

const tags = input
  .split(",")
  .map(t => t.trim())
  .filter(t => t.length > 0);

let frontmatter = `\ntags:\n`;
let tagvar  = ``;
for (const tag of tags) {
  frontmatter += `  - ${tag}\n`;
  tagvar += `#${tag} `;
}
frontmatter += `---\n`;
const tagvars = tagvar;

tR += `---
onderwerp: ${onderwerp}
aliases:
created: ${datum} 
status: Nieuw`;
tR += frontmatter;
tR += `
## Info
**Onderzoeker:** [jfresco](https://jacobfresco.nl)
**Parenteel:** [Abraham Fresco (1625)](http://stamboom.jacobfresco.nl)
**Status:** \`= this.status\`
**Tags:** ${tagvars}

## Laatste informatie
| Datum | Info | link |
| --- | --- | --- |

## Acties

\`button-inserttask\` 
   
## Aantekeningen


## Backlinks`;
%>
