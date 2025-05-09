# Compucoin-


<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <title>Compucoin Game</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      background: #f0f0f0;
      text-align: center;
      margin: 0;
      padding: 20px;
    }
    .profile, .game, .transfer, .admin-panel {
      background: white;
      margin: 20px auto;
      padding: 20px;
      border-radius: 12px;
      width: 90%;
      max-width: 500px;
      box-shadow: 0 0 10px #ccc;
    }
    input, button {
      padding: 8px;
      margin: 5px;
    }
    #result {
      font-size: 24px;
      margin-top: 10px;
    }
    .hidden {
      display: none;
    }
  </style>
</head>
<body>

  <h1>Compucoin Game</h1>

  <!-- Perfil -->
  <div class="profile">
    <h2>Tu Perfil</h2>
    <input type="text" id="playerName" placeholder="Tu nombre"><br>
    <input type="file" id="avatarInput"><br>
    <img id="avatarPreview" src="" alt="Avatar" style="width:100px; border-radius:50%; display:none;"><br>
    <button onclick="saveProfile()">Guardar Perfil</button>
    <p id="saldoText"></p>
  </div>

  <!-- Ruleta -->
  <div class="game">
    <h2>Ruleta de Compucoins</h2>
    <input type="number" id="bet" placeholder="Apuesta (Compucoins)">
    <button onclick="spin()">Girar</button>
    <p id="result"></p>
    <audio id="winSound" src="https://assets.mixkit.co/sfx/preview/mixkit-winning-chimes-2012.mp3"></audio>
    <audio id="loseSound" src="https://assets.mixkit.co/sfx/preview/mixkit-sad-game-over-trombone-471.mp3"></audio>
  </div>

  <!-- Transferencias -->
  <div class="transfer">
    <h2>Transferir Compucoins</h2>
    <input type="text" id="targetName" placeholder="Nombre del receptor">
    <input type="number" id="amountToSend" placeholder="Cantidad">
    <button onclick="transferCoins()">Enviar</button>
    <p id="transferStatus"></p>
  </div>

  <!-- Admin Access -->
  <div>
    <input type="password" id="adminPass" placeholder="Contraseña admin">
    <button onclick="checkAdmin()">Entrar admin</button>
  </div>

  <!-- Panel Admin -->
  <div class="admin-panel hidden" id="adminPanel">
    <h2>Zona de Administrador</h2>
    <input type="text" id="giveName" placeholder="Nombre del jugador">
    <input type="number" id="giveAmount" placeholder="Monedas a dar">
    <button onclick="giveCoins()">Dar monedas</button>
    <p id="adminMsg"></p>
  </div>

  <script>
    // Variables
    let saldo = 10;
    const fechaHoy = new Date().toDateString();

    // Cargar perfil al iniciar
    window.onload = () => {
      const savedDate = localStorage.getItem("lastDate");
      if (savedDate !== fechaHoy) {
        localStorage.setItem("saldo", "10");
        localStorage.setItem("lastDate", fechaHoy);
      }
      saldo = parseInt(localStorage.getItem("saldo")) || 10;
      const name = localStorage.getItem("name");
      const avatar = localStorage.getItem("avatar");
      if (name) document.getElementById("playerName").value = name;
      if (avatar) {
        document.getElementById("avatarPreview").src = avatar;
        document.getElementById("avatarPreview").style.display = "block";
      }
      updateSaldo();
    };

    // Guardar perfil
    function saveProfile() {
      const name = document.getElementById("playerName").value;
      const file = document.getElementById("avatarInput").files[0];
      if (name) localStorage.setItem("name", name);
      if (file) {
        const reader = new FileReader();
        reader.onload = () => {
          localStorage.setItem("avatar", reader.result);
          document.getElementById("avatarPreview").src = reader.result;
          document.getElementById("avatarPreview").style.display = "block";
        };
        reader.readAsDataURL(file);
      }
      updateSaldo();
    }

    // Actualizar saldo en pantalla
    function updateSaldo() {
      document.getElementById("saldoText").innerText = `Saldo: ${saldo} Compucoins`;
      localStorage.setItem("saldo", saldo);
    }

    // Juego de ruleta
    function spin() {
      const apuesta = parseInt(document.getElementById("bet").value);
      const result = document.getElementById("result");
      if (!apuesta || apuesta <= 0 || apuesta > saldo) {
        result.innerText = "Apuesta no válida";
        return;
      }
      const win = Math.random() < 0.5;
      if (win) {
        saldo += apuesta;
        result.innerText = "¡You win!";
        document.getElementById("winSound").play();
        confettiEffect();
      } else {
        saldo -= apuesta;
        result.innerText = "You lost...";
        document.getElementById("loseSound").play();
      }
      updateSaldo();
    }

    // Transferir monedas
    function transferCoins() {
      const to = document.getElementById("targetName").value;
      const amount = parseInt(document.getElementById("amountToSend").value);
      const status = document.getElementById("transferStatus");
      if (!to || !amount || amount <= 0 || amount > saldo) {
        status.innerText = "Transferencia inválida.";
        return;
      }
      saldo -= amount;
      updateSaldo();
      status.innerText = `Transferiste ${amount} a ${to}.`;
    }

    // Admin
    function checkAdmin() {
      const pass = document.getElementById("adminPass").value;
      if (pass === "compuadmin2025") {
        document.getElementById("adminPanel").classList.remove("hidden");
      } else {
        alert("Contraseña incorrecta.");
      }
    }

    function giveCoins() {
      const name = document.getElementById("giveName").value;
      const amount = parseInt(document.getElementById("giveAmount").value);
      if (!name || !amount || amount <= 0) {
        document.getElementById("adminMsg").innerText = "Datos inválidos.";
        return;
      }
      saldo += amount;
      updateSaldo();
      document.getElementById("adminMsg").innerText = `Diste ${amount} a ${name}.`;
    }

    // Confeti básico
    function confettiEffect() {
      const emojis = ["✨", "🎉", "⭐", "💥"];
      for (let i = 0; i < 30; i++) {
        const conf = document.createElement("div");
        conf.innerText = emojis[Math.floor(Math.random() * emojis.length)];
        conf.style.position = "fixed";
        conf.style.left = Math.random() * 100 + "vw";
        conf.style.top = Math.random() * 100 + "vh";
        conf.style.fontSize = "24px";
        conf.style.animation = "fall 1.5s ease-out forwards";
        document.body.appendChild(conf);
        setTimeout(() => conf.remove(), 1500);
      }
    }
  </script>

</body>
</html>
