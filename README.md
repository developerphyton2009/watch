<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <title>OriOS Clock v10.0</title>
  <style>
    body {
      font-family: 'Segoe UI', sans-serif;
      margin: 0;
      padding: 40px;
      text-align: center;
      transition: background-color 0.5s, color 0.5s;
    }

    body.tema-oscuro {
      background-color: #000;
      color: white;
    }

    body.tema-claro {
      background-color: #fff;
      color: black;
    }

    .clock {
      font-size: 80px;
      font-weight: bold;
      transition: color 0.3s, font-family 0.3s;
    }

    .date {
      font-size: 30px;
      margin-top: 10px;
      transition: color 0.3s, font-family 0.3s;
    }

    .ampm-box {
      border-radius: 12px;
      width: 100px;
      margin: 20px auto;
      padding: 10px 0;
      transition: background-color 0.5s;
      background-color: #eee;
    }

    .tema-oscuro .ampm-box {
      background-color: #222;
    }

    .ampm {
      font-size: 24px;
      font-weight: bold;
      opacity: 0.3;
      transition: color 0.3s, font-family 0.3s;
    }

    .layout {
      display: flex;
      justify-content: center;
      align-items: flex-start;
      gap: 40px;
      flex-wrap: wrap;
      margin-top: 50px;
    }

    .zona-box, .panel {
      border-radius: 15px;
      padding: 20px;
      width: 250px;
      transition: background-color 0.5s, color 0.5s;
    }

    .zona-box {
      text-align: left;
    }

    .tema-claro .zona-box,
    .tema-claro .panel {
      background-color: #f0f0f0;
      color: black;
    }

    .tema-oscuro .zona-box,
    .tema-oscuro .panel {
      background-color: #333;
      color: white;
    }

    .zonas-scroll label {
      display: flex;
      align-items: center;
      margin-bottom: 10px;
      cursor: pointer;
    }

    .zonas-scroll input[type="radio"] {
      margin-right: 10px;
    }

    .panel-title {
      font-size: 16px;
      font-weight: bold;
      margin-bottom: 10px;
      text-align: left;
    }

    .color-options, .font-options {
      display: flex;
      justify-content: start;
      gap: 10px;
      margin: 10px 0;
      flex-wrap: wrap;
    }

    .color-circle {
      width: 20px;
      height: 20px;
      border-radius: 50%;
      cursor: pointer;
      border: 2px solid white;
    }

    .font-button {
      background-color: #ccc;
      border: none;
      padding: 5px 10px;
      border-radius: 5px;
      cursor: pointer;
    }

    .more-settings {
      font-size: 14px;
      margin-top: 10px;
      text-align: left;
    }

    .more-settings label {
      display: block;
      margin: 5px 0;
    }
  </style>
