<html lang="pt-BR">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Orçamento - Collins Bartenders</title>
  <style>
    body {
      font-family: 'Georgia', serif;
      background-color: #f4f4f4;
      padding: 40px;
      max-width: 800px;
      margin: auto;
      color: #333;
    }

    h1 {
      color: #000;
      margin-bottom: 20px;
    }

    label {
      font-weight: bold;
      margin-top: 15px;
      display: block;
    }

    input, select, textarea {
      width: 100%;
      padding: 10px;
      margin-top: 5px;
      margin-bottom: 10px;
      border-radius: 5px;
      border: 1px solid #ccc;
      font-size: 14px;
    }

    .radio-group label {
      display: block;
      margin-bottom: 5px;
    }

    button {
      background-color: #000;
      color: #fff;
      padding: 10px 25px;
      border: none;
      border-radius: 5px;
      cursor: pointer;
      margin-top: 10px;
      margin-right: 10px;
    }

    #orcamentoResultado {
      background-color: #fff;
      border: 1px solid #ccc;
      padding: 20px;
      margin-top: 20px;
      border-radius: 5px;
      display: none;
    }

    #justificativa-container {
      margin-top: 20px;
      display: none;
    }

    ul {
      margin-top: 10px;
      padding-left: 20px;
    }
  </style>
</head>
<body>
  <h1>Solicitação de Orçamento - Collins Bartenders</h1>
  <form id="orcamentoForm">
    <label for="nome">Nome:</label>
    <input type="text" id="nome" required>

    <label for="data">Data do Evento:</label>
    <input type="date" id="data" required>

    <label for="tipoEvento">Tipo de Evento:</label>
    <input type="text" id="tipoEvento" required>

    <label for="localEvento">Local do Evento:</label>
    <input type="text" id="localEvento" required>

    <label for="convidados">Número de Convidados:</label>
    <input type="number" id="convidados" required>

    <label for="pacote">Pacote:</label>
    <select id="pacote" required>
      <option value="Básico">Básico</option>
      <option value="Padrão">Padrão</option>
      <option value="Premium">Premium</option>
    </select>

    <label>Tipo de Drinks:</label>
    <div class="radio-group">
      <label><input type="radio" name="tipoDrink" value="Com Álcool"> Com Álcool</label>
      <label><input type="radio" name="tipoDrink" value="Sem Álcool"> Sem Álcool</label>
      <label><input type="radio" name="tipoDrink" value="Ambos"> Ambos</label>
    </div>

    <label for="outrasBebidas">Haverá outras bebidas no local?</label>
    <select id="outrasBebidas">
      <option value="Sim">Sim</option>
      <option value="Não">Não</option>
    </select>

    <button type="button" onclick="gerarOrcamento()">Gerar Orçamento</button>

    <div id="orcamentoResultado"></div>

    <button type="button" onclick="enviarWhatsApp(true)">Aceitar Proposta</button>
    <button type="button" onclick="mostrarJustificativa()">Recusar Proposta</button>

    <div id="justificativa-container">
      <label for="justificativa">O que podemos melhorar?</label>
      <textarea id="justificativa" rows="4"></textarea>
      <button type="button" onclick="enviarWhatsApp(false)">Enviar Justificativa</button>
    </div>
  </form>

  <script>
    function getTipoDrink() {
      const radios = document.getElementsByName("tipoDrink");
      for (const radio of radios) {
        if (radio.checked) return radio.value;
      }
      return "Não especificado";
    }

    function gerarOrcamento() {
      const nome = document.getElementById("nome").value;
      const data = document.getElementById("data").value;
      const tipoEvento = document.getElementById("tipoEvento").value;
      const localEvento = document.getElementById("localEvento").value;
      const convidados = parseInt(document.getElementById("convidados").value);
      const pacote = document.getElementById("pacote").value;
      const tipoDrink = getTipoDrink();
      const outrasBebidas = document.getElementById("outrasBebidas").value;

      const tabelaValores = {
        "Básico": [1100, 1400, 1600, 1800, 2000, 2400, 2800],
        "Padrão": [1300, 1600, 1800, 2000, 2200, 2600, 3200],
        "Premium": [1900, 2600, 2800, 3000, 3300, 3900, 4700]
      };
      const faixas = [30, 50, 70, 90, 110, 130, 150];
      let valor = 0;
      for (let i = 0; i < faixas.length; i++) {
        if (convidados <= faixas[i]) {
          valor = tabelaValores[pacote][i];
          break;
        }
      }

      let bartenders = 1;
      let barbacks = 0;

      if (pacote === "Básico") {
        if (convidados > 30 && convidados <= 110) bartenders = 2;
        else if (convidados <= 130) bartenders = 3;
        else if (convidados <= 150) bartenders = 4;
      } else {
        if (convidados <= 30) { bartenders = 1; barbacks = 1; }
        else if (convidados <= 110) { bartenders = 2; barbacks = 1; }
        else if (convidados <= 130) { bartenders = 3; barbacks = 1; }
        else if (convidados <= 150) { bartenders = 4; barbacks = 2; }
      }

      const copos = (pacote === "Básico") ? "Copos descartáveis" : "Copos/taças de vidro e canecas";

      const resultado = `
        <h3>Orçamento Gerado:</h3>
        <p><strong>Nome:</strong> ${nome}</p>
        <p><strong>Data:</strong> ${data}</p>
        <p><strong>Tipo de Evento:</strong> ${tipoEvento}</p>
        <p><strong>Local do Evento:</strong> ${localEvento}</p>
        <p><strong>Número de Convidados:</strong> ${convidados}</p>
        <p><strong>Pacote:</strong> ${pacote}</p>
        <p><strong>Tipo de Drinks:</strong> ${tipoDrink}</p>
        <p><strong>Outras bebidas no local:</strong> ${outrasBebidas}</p>
        <p><strong>Valor:</strong> R$ ${valor.toFixed(2).replace('.', ',')}</p>
        <ul>
          <li>Até 8 drinks</li>
          <li>4 horas Open Bar</li>
          <li>${bartenders} Bartender(s)</li>
          ${barbacks > 0 ? `<li>${barbacks} Barback(s)</li>` : ''}
          <li>${copos}</li>
        </ul>
      `;

      document.getElementById("orcamentoResultado").innerHTML = resultado;
      document.getElementById("orcamentoResultado").style.display = "block";
    }

    function montarMensagem() {
      const justificativa = document.getElementById("justificativa").value;
      const texto = document.getElementById("orcamentoResultado").innerText;
      return encodeURIComponent(justificativa ? texto + "\n\nMotivo da recusa: " + justificativa : texto);
    }

    function enviarWhatsApp(aceito) {
      const numeroDestino = "5565992887213";
      const mensagem = montarMensagem();
      const link = `https://wa.me/${numeroDestino}?text=${mensagem}`;
      window.open(link, "_blank");
    }

    function mostrarJustificativa() {
      document.getElementById("justificativa-container").style.display = "block";
    }
  </script>
</body>
</html>
