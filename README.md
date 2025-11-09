<h1>🎁 Amigo Secreto - Familia Juárez</h1>
<p>Selecciona tu nombre para ver a quién te toca regalar.</p>

<select id="nombre"></select>
<br>
<button onclick="mostrarResultado()">Ver mi Amigo Secreto</button>

<div id="resultado" class="card" style="display:none;"></div>
<div id="adminPanel" style="display:none;"></div>

<script>
// Lista de participantes
const nombres = [
"Lupe","Jorge Cora","Jorge Ángel","Tere","Sofi","Germán Grande","Germán Chico",
"Rosario","Fer","Sara","Víctor","Chary","Rubén","Karim","Kael","Neithan",
"Oscar","Abuelita","Joel"
];

// Sorteo fijo generado:
const asignaciones = {
"Lupe": "Neithan",
"Jorge Cora": "Rosario",
"Jorge Ángel": "Sara",
"Tere": "Karim",
"Sofi": "Tere",
"Germán Grande": "Joel",
"Germán Chico": "Víctor",
"Rosario": "Rubén",
"Fer": "Germán Grande",
"Sara": "Germán Chico",
"Víctor": "Jorge Ángel",
"Chary": "Kael",
"Rubén": "Sofi",
"Karim": "Abuelita",
"Kael": "Jorge Cora",
"Neithan": "Lupe",
"Oscar": "Chary",
"Abuelita": "Fer",
"Joel": "Oscar"
};

// Llenar el selector
const selector = document.getElementById("nombre");
nombres.forEach(n => {
  const opt = document.createElement("option");
  opt.value = opt.textContent = n;
  selector.appendChild(opt);
});

function mostrarResultado() {
  const nombre = selector.value;
  const quien = asignaciones[nombre];
  document.getElementById("resultado").style.display = "block";
  document.getElementById("resultado").innerHTML =
    `<h2>${nombre}</h2><p>Te toca regalar a:</p><h3>${quien}</h3>`;
}

// Panel del organizador:
const params = new URLSearchParams(window.location.search);
if (params.get("admin") === "familia-juarez") {
  let html = "<h2>Panel del Organizador</h2><table><tr><th>Persona</th><th>Regala a</th></tr>";
  for (let p in asignaciones) html += `<tr><td>${p}</td><td>${asignaciones[p]}</td></tr>`;
  html += "</table>";
  document.getElementById("adminPanel").innerHTML = html;
  document.getElementById("adminPanel").style.display = "block";
}
</script>

</body>
</html>
