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
    td, th { border:1px solid #ccc; padding:6px 8px; text-align:left; }
    #adminBox { display:none; background:#f8f8f8; padding:10px; border-radius:6px; margin-top:12px; }
    .small { font-size:13px; color:#555; }
    .danger { color:#a00; }
  </style>
</head>
<body>
  <h1>🎁 Intercambio — Familia Juárez</h1>
  <p class="small">Abre este enlace desde WhatsApp en el teléfono. Escribe tu nombre exactamente como aparece en la lista y toca "Ver".</p>

  <label for="nameInput">Tu nombre</label>
  <input id="nameInput" type="text" placeholder="Escribe tu nombre (ej. Lupe)" autocomplete="off" />

  <div style="margin-top:8px;">
    <button id="viewBtn">Ver a quién le regalas</button>
  </div>

  <p id="result" aria-live="polite"></p>

  <hr/>

  <div id="adminBox" aria-hidden="true">
    <h3>Vista del organizador</h3>
    <p class="small">Abre con <code>?admin=familia-juarez</code> para ver la tabla completa.</p>
    <table id="adminTable" aria-label="Tabla de asignaciones">
      <thead><tr><th>Persona</th><th>Le toca regalar a</th></tr></thead>
      <tbody></tbody>
    </table>
    <p class="small danger">No compartas la URL con <code>?admin=familia-juarez</code> al grupo.</p>
  </div>

<script>
/* Lista en el orden que pediste (20) */
const NAMES = [
  "Lupe",
  "Joel",
  "Abuelita",
  "Jorge Cora",
  "Jorge Ángel",
  "Janet",
  "Tere",
  "Sofi",
  "Germán Grande",
  "Germán Chico",
  "Rosario",
  "Fer",
  "Sara",
  "Víctor",
  "Chary",
  "Rubén",
  "Karim",
  "Kael",
  "Neithan",
  "Oscar"
];

/* Mapeo fijo que compartiste: (columna izquierda -> columna derecha) */
const MAPPING = {
  "Lupe": "Oscar",
  "Joel": "Jorge Ángel",
  "Abuelita": "Chary",
  "Jorge Cora": "Karim",
  "Jorge Ángel": "Sara",
  "Janet": "Rosario",
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
  "Oscar": "Tere"
};

/* Normaliza texto para match case-insensitive y sin espacios extras */
function normalize(s){
  return String(s).trim().toLowerCase();
}

/* Build a lookup that maps normalized name -> canonical name */
const normalizedLookup = {};
for (const n of NAMES) normalizedLookup[normalize(n)] = n;

/* Mostrar resultado cuando el usuario escribe su nombre */
document.getElementById('viewBtn').addEventListener('click', () => {
  const raw = document.getElementById('nameInput').value;
  const resultEl = document.getElementById('result');
  resultEl.textContent = '';
  if (!raw || !raw.trim()) {
    resultEl.textContent = "Por favor escribe tu nombre.";
    return;
  }
  const key = normalize(raw);
  if (!(key in normalizedLookup)) {
    resultEl.textContent = "Nombre no reconocido. Revisa la lista o escribe exactamente tu nombre.";
    return;
  }
  const canonical = normalizedLookup[key];
  const receiver = MAPPING[canonical];
  if (!receiver) {
    resultEl.textContent = "Aún no hay asignación para tu nombre. Contacta al organizador.";
    return;
  }
  resultEl.textContent = `Te toca regalar a: ${receiver}`;
});

/* Mostrar vista admin solo si ?admin=familia-juarez */
function getParam(name){
  return new URLSearchParams(location.search).get(name);
}
if (getParam('admin') === 'familia-juarez') {
  const adminBox = document.getElementById('adminBox');
  adminBox.style.display = 'block';
  adminBox.setAttribute('aria-hidden', 'false');
  const tbody = document.querySelector('#adminTable tbody');
  tbody.innerHTML = '';
  for (const giver of NAMES) {
    const tr = document.createElement('tr');
    const td1 = document.createElement('td'); td1.textContent = giver;
    const td2 = document.createElement('td'); td2.textContent = (MAPPING[giver] || '—');
    tr.appendChild(td1); tr.appendChild(td2);
    tbody.appendChild(tr);
  }
}
</script>
</body>
</html>

