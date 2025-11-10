<!doctype html>
<html lang="es">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>🎁 Intercambio — Familia Juárez</title>
  <style>
    body { font-family: system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial; max-width:720px; margin:auto; padding:18px; }
    h1 { margin-top: 0; }
    label, p.note { display:block; margin-top:10px; }
    input[type="text"], button { font-size:16px; padding:10px; width:100%; box-sizing:border-box; margin-top:6px; }
    #result { font-size:20px; margin-top:18px; font-weight:600; }
    table { border-collapse:collapse; width:100%; margin-top:12px; }
    td, th { border:1px solid #ccc; padding:8px; text-align:left; }
    #adminBox { display:none; background:#f8f8f8; padding:10px; border-radius:6px; margin-top:12px; }
    .small { font-size:13px; color:#555; }
    .danger { color:#a00; }
  </style>
</head>
<body>
  <h1>🎁 Intercambio — Familia Juárez</h1>
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
    <p class="small"></p>
    <table id="adminTable" aria-label="Tabla de asignaciones">
      <thead><tr><th>Persona</th><th>Le toca regalar a</th></tr></thead>
      <tbody></tbody>
    </table>
    <p class="small danger">Nota: la asignación es fija — no se regenera.</p>
  </div>

<script>
/* === Lista de participantes (19) y asignación fija (giver -> receiver) === */
const MAPPING = {
  "Lupe": "Oscar",
  "Joel": "Jorge Ángel",
  "Abuelita": "Chary",
  "Jorge Cora": "Karim",
  "Jorge Ángel": "Sara",
  "Tere": "Germán Chico",
  "Sofi": "Kael",
  "Germán Grande": "Sofi",
  "Germán Chico": "Víctor",
  "Rosario": "Janet",
  "Fer": "Neithan",
  "Sara": "Rubén",
  "Víctor": "Fer",
  "Chary": "Abuelita",
  "Rubén": "Joel",
  "Karim": "Jorge Cora",
  "Kael": "Germán Grande",
  "Neithan": "Lupe",
  "Oscar": "Tere",
  "Janet": "Rosario"
};

/* Build canonical NAMES list from mapping keys for suggestions and validation */
const NAMES = Object.keys(MAPPING).slice(); // array of giver names

/* Populate datalist for suggestions */
const namesList = document.getElementById('namesList');
for (const n of NAMES) {
  const opt = document.createElement('option');
  opt.value = n;
  namesList.appendChild(opt);
}

/* Normalize helper: remove extra spaces and compare case-insensitive */
function findCanonicalName(input) {
  if (!input) return null;
  const trimmed = input.trim();
  // exact match
  if (NAMES.includes(trimmed)) return trimmed;
  // case-insensitive match
  const lower = trimmed.toLowerCase();
  const found = NAMES.find(n => n.toLowerCase() === lower);
  if (found) return found;
  // try removing diacritics for match (basic)
  function removeDiacritics(s) {
    return s.normalize('NFD').replace(/[\u0300-\u036f]/g, '');
  }
  const noDiacritics = removeDiacritics(trimmed).toLowerCase();
  return NAMES.find(n => removeDiacritics(n).toLowerCase() === noDiacritics) || null;
}

/* Show result on button click */
document.getElementById('viewBtn').addEventListener('click', () => {
  const input = document.getElementById('nameInput').value;
  const result = document.getElementById('result');
  result.textContent = '';
  if (!input || input.trim() === '') {
    result.textContent = "Por favor escribe tu nombre.";
    return;
  }
  const name = findCanonicalName(input);
  if (!name) {
    result.textContent = "Nombre no reconocido. Revisa que esté escrito exactamente como en la lista.";
    return;
  }
  // show the assigned receiver
  const receiver = MAPPING[name];
  result.textContent = `Te toca regalar a: ${receiver}`;
});

/* Admin display if ?admin=familia-juarez */
function getParam(name) {
  return new URLSearchParams(location.search).get(name);
}
if (getParam('admin') === 'familia-juarez') {
  document.getElementById('adminBox').style.display = 'block';
  renderAdminTable();
}
function renderAdminTable() {
  const tbody = document.querySelector('#adminTable tbody');
  tbody.innerHTML = '';
  for (const giver of NAMES) {
    const tr = document.createElement('tr');
    const td1 = document.createElement('td'); td1.textContent = giver;
    const td2 = document.createElement('td'); td2.textContent = MAPPING[giver] || '';
    tr.appendChild(td1); tr.appendChild(td2);
    tbody.appendChild(tr);
  }
}

/* Accessibility: allow Enter key in input to trigger view */
document.getElementById('nameInput').addEventListener('keydown', (e) => {
  if (e.key === 'Enter') {
    e.preventDefault();
    document.getElementById('viewBtn').click();
  }
});
</script>
</body>
</html>
