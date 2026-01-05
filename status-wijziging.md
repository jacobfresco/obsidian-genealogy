<%*
const opties = ["Nieuw", "Actief", "Gepauzeerd", "Voltooid", "Gearchiveerd"];

// Laat een keuzelijst zien
const keuze = await tp.system.suggester(opties, opties, false, "Kies status");
if (!keuze) return;

// Update frontmatter van de huidige (target) notitie
const file = tp.config.target_file;

await app.fileManager.processFrontMatter(file, (fm) => {
  fm.status = keuze;
});

// Optioneel: klein bevestigingsbericht
new Notice(`Status gezet op: ${keuze}`);
%>