</head>
<body>
  <div class="clock" id="clock">--:--:--</div>
  <div class="date" id="date">Cargando fecha...</div>

  <div class="ampm-box">
    <div class="ampm" id="am">AM</div>
    <div class="ampm" id="pm">PM</div>
  </div>

  <div class="layout">
    <div class="zona-box">
      <div class="panel-title">Zona horaria</div>
      <div class="zonas-scroll">
        <label><input type="radio" name="zona" value="Europe/Madrid" onclick="setZonaPersonalizada(this)" checked> Spain</label>
        <label><input type="radio" name="zona" value="America/New_York" onclick="setZonaPersonalizada(this)"> United States</label>
        <label><input type="radio" name="zona" value="Asia/Shanghai" onclick="setZonaPersonalizada(this)"> China</label>
        <label><input type="radio" name="zona" value="Europe/Moscow" onclick="setZonaPersonalizada(this)"> Russia</label>
        <label><input type="radio" name="zona" value="Europe/Bucharest" onclick="setZonaPersonalizada(this)"> Romania</label>
        <label><input type="radio" name="zona" value="Asia/Karachi" onclick="setZonaPersonalizada(this)"> Pakistan</label>
        <label><input type="radio" name="zona" value="Asia/Dubai" onclick="setZonaPersonalizada(this)"> Dubai</label>
      </div>
    </div>

    <div class="panel">
      <div class="panel-title">Panel control</div>

      <div class="panel-title">Color</div>
      <div class="color-options">
        <div class="color-circle" style="background:red" onclick="changeColor('red')"></div>
        <div class="color-circle" style="background:green" onclick="changeColor('lime')"></div>
        <div class="color-circle" style="background:blue" onclick="changeColor('blue')"></div>
        <div class="color-circle" style="background:cyan" onclick="changeColor('cyan')"></div>
        <div class="color-circle" style="background:orange" onclick="changeColor('orange')"></div>
        <div class="color-circle" style="background:gray" onclick="changeColor('gray')"></div>
        <div class="color-circle" style="background:white" onclick="changeColor('white')"></div>
        <div class="color-circle" style="background:yellow" onclick="changeColor('yellow')"></div>
        <div class="color-circle" style="background:magenta" onclick="changeColor('magenta')"></div>
      </div>

      <div class="panel-title">Fonts</div>
      <div class="font-options">
        <button class="font-button" onclick="changeFont('Segoe UI')">Segoe UI</button>
        <button class="font-button" onclick="changeFont('Courier New')">Courier</button>
        <button class="font-button" onclick="changeFont('Arial')">Arial</button>
        <button class="font-button" onclick="changeFont('Georgia')">Georgia</button>
      </div>

      <div class="more-settings">
        <label><input type="checkbox" id="showSeconds" checked> Show Seconds</label>
        <label><input type="checkbox" id="autoTheme" checked disabled> Automatic Theme</label>
      </div>

      <div class="version">Clock systems v10.0</div>
    </div>
  </div>

  <script>
    let zonaSeleccionada = "Europe/Madrid";

    function setZonaPersonalizada(radio) {
      zonaSeleccionada = radio.value;
      aplicarTemaAutomatico();
    }

    function updateClock() {
      const now = new Date();
      const options = {
        timeZone: zonaSeleccionada,
        hour: '2-digit',
        minute: '2-digit',
        second: document.getElementById("showSeconds").checked ? '2-digit' : undefined
      };

      const hora = now.toLocaleTimeString('es-ES', options);
      document.getElementById("clock").textContent = hora;

      const day = now.toLocaleDateString("es-ES", {
        timeZone: zonaSeleccionada,
        day: 'numeric',
        month: 'long',
        year: 'numeric'
      });

      document.getElementById("date").textContent = day;

      const hour = now.toLocaleString("en-US", { timeZone: zonaSeleccionada, hour: 'numeric', hour12: false });
      document.getElementById("am").style.opacity = hour >= 12 ? "0.3" : "1";
      document.getElementById("pm").style.opacity = hour >= 12 ? "1" : "0.3";
    }

    function aplicarTemaAutomatico() {
      const hora = new Date().toLocaleString("en-US", { timeZone: zonaSeleccionada, hour: "numeric", hour12: false });
      if (hora >= 7 && hora < 19) {
        document.body.classList.add('tema-claro');
        document.body.classList.remove('tema-oscuro');
      } else {
        document.body.classList.add('tema-oscuro');
        document.body.classList.remove('tema-claro');
      }
    }

    function changeColor(color) {
      document.getElementById("clock").style.color = color;
      document.getElementById("date").style.color = color;
      document.getElementById("am").style.color = color;
      document.getElementById("pm").style.color = color;
    }

    function changeFont(font) {
      document.getElementById("clock").style.fontFamily = font;
      document.getElementById("date").style.fontFamily = font;
      document.getElementById("am").style.fontFamily = font;
      document.getElementById("pm").style.fontFamily = font;
    }

    setInterval(updateClock, 1000);
    setInterval(aplicarTemaAutomatico, 60000);
    updateClock();
    aplicarTemaAutomatico();
  </script>
</body>
</html>
