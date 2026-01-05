<%*
const fm = tp.frontmatter || {};

// ------------- Helpers -------------
const maanden = [
  "januari","februari","maart","april","mei","juni",
  "juli","augustus","september","oktober","november","december"
];

function formatDate(dateStr) {
  if (!dateStr) return "";
  if (dateStr instanceof Date) {
    const dag = dateStr.getDate();
    const maandIndex = dateStr.getMonth();
    const jaar = dateStr.getFullYear();
    return `${dag} ${maanden[maandIndex]} ${jaar}`;
  }
  if (typeof dateStr !== "string" || !dateStr.includes("-")) return String(dateStr);
  const [y, m, d] = dateStr.split("-");
  const dag = parseInt(d, 10);
  const maandIndex = parseInt(m, 10) - 1;
  if (isNaN(dag) || maandIndex < 0 || maandIndex > 11) return dateStr;
  return `${dag} ${maanden[maandIndex]} ${y}`;
}

function extractDatePlace(list) {
  if (!Array.isArray(list)) return { date: "", place: "" };
  let date = "";
  let place = "";
  for (const item of list) {
    if (item && typeof item === "object") {
      if (item.date) date = item.date;
      if (item.place) place = item.place;
    }
  }
  return { date, place };
}

function linkify(value) {
  if (!value) return "";
  if (typeof value === "string") {
    if (value.includes("[[")) return value;
    return `[[${value}]]`;
  }
  if (Array.isArray(value)) {
    const joined = value.flat(Infinity).filter(Boolean).join(" ");
    if (joined.includes("[[")) return joined;
    return `[[${joined}]]`;
  }
  if (typeof value === "object") {
    const path = value.path || (value.file && value.file.path) || value.name || value.fileName;
    if (path) return `[[${path}]]`;
  }
  return `[[${String(value)}]]`;
}

function linkText(value) {
  if (!value) return "";
  if (typeof value === "string") {
    const m = value.match(/\[\[(.+?)\]\]/);
    return m ? m[1] : value;
  }
  if (Array.isArray(value)) return value.flat(Infinity).filter(Boolean).join(" ");
  if (typeof value === "object") {
    return value.path || (value.file && value.file.path) || value.name || value.fileName || String(value);
  }
  return String(value);
}

// ------------- Basisvelden -------------
const surname    = fm.surname    || "";
const firstName  = fm.first_name || "";
const nickName   = fm.nick_name  || "";

// ------------- Events -------------
let birthDate = "", birthPlace = "";
let deathDate = "", deathPlace = "";
let buriedDate = "", buriedPlace = "";  // <<< begraven toegevoegd

if (Array.isArray(fm.events)) {
  for (const ev of fm.events) {
    if (ev.birth) {
      const bp = extractDatePlace(ev.birth);
      birthDate = bp.date || birthDate;
      birthPlace = bp.place || birthPlace;
    }
    if (ev.death) {
      const dp = extractDatePlace(ev.death);
      deathDate = dp.date || deathDate;
      deathPlace = dp.place || deathPlace;
    }
    if (ev.buried) {  // <<< begraven toegevoegd
      const bu = extractDatePlace(ev.buried);
      buriedDate = bu.date || buriedDate;
      buriedPlace = bu.place || buriedPlace;
    }
  }
}

// ------------- Ouders & siblings -------------
const father = fm.father;
const mother = fm.mother;

let siblingsBlock = "";
if (Array.isArray(fm.siblings) && fm.siblings.length) {
  siblingsBlock += "**Broer(s) / zus(sen):**\n";
  for (const s of fm.siblings) {
    if (!s || typeof s !== "object") continue;
    if (s.brother) siblingsBlock += `- Broer ${linkify(s.brother)}\n`;
    if (s.sister) siblingsBlock += `- Zus ${linkify(s.sister)}\n`;
  }
}

// ------------- Huwelijken & kinderen -------------
let marriagesBlock = "";
const marriagesContainer = fm.marriages || {};
let marriages = marriagesContainer.marriage || [];

if (Array.isArray(marriages)) {
  if (marriages.length && marriages.every(m => m && typeof m === "object" && Object.keys(m).length === 1)) {
    const merged = {};
    for (const part of marriages) Object.assign(merged, part);
    marriages = [merged];
  }
} else if (marriages && typeof marriages === "object") {
  marriages = [marriages];
} else {
  marriages = [];
}

