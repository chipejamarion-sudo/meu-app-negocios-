# index.html  
  
<!DOCTYPE html>  
<html lang="pt">  
<head>  
  <meta charset="UTF-8">  
  <title>Encontrar Negócios</title>  
  <style>  
    body {  
      font-family: Arial;  
      background: #111;  
      color: #fff;  
      text-align: center;  
      padding: 20px;  
    }  
    input, select, button {  
      padding: 10px;  
      margin: 10px;  
      border-radius: 8px;  
      border: none;  
    }  
    button {  
      background: #00ff88;  
      cursor: pointer;  
    }  
    .result {  
      margin-top: 20px;  
      background: #222;  
      padding: 10px;  
      border-radius: 10px;  
    }  
  </style>  
</head>  
<body>  
  
<h1>🔍 Encontrar Negócios</h1>  
  
<input type="text" id="location" placeholder="Digite sua cidade">  
<br>  
  
<select id="type">  
  <option value="restaurant">Restaurante</option>  
  <option value="hospital">Hospital</option>  
  <option value="school">Escola</option>  
  <option value="gym">Academia</option>  
</select>  
<br>  
  
<button onclick="buscar()">Buscar</button>  
  
<div id="results"></div>  
  
<script>  
async function buscar() {  
  let location = document.getElementById("location").value;  
  let type = document.getElementById("type").value;  
  
  let url = `https://nominatim.openstreetmap.org/search?format=json&q=${location}`;  
  let geo = await fetch(url).then(res => res.json());  
  
  if (geo.length === 0) {  
    alert("Local não encontrado!");  
    return;  
  }  
  
  let lat = geo[0].lat;  
  let lon = geo[0].lon;  
  
  let overpass = `  
    [out:json];  
    node["amenity"="${type}"](around:3000,${lat},${lon});  
    out;  
  `;  
  
  let res = await fetch("https://overpass-api.de/api/interpreter", {  
    method: "POST",  
    body: overpass  
  });  
  
  let data = await res.json();  
  
  let resultsDiv = document.getElementById("results");  
  resultsDiv.innerHTML = "";  
  
  data.elements.slice(0,10).forEach(place => {  
    let name = place.tags.name || "Sem nome";  
    resultsDiv.innerHTML += `  
      <div class="result">  
        📍 ${name}  
      </div>  
    `;  
  });  
}  
</script>  
  
</body>  
</html>  
