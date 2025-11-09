<!doctype html>
<html lang="es">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>🎁Intercambio — Familia Juárez</title>
  <style>
    body { font-family: system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial; max-width:720px; margin:auto; padding:18px; }
    h1 { margin-top: 0; }
    label, p.note { display:block; margin-top:10px; }
    input[type="text"], select, button { font-size:16px; padding:8px; width:100%; box-sizing:border-box; margin-top:6px; }
    .row { display:flex; gap:8px; }
    .col { flex:1; }
    #result { font-size:20px; margin-top:18px; font-weight:600; }
    table { border-collapse:collapse; width:100%; margin-top:12px; }
    td, th { border:1px solid #ccc; padding:6px 8px; text-align:left; }
    #adminBox { display:none; background:#f8f8f8; padding:10px; border-radius:6px; margin-top:12px; }
    .small { font-size:13px; color:#555; }
    .danger { color:#a00; }
  </style>
</head>
<body>
  <h1>🎁Intercambio — Familia Juárez</h1>
  <p class="small"></p>

  <label for="nameInput">Tu nombre</label>
  <input id="nameInput" list="namesList" placeholder="Escribe tu nombre exactamente (ej. Lupe)" autocomplete="off" />
  <datalist id="namesList"></datalist>

  <div style="margin-top:8px;">
    <button id="viewBtn">Ver a quién le regalas</button>
  </div>

  <p id="result" aria-live="polite"></p>

  <hr/>

  <div id="adminBox">
    <h3>Vista del organizador</h3>
    <p class="small"><code>?admin=familia-juarez</code>.</p>
    <div style="display:flex; gap:8px; margin-bottom:8px;">
      <button id="regenBtn">Regenerar sorteo</button>
      <div style="flex:1;">
        <label class="small"></label>
        <input id="seedInput" type="text" readonly />
      </div>
    </div>
    <table id="adminTable" aria-label="Tabla de asignaciones">
      <thead><tr><th>Persona</th><th>Le toca regalar a</th></tr></thead>
      <tbody></tbody>
    </table>
    <p class="small"></p>
    <p class="small danger"></p>
  </div>

<script>
/* === Lista de participantes (19) === */
const NAMES = [
  "Lupe","Joel","Abuelita","Jorge Cora","Jorge Ángel","Tere","Sofi","Germán Grande","Germán Chico",
  "Rosario","Fer","Sara","Víctor","Chary","Rubén","Karim","Kael","Neithan",
  "Oscar"
];

/* Helpers: seeded RNG (Mulberry32) */
function mulberry32(a) {
  return function() {
    a |= 0; a = a + 0x6D2B79F5 | 0;
    let t = Math.imul(a ^ a >>> 15, 1 | a);
    t = t + Math.imul(t ^ t >>> 7, 61 | t) ^ t;
    return ((t ^ t >>> 14) >>> 0) / 4294967296;
  }
}

/* Shuffle with seed */
function shuffleWithSeed(arr, seed) {
  const a = arr.slice();
  const rnd = mulberry32(seed >>> 0);
  for (let i = a.length - 1; i > 0; i--) {
    const j = Math.floor(rnd() * (i + 1));
    [a[i], a[j]] = [a[j], a[i]];
  }
  return a;
}

/* Generate valid mapping: no self-assign, and Joel & Víctor no mutual */
function generateMapping(seed) {
  // seed: integer
  let attempts = 0;
  let receivers;
  do {
    // derive a local seed variant to change shuffle each attempt predictably
    receivers = shuffleWithSeed(NAMES, seed + attempts);
    const mapping = {};
    for (let i = 0; i < NAMES.length; i++) mapping[NAMES[i]] = receivers[i];
    // validate
    let ok = true;
    for (const giver of NAMES) {
      const rec = mapping[giver];
      if (rec === giver) { ok = false; break; }
      if ((giver === "Joel" && rec === "Víctor") || (giver === "Víctor" && rec === "Joel")) { ok = false; break; }
    }
    if (ok) return {mapping, seedUsed: seed + attempts};
    attempts++;
  } while (attempts < 2000);
  throw new Error("No se pudo generar un mapeo válido (intenta otro seed).");
}

/* Utilities URL params */
function getParam(name) {
  return new URLSearchParams(location.search).get(name);
}

/* Populate datalist */
const namesList = document.getElementById('namesList');
for (const n of NAMES) {
  const opt = document.createElement('option');
  opt.value = n;
  namesList.appendChild(opt);
}

/* Initial seed handling */
let seedParam = getParam('seed');
let seed;
if (seedParam !== null) {
  // try parseInt, fallback to hash of string
  seed = parseInt(seedParam, 10);
  if (Number.isNaN(seed)) {
    // simple string-to-int hash
    seed = 0;
    for (let i = 0; i < seedParam.length; i++) seed = (seed * 31 + seedParam.charCodeAt(i)) >>> 0;
  }
} else {
  // random seed based on time (but reproducible per page load)
  seed = Math.floor(Math.random() * 0x7fffffff);
}

/* Generate initial mapping */
let current = generateMapping(seed);
let MAPPING = current.mapping;
document.getElementById('seedInput') && (document.getElementById('seedInput').value = String(current.seedUsed));

/* Show admin if ?admin=familia-juarez */
if (getParam('admin') === 'familia-juarez') {
  document.getElementById('adminBox').style.display = 'block';
  renderAdminTable();
}

/* Render admin table */
function renderAdminTable() {
  const tbody = document.querySelector('#adminTable tbody');
  tbody.innerHTML = '';
  for (const giver of NAMES) {
    const tr = document.createElement('tr');
    const td1 = document.createElement('td'); td1.textContent = giver;
    const td2 = document.createElement('td'); td2.textContent = MAPPING[giver];
    tr.appendChild(td1); tr.appendChild(td2);
    tbody.appendChild(tr);
  }
  // update seed display (if admin)
  const seedInput = document.getElementById('seedInput');
  if (seedInput) seedInput.value = String(current.seedUsed);
}

/* Button handlers */
document.getElementById('viewBtn').addEventListener('click', () => {
  const name = document.getElementById('nameInput').value.trim();
  const result = document.getElementById('result');
  if (!name) { result.textContent = "Por favor escribe tu nombre."; return; }
  // find exact match ignoring diacritics? We'll require exact match from list.
  if (!NAMES.includes(name)) {
    // try case-insensitive match
    const lower = NAMES.find(n => n.toLowerCase() === name.toLowerCase());
    if (lower) {
      // use normalized found name
      const receiver = MAPPING[lower];
      result.textContent = `Te toca regalar a: ${receiver}`;
      return;
    }
    result.textContent = "Nombre no reconocido. Revisa que esté escrito exactamente como en la lista.";
    return;
  }
  const receiver = MAPPING[name];
  result.textContent = `Te toca regalar a: ${receiver}`;
});

/* Regenerar sorteo (solo admin) */
const regenBtn = document.getElementById('regenBtn');
if (regenBtn) {
  regenBtn.addEventListener('click', () => {
    // generate a new random seed and mapping
    const newSeed = Math.floor(Math.random() * 0x7fffffff);
    current = generateMapping(newSeed);
    MAPPING = current.mapping;
    renderAdminTable();
    // show a small alert para que el organizador copie el seed si quiere fijarlo
    alert('Sorteo regenerado. Seed: ' + current.seedUsed + '\nCopia ese número y pégalo en la URL como ?admin=familia-juarez&seed=' + current.seedUsed + ' para fijarlo.');
  });
}

/* On page load, if seed was provided but mapping differs, update seedInput */
document.addEventListener('DOMContentLoaded', () => {
  // ensure seed input shows actual seedUsed
  const seedInput = document.getElementById('seedInput');
  if (seedInput) seedInput.value = String(current.seedUsed);
});
</script>
</body>
</html>
