<%*

const title = await tp.system.prompt("Wat moet er gebeuren?");
const vandaag = tp.date.now("YYYY-MM-DD");

// ✅ Kies of je datums wilt invullen
const dateModeOptions = [
  "Geen datums",
  "Alleen startdatum",
  "Alleen einddatum",
  "Start- én einddatum"
];

const dateMode = await tp.system.suggester(
  dateModeOptions,
  dateModeOptions,
  false,
  "Wil je een start- en/of einddatum invullen?"
);

let startDateInput = null;
let dueDate = null;

if (dateMode === "Alleen startdatum" || dateMode === "Start- én einddatum") {
  startDateInput = await tp.system.prompt("Startdatum (YYYY-MM-DD):", vandaag);
}

if (dateMode === "Alleen einddatum") {
  const defaultDue = window.moment(vandaag, "YYYY-MM-DD").add(7, "days").format("YYYY-MM-DD");
  dueDate = await tp.system.prompt("Einddatum (YYYY-MM-DD):", defaultDue);
}

if (dateMode === "Start- én einddatum") {
  const startDateMoment = window.moment(startDateInput, "YYYY-MM-DD");
  const defaultDueDate = startDateMoment.add(7, "days").format("YYYY-MM-DD");
  dueDate = await tp.system.prompt("Einddatum (YYYY-MM-DD):", defaultDueDate);
}

// Tags (vrije invoer)
const tag = await tp.system.prompt("Welke extra tags wil je toevoegen aan deze taak?");

// ✅ Bouw datumvelden dynamisch op
let dateFields = "";
if (startDateInput) dateFields += ` [start:: ${startDateInput}]`;
if (dueDate) dateFields += ` [due:: ${dueDate}]`;

// Prioriteit
const priorityLabels = ["Geen", "Laag", "Normaal", "Hoog", "Hoogste"];
const priorityMap = {
  "Geen":    "none",
  "Laag":    "low",
  "Normaal": "medium",
  "Hoog":    "high",
  "Hoogste": "highest"
};

const gekozenPriorityLabel = await tp.system.suggester(
  priorityLabels,
  priorityLabels,
  false,
  "Kies prioriteit:"
);
const tasksPriority = priorityMap[gekozenPriorityLabel];

tR += `- [ ] ${title} #genealogie ${tag}${dateFields} [priority:: ${tasksPriority}]`;

%>

