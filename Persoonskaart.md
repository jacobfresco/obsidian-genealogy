<%*
const ROOT = "200 Genealogie/200.3 Persoonskaarten"; 
const title = await tp.system.prompt("Naam van de persoon?");
const surnameFromTitle = title.includes(",")
  ? title.split(",")[0].trim()
  : title.trim();
const parent = app.vault.getAbstractFileByPath(ROOT);
if (!parent) {
  new Notice(`Map niet gevonden: ${ROOT}`);
  tR += `⚠️ Map niet gevonden: ${ROOT}`;
  return;
}
const children = parent.children || [];
const folders = children.filter(f => "children" in f); // Alleen folders
const folderNames = folders.map(f => f.name);
const folderPaths = folders.map(f => f.path);

// Voeg een optie toe om direct een nieuwe subfolder te maken op basis van de achternaam
const makeNewLabel = `➕ Nieuwe map “${surnameFromTitle}”`;
const choices = [...folderNames, makeNewLabel];
const values  = [...folderPaths, "__MAKE_NEW__"];
const picked = await tp.system.suggester(choices, values, false, "Kies achternaam-map");

let targetFolderPath = picked;
if (picked === "__MAKE_NEW__") {
  targetFolderPath = `${ROOT}/${surnameFromTitle}`;
  const exists = app.vault.getAbstractFileByPath(targetFolderPath);
  if (!exists) {
    try {
      await app.vault.createFolder(targetFolderPath);
    } catch (e) {
      new Notice(`Kon map niet aanmaken: ${targetFolderPath}`);
      tR += `⚠️ Kon map niet aanmaken: ${targetFolderPath}`;
      return;
    }
  }
}

// --- bestands-check ---

const targetPathNoExt = `${targetFolderPath}/${title}`;
const targetFilePath = `${targetPathNoExt}.md`;
const existingFile = app.vault.getAbstractFileByPath(targetFilePath);
const currentFile = tp.file.find_tfile(tp.file.path(true));

const content = `---
surname: 
first_name: 
nick_name: 
father: 
mother: 
siblings:
  - brother:
  - sister: 
events:
  - birth:
    - date: 
    - place: 
  - death:
    - date: 
    - place:
  - buried:
    - date:
    - place:
marriages:
  marriage:
    - spouse: 
      date: 
      place: 
      divorced: 
        date: 
        place:
      children:
        - daugther: 
        - son: 
photo: 
last_update_info: 
---
<!-- START_AUTOGEN_PERSOONSKAART -->

<!-- END_AUTOGEN_PERSOONSKAART -->

---
### Aantekeningen

---
<sup>Laatste update: \`= this.file.mtime\`
Reden: \`= this.last_update_info\`</sup>
`;

if (existingFile) {
  const choice = await tp.system.suggester(
    [
      "⬆️ Ja, overschrijf de bestaande persoonskaart",
      "❌ Nee, behoud de bestaande persoonskaart"
    ],
    ["overwrite", "cancel"],
    false,
    `Bestand bestaat al:\n${targetFilePath}\nWat wil je doen?`
  );

  if (choice === "cancel" || !choice) {
    new Notice("Bestaande persoonskaart behouden; template geannuleerd.");
    tR += `⚠️ Bestaand bestand behouden. Niets overschreven: ${targetFilePath}`;
    return;
  }

  // Overschrijf de inhoud
  await app.vault.modify(existingFile, content);

  // Verwijder het tijdelijke (npk) bestand
  if (currentFile && currentFile.path !== targetFilePath) {
    await app.vault.delete(currentFile);
  }

  // Open het bestand na korte vertraging
  setTimeout(async () => {
    await app.workspace.getLeaf(true).openFile(existingFile);
  }, 500);

  new Notice(`Persoonskaart overschreven en geopend: ${targetFilePath}`);
  tR += `ℹ️ Bestaande persoonskaart overschreven en geopend: ${targetFilePath}`;
  return;
}

// Bestaat nog niet → huidige notitie vullen en verplaatsen
tR += content;
await tp.file.move(targetPathNoExt);
new Notice(`Verplaatst naar: ${targetFolderPath}`);
%>
