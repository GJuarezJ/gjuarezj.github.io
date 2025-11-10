<!doctype html>
<html lang="es">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>🎁Intercambio — Familia Juárez</title>
  <style>
    body { font-family: system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial; max-width:720px; margin:auto; padding:18px; }
    h1 { margin-top: 0; }
    .small { font-size:13px; color:#555; margin-top:8px; }
    #closed { padding:16px; background:#f3f3f3; border-radius:8px; }
    table { border-collapse:collapse; width:100%; margin-top:12px; }
    td, th { border:1px solid #ccc; padding:6px 8px; text-align:left; }
    #adminBox { display:none; background:#fff; padding:12px; border-radius:6px; margin-top:12px; box-shadow: 0 1px 3px rgba(0,0,0,0.06); }
    code { background:#eee; padding:2px 6px; border-radius:4px; }
  </style>
</head>
<body>
  <h1>🎁Intercambio — Familia Juárez</h1>

  <!-- Mensaje público (visible para todos los que no son admin) -->
  <div id="publicView">
    <div id="closed">
      <strong>Sorteo cerrado</strong>
      <p class="small">El acceso público para ver a quién le toca regalar ya fue desactivado. Si necesitas ver la tabla completa, utiliza el enlace de administrador.</p>
      <p class="small">Enlace público (para compartir con todos): <code>https://gjuarezj.github.io/?seed=123456</code></p>
    </div>
  </div>

  <!-- Vista de administrador (solo con ?admin=familia-juarez&seed=123456) -->
  <div id="adminBox" aria-hidden="true">
    <h2>Vista del organizador</h2>
    <p class="small">Estás viendo la tabla porque abriste la página con <code>?admin=familia-juarez&seed=123456</code>.</p>

    <table aria-label="Tabla de asignaciones">
      <thead><tr><th>Persona</th><th>Le toca regalar a</th></tr></thead>
      <tbody id="adminTbody"></tbody>
    </table>

    <p class="small" style="margin-top:8px">Enlace administrador (cópialo): <code>https://gjuarezj.github.io/?admin=familia-juarez&seed=123456</code></p>
  </div>

<script>
/* ======= Lista de nombres (orden y opciones de selección) ======= */
const NAMES = [
  "Lupe","Joel","Abuelita","Jorge Cora","Jorge Ángel","Janet","Tere","Sofi",
  "Germán Grande","Germán Chico","Rosario","Fer","Sara","Víctor","Chary",
  "Rubén","Karim","Kael","Neithan","Oscar"
];

/* ======= Mapeo fijo que proporcionaste (giver -> receiver) ======= */
const FIXED_MAPPING = {
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

/* ======= Parámetros esperados ======= */
const REQUIRED_SEED = "123456";
const ADMIN_KEY = "familia-juarez";

/* ======= Utilidades para leer URL params ======= */
function getParam(name) {
  return new URLSearchParams(window.location.search).get(name);
}

/* ======= Lógica de visibilidad ======= */
const seedParam = getParam('seed');
const adminParam = getParam('admin');

// Mostrar admin sólo si ambos parámetros coinciden exactamente
const isAdmin = (adminParam === ADMIN_KEY && seedParam === REQUIRED_SEED);

if (isAdmin) {
  // mostrar adminBox y ocultar la vista pública
  document.getElementById('adminBox').style.display = 'block';
  document.getElementById('adminBox').setAttribute('aria-hidden', 'false');
  document.getElementById('publicView').style.display = 'none';

  // renderizar tabla con FIXED_MAPPING en el orden NAMES
  const tbody = document.getElementById('adminTbody');
  tbody.innerHTML = "";
  for (const giver of NAMES) {
    const tr = document.createElement('tr');
    const td1 = document.createElement('td'); td1.textContent = giver;
    const td2 = document.createElement('td'); td2.textContent = FIXED_MAPPING[giver] || "(sin asignar)";
    tr.appendChild(td1); tr.appendChild(td2);
    tbody.appendChild(tr);
  }
} else {
  // no admin: mantener sólo el mensaje "Sorteo cerrado"
  document.getElementById('adminBox').style.display = 'none';
  document.getElementById('publicView').style.display = 'block';
}
</script>
</body>
</html>



