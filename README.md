<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<title>Amigo Secreto Familia Juárez</title>
<style>
    body { font-family: Arial, sans-serif; background: #faf5ef; padding: 40px; text-align: center; }
    h1 { color: #d18b52; }
    select, button { padding: 10px; margin-top: 15px; font-size: 16px; }
    .card {
        margin-top: 30px;
        background: #fff;
        padding: 25px;
        border-radius: 12px;
        display: inline-block;
        border: 2px solid #d18b52;
    }
</style>
</head>
<body>

<h1>🎁 Amigo Secreto - Familia Juárez</h1>
<p>Selecciona tu nombre para ver a quién te toca regalar.</p>

<select id="nombre"></select>
<br>
<button onclick="mostrarResultado()">Ver mi Amigo Secreto</button>

<div id="resultado" class="card" style="display:none;"></div>
<div id="adminPanel" style="margin-top:40px; display:none;"></div>

<script>
// Nombres de la familia:
const nombres = [
  "German",
  "Joel",
  "Victor",
  "Mamá",
  "Papá",
  "Alejandra",
  "Carmen"
];

// Sorteo fijo para que no cambie:
const asignaciones = {
  "German": "Carmen",
  "Joel": "Papá",
  "Victor": "German",
  "Mamá": "Joel",
  "Papá": "Alejandra",
  "Alejandra": "Victor",
  "Carmen": "Mamá"
};

// Llenar el dropdown
const selector = document.getElementById("nombre");
nombres.forEach(n => {
  let opt = document.createElement("option");
  opt.value = n;
  opt.innerHTML = n;
  selector.appendChild(opt);
});

function mostrarResultado() {
  const nombre = selector.value;
  const quien = asignaciones[nombre];
  document.getElementById("resultado").style.display = "block";
  document.getElementById("resultado").innerHTML = `<h2>${nombre}</h2><p>Te toca regalar a:</p><h3>${quien}</h3>`;
}

// Panel admin
const params = new URLSearchParams(window.location.search);
if (params.get("admin") === "familia-juarez") {
  let adminHTML = "<h2>Panel del Organizador</h2><table style='margin:auto; text-align:left;'><tr><th>Persona</th><th>Regala a</th></tr>";
  for (let p in asignaciones) {
    adminHTML += `<tr><td>${p}</td><td>${asignaciones[p]}</td></tr>`;
  }
  adminHTML += "</table>";
  document.getElementById("adminPanel").innerHTML = adminHTML;
  document.getElementById("adminPanel").style.display = "block";
}
</script>

</body>
</html>
