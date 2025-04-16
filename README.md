<!DOCTYPE html>
<html lang="pt-br">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Painel de Arbitragem</title>
  <style>
    body { font-family: Arial, sans-serif; margin: 0; padding: 0; }
    .container { width: 100%; max-width: 600px; margin: 50px auto; padding: 20px; border: 1px solid #ccc; border-radius: 10px; background-color: #f9f9f9; }
    h1 { text-align: center; }
    label { display: block; margin: 10px 0 5px; }
    input[type="number"], input[type="text"], select { width: 100%; padding: 10px; margin-bottom: 10px; border: 1px solid #ccc; border-radius: 5px; }
    .result { margin-top: 20px; }
    .result p { font-size: 18px; }
    .no-opportunity { color: red; font-size: 18px; }
  </style>
</head>
<body>

  <div class="container">
    <h1>Painel de Arbitragem</h1>
    
    <label for="amount">Valor da operação (BTC)</label>
    <input type="number" id="amount" placeholder="Digite o valor em BTC" step="any">

    <label for="coin">Escolha a moeda</label>
    <select id="coin">
      <option value="BTC">BTC</option>
      <option value="ETH">ETH</option>
      <!-- Adicione mais opções conforme necessário -->
    </select>

    <label for="price1">Preço de Compra (Corretora 1)</label>
    <input type="number" id="price1" placeholder="Preço de compra na Corretora 1" step="any">

    <label for="price2">Preço de Venda (Corretora 2)</label>
    <input type="number" id="price2" placeholder="Preço de venda na Corretora 2" step="any">

    <div class="result">
      <p id="profit">Lucro: --</p>
      <p id="percent">Lucro Percentual: --</p>
      <p id="withdrawalAmount">Valor do Saque: --</p>
      <p id="noOpportunity" class="no-opportunity" style="display:none;">Não há oportunidade acima de 2% ou com redes diferentes.</p>
    </div>
  </div>

  <script>
    async function fetchPrices() {
      try {
        const response = await fetch('/api/get-prices');
        const data = await response.json();

        const binancePrice = parseFloat(data.binancePrice);
        const kuCoinPrice = parseFloat(data.kuCoinPrice);

        document.getElementById('price1').value = binancePrice;
        document.getElementById('price2').value = kuCoinPrice;

        calculateProfit();
      } catch (error) {
        console.error('Erro ao buscar preços:', error);
      }
    }

    function calculateProfit() {
      const amount = parseFloat(document.getElementById('amount').value);
      const price1 = parseFloat(document.getElementById('price1').value);
      const price2 = parseFloat(document.getElementById('price2').value);

      if (isNaN(amount) || isNaN(price1) || isNaN(price2)) {
        return;
      }

      const profit = (price2 - price1) * amount;
      const percent = ((price2 - price1) / price1) * 100;

      document.getElementById('profit').textContent = `Lucro: $${profit.toFixed(2)}`;
      document.getElementById('percent').textContent = `Lucro Percentual: ${percent.toFixed(2)}%`;

      const withdrawal = amount + profit;
      document.getElementById('withdrawalAmount').textContent = `Valor do Saque: $${withdrawal.toFixed(2)}`;

      if (percent > 2) {
        document.getElementById('noOpportunity').style.display = "none";
      } else {
        document.getElementById('noOpportunity').style.display = "block";
      }
    }

    window.onload = fetchPrices;
  </script>

</body>
</html>