for (const m of marriages) {
  if (!m || typeof m !== "object") continue;

  const spouse   = m.spouse ? linkify(m.spouse) : "";
  const mDate    = m.date ? formatDate(m.date) : "";
  const mPlace   = m.place || "";

  // Scheiding
  let divorcedDate = "";
  let divorcedPlace = "";
  if (Array.isArray(m.divorced)) {
    const dv = extractDatePlace(m.divorced);
    divorcedDate = dv.date;
    divorcedPlace = dv.place;
  } else if (m.divorced && typeof m.divorced === "object") {
    divorcedDate = m.divorced.date  || "";
    divorcedPlace = m.divorced.place || "";
  }

  // Alleen tonen als gescheiden-info aanwezig is
  let divorcedLine = "";
  if (divorcedDate || divorcedPlace) {
    divorcedLine = `**Gescheiden:** ${divorcedPlace || ""}${(divorcedPlace && divorcedDate) ? " op " : ""}${divorcedDate ? formatDate(divorcedDate) : ""}\n`;
  }

  // Kinderen
  let childrenBlock = "";
  if (Array.isArray(m.children) && m.children.length) {
    childrenBlock += "**Kinderen:**\n";
    for (const c of m.children) {
      if (!c || typeof c !== "object") continue;
      const daughter = c.daughter || c.daugther;
      const son      = c.son;
      if (daughter) childrenBlock += `- Dochter ${linkify(daughter)}\n`;
      if (son)      childrenBlock += `- Zoon ${linkify(son)}\n`;
    }
  }

  marriagesBlock += `\n### Gezin met ${spouse}\n`;
  marriagesBlock += `**Getrouwd op:** ${mDate}\n`;
  marriagesBlock += `**Locatie:** ${mPlace}\n`;
  if (childrenBlock) marriagesBlock += `${childrenBlock}\n`;
  if (divorcedLine) marriagesBlock += divorcedLine;
}

// ------------- Kaart opbouwen (zonder markers) -------------
let kaart = "";

kaart += `### Algemeen
**Achternaam:** ${surname}
**Voornaam:** ${firstName}
**Roepnaam:** ${nickName}
**Geboren:** ${birthDate ? formatDate(birthDate) : ""}
**Geboorteplaats:** ${birthPlace}
`;

if (deathDate || deathPlace) {
  kaart += `**Overleden:** ${deathDate ? formatDate(deathDate) : ""}${(deathDate && deathPlace) ? " in " : ""}${deathPlace || ""}\n`;
}

// <<< begraven toegevoegd
if (buriedDate || buriedPlace) {
  kaart += `**Begraven:** ${buriedDate ? formatDate(buriedDate) : ""}${(buriedDate && buriedPlace) ? " in " : ""}${buriedPlace || ""}\n`;
}

kaart += `\n`;

if (father || mother) {
  const fatherText = father ? linkText(father) : "";
  const motherText = mother ? linkText(mother) : "";
  kaart += `### Familie ${fatherText}${(fatherText && motherText) ? " x " : ""}${motherText}\n`;
  if (father) kaart += `**Vader:** ${linkify(father)}\n`;
  if (mother) kaart += `**Moeder:** ${linkify(mother)}\n`;
}

if (siblingsBlock) {
  kaart += `${siblingsBlock}\n`;
}

if (marriagesBlock) {
  kaart += marriagesBlock;
}

// Foto-blok met DataviewJS
kaart += `

\`\`\`dataviewjs
const p = dv.current().photo;
if (p) dv.paragraph(\`![[\${p}|#rechts]]\`);
\`\`\`
`;

// ------------- In bestand schrijven tussen markers -------------
const START = "<!-- START_AUTOGEN_PERSOONSKAART -->";
const END   = "<!-- END_AUTOGEN_PERSOONSKAART -->";

const file = tp.file.find_tfile(tp.file.path(true));
let content = await app.vault.read(file);

// blok dat we willen plaatsen
const fullBlock = `${START}
${kaart.trim()}
${END}`;

// Als beide markers bestaan: vervang het stuk ertussen
if (content.includes(START) && content.includes(END)) {
  const regex = new RegExp(`${START}[\\s\\S]*?${END}`);
  content = content.replace(regex, fullBlock);
} else {
  // Anders: voeg het blok aan het einde van het bestand toe
  content += `

${fullBlock}
`;
}

await app.vault.modify(file, content);

// Niets invoegen op de cursor
tR = "";
-%>
