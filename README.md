<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Trading Demo Game</title>
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700&display=swap" rel="stylesheet">
  <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
  <script src="https://cdn.jsdelivr.net/npm/chartjs-plugin-zoom@2.0.1"></script>
  <style>
    * {
      box-sizing: border-box;
    }
    body {
      font-family: 'Inter', sans-serif;
      background-color: #0e1117;
      color: #e6e6e6;
      margin: 0;
      padding: 0;
    }
    .centered {
      display: flex;
      justify-content: center;
      align-items: center;
      height: 100vh;
      background-color: #0e1117;
    }
    .login-card {
      background-color: #1a1f2e;
      padding: 30px 40px;
      border-radius: 10px;
      box-shadow: 0 0 20px rgba(255, 255, 255, 0.05);
      text-align: center;
      max-width: 350px;
      width: 100%;
    }
    .login-card h2 {
      margin-bottom: 1.2rem;
      color: #ffcc00;
    }
    .login-card input {
      width: 100%;
      padding: 10px;
      margin: 10px 0;
      border: none;
      border-radius: 6px;
      font-size: 1rem;
    }
    .login-card button {
      background-color: #ffcc00;
      color: #000;
      padding: 10px;
      border: none;
      border-radius: 6px;
      font-weight: bold;
      font-size: 1rem;
      cursor: pointer;
      margin-top: 10px;
      width: 100%;
      box-shadow: 0 0 10px rgba(255, 204, 0, 0.4);
    }
    .login-card button:hover {
      background-color: #ffdb4d;
    }

    .app-container {
      padding: 20px;
      max-width: 1200px;
      margin: 0 auto;
    }

    .chat-feed {
      background-color: #1a1f2e;
      padding: 10px 15px;
      border-radius: 10px;
      margin-bottom: 20px;
      max-height: 150px;
      overflow-y: auto;
      font-size: 0.9rem;
    }
    .chat-message {
      margin-bottom: 6px;
    }
    .chat-user {
      color: #ffcc00;
      font-weight: bold;
    }

    .user-info, .account-details {
      margin-bottom: 1rem;
      background-color: #1a1f2e;
      padding: 1rem;
      border-radius: 10px;
    }

    .account-details p, .user-info p {
      margin: 4px 0;
    }

    .currency-cards {
      display: flex;
      flex-wrap: wrap;
      gap: 1rem;
      margin: 1.5rem 0;
    }

    .currency-card {
      flex: 1 1 220px;
      background-color: #1a1f2e;
      border-radius: 10px;
      padding: 1rem;
      box-shadow: 0 0 10px rgba(255,255,255,0.03);
    }

    .currency-card h3 {
      margin-top: 0;
      color: #ffcc00;
    }

    .currency-card p {
      margin: 0.5rem 0;
    }

    .currency-card button {
      background: #ffcc00;
      color: #000;
      border: none;
      padding: 8px 14px;
      font-weight: bold;
      border-radius: 5px;
      cursor: pointer;
      margin-top: 8px;
    }

    .time-buttons {
      margin: 20px 0;
      text-align: center;
    }
    .time-buttons button {
      margin: 5px;
      padding: 10px 16px;
      font-weight: bold;
      font-size: 1rem;
      background: #ffcc00;
      color: #000;
      border: none;
      border-radius: 6px;
      cursor: pointer;
      box-shadow: 0 0 8px rgba(255, 204, 0, 0.5);
    }

    .chart-container {
      background: repeating-linear-gradient(45deg, #1a1f2e, #1a1f2e 10px, #141924 10px, #141924 20px);
      padding: 20px;
      border-radius: 12px;
      margin-bottom: 2rem;
      overflow-x: auto;
    }

    .invest-buttons {
      display: flex;
      justify-content: center;
      gap: 1rem;
      margin-bottom: 2rem;
    }

    .invest-buttons button {
      padding: 12px 20px;
    }

    .highlight-profit { color: #00ff88; font-weight: bold; }
    .highlight-loss { color: #ff5f5f; font-weight: bold; }

    .modal {
      display: none;
      position: fixed;
      z-index: 10;
      left: 0; top: 0;
      width: 100%; height: 100%;
      background: rgba(0,0,0,0.6);
      justify-content: center;
      align-items: center;
    }

    .modal-content {
      background: #1a1f2e;
      padding: 20px;
      border-radius: 10px;
      max-width: 300px;
      width: 90%;
    }

    .modal-content h3 {
      margin-top: 0;
      color: #ffcc00;
    }

    .modal-content input {
      width: 100%;
      padding: 10px;
      margin: 12px 0;
      border: none;
      border-radius: 5px;
      font-size: 1rem;
    }

    .modal-content .actions {
      text-align: right;
    }

    .modal-content button {
      padding: 8px 14px;
      font-weight: bold;
      border: none;
      border-radius: 5px;
      cursor: pointer;
      margin-left: 10px;
    }

    .modal-content .confirm {
      background: #00ff88;
      color: #000;
    }

    .modal-content .cancel {
      background: #ff5f5f;
      color: #fff;
    }
  </style>
</head>
<body>

    <!-- LOGIN SECTION -->
    <div class="centered" id="loginScreen">
      <div class="login-card">
        <h2>Welcome to Trading Game</h2>
        <input type="text" id="inputName" placeholder="Enter your name" />
        <input type="number" id="inputBalance" placeholder="Starting balance in USD" />
        <button onclick="startGame()">Start Trading</button>
      </div>
    </div>
  
    <!-- APP SECTION -->
    <div id="app" style="display: none;">
      <div class="app-container">
        <h1>Trading Demo Game</h1>
  
        <!-- CHAT FEED -->
        <div class="chat-feed" id="chatFeed"></div>
  
        <!-- USER INFO + PORTFOLIO -->
        <div class="user-info">
          <p><strong>User:</strong> <span id="username">-</span></p>
          <p><strong>Cash Balance:</strong> $<span id="balance">0.00</span></p>
          <p><strong>Total Portfolio Value:</strong> $<span id="totalValue">0.00</span></p>
        </div>
  
        <div class="account-details">
          <p><strong>Email:</strong> <span id="email">user@example.com</span></p>
          <p><strong>Account Created:</strong> Jan 10, 2024</p>
          <p><strong>Status:</strong> Active Trader</p>
          <p><strong>Performance:</strong> <span id="performance">0.00%</span></p>
        </div>
  
        <!-- CURRENCY LAYOUT -->
        <div class="currency-cards" id="currencyCards"></div>
  
        <!-- TIMEFRAME BUTTONS -->
        <div class="time-buttons">
          <button onclick="setTimeframe('1D')">1D</button>
          <button onclick="setTimeframe('1W')">1W</button>
          <button onclick="setTimeframe('1M')">1M</button>
          <button onclick="setTimeframe('1Y')">1Y</button>
        </div>
  
        <!-- CHART -->
        <div class="chart-container">
          <canvas id="dynamicChart" height="100"></canvas>
        </div>
  
        <!-- BUY / SELL -->
        <div class="invest-buttons">
          <button onclick="openModal('buy')">💰 Buy</button>
          <button onclick="openModal('sell')">📤 Sell</button>
        </div>
      </div>
    </div>
  
    <!-- MODALS -->
    <div class="modal" id="tradeModal">
      <div class="modal-content">
        <h3 id="modalTitle">Trade</h3>
        <input type="number" id="tradeAmount" placeholder="Amount in USD">
        <div class="actions">
          <button class="cancel" onclick="closeModal()">Cancel</button>
          <button class="confirm" onclick="confirmTrade()">Confirm</button>
        </div>
      </div>
    </div>

    <script>
      let username = '';
      let startingBalance = 0;
      let portfolio = {
        balance: 0,
        holdings: { zorax: 0, metax: 0, lusq: 0, nebula: 0, quantyx: 0 }
      };
      let currentCurrency = '';
      let liveData = [], liveChart = null, intervalId;
      let tradeType = 'buy';
      
      const prices = {
        zorax: 12,
        metax: 2.8,
        lusq: 0.65,
        nebula: 6.9,
        quantyx: 48
      };
      
      const risks = {
        zorax: 'high',
        metax: 'medium',
        lusq: 'low',
        nebula: 'medium',
        quantyx: 'high'
      };
      
      const users = ['NovaX', 'CryptoQueen', 'StonksGuy', 'Elon Jr.', 'BearHunter'];
      const messages = [
        'Going all in on Zorax 🚀', 'Just cashed out 92% gain!', 'Lusq crashed again 💀',
        'Quantyx is pumping hard!', 'Nebula is safe for now.', 'I regret selling Metax...'
      ];
      
      function startGame() {
        username = document.getElementById('inputName').value || 'Dan';
        startingBalance = parseFloat(document.getElementById('inputBalance').value) || 10000;
        portfolio.balance = startingBalance;
      
        document.getElementById('loginScreen').style.display = 'none';
        document.getElementById('app').style.display = 'block';
        document.getElementById('username').innerText = username;
      
        generateCurrencyCards();
        updateUI();
        fakeChatLoop();
      }
      
      function generateCurrencyCards() {
        const container = document.getElementById('currencyCards');
        container.innerHTML = '';
        for (let key in prices) {
          const card = document.createElement('div');
          card.className = 'currency-card';
          card.innerHTML = `
            <h3>${key.toUpperCase()}</h3>
            <p><strong>Symbol:</strong> ${key.slice(0,3).toUpperCase()}</p>
            <p><strong>Risk:</strong> ${risks[key]}</p>
            <p><strong>Price:</strong> $<span id="price-${key}">${prices[key].toFixed(2)}</span></p>
            <p><strong>Holdings:</strong> <span id="holdings-${key}">0.00</span></p>
            <p><strong>Change:</strong> <span id="change-${key}">0.00%</span></p>
            <button onclick="showChart('${key}')">View Chart</button>
          `;
          container.appendChild(card);
        }
      }
      
      function fakeChatLoop() {
        const chat = document.getElementById('chatFeed');
        setInterval(() => {
          const msg = `<div class="chat-message"><span class="chat-user">${users[Math.floor(Math.random()*users.length)]}:</span> ${messages[Math.floor(Math.random()*messages.length)]}</div>`;
          chat.innerHTML += msg;
          if (chat.children.length > 12) chat.removeChild(chat.firstChild);
          chat.scrollTop = chat.scrollHeight;
        }, 6000);
      }
      
      function updateUI() {
        const total = Object.keys(portfolio.holdings).reduce((sum, key) => {
          const price = (currentCurrency === key && liveData.length) ? liveData[liveData.length - 1] : prices[key];
          return sum + portfolio.holdings[key] * price;
        }, portfolio.balance);
      
        const perf = ((total - startingBalance) / startingBalance) * 100;
        document.getElementById('balance').innerText = portfolio.balance.toFixed(2);
        document.getElementById('totalValue').innerText = total.toFixed(2);
      
        const perfEl = document.getElementById('performance');
        perfEl.innerText = `${perf.toFixed(2)}%`;
        perfEl.className = perf >= 0 ? 'highlight-profit' : 'highlight-loss';
      
        for (let key in prices) {
          const price = (currentCurrency === key && liveData.length) ? liveData[liveData.length - 1] : prices[key];
          document.getElementById(`price-${key}`).innerText = price.toFixed(2);
          document.getElementById(`holdings-${key}`).innerText = portfolio.holdings[key].toFixed(2);
          const baseValue = prices[key] * portfolio.holdings[key];
          const nowValue = price * portfolio.holdings[key];
          const diff = baseValue > 0 ? ((nowValue - baseValue) / baseValue) * 100 : 0;
          document.getElementById(`change-${key}`).innerText = `${diff.toFixed(2)}%`;
          document.getElementById(`change-${key}`).className = diff >= 0 ? 'highlight-profit' : 'highlight-loss';
        }
      }
      </script>


<script>
  function showChart(symbol) {
    clearInterval(intervalId);
    currentCurrency = symbol;
    const base = prices[symbol];
    const vol = { low: 0.002, medium: 0.01, high: 0.03 }[risks[symbol]];
    liveData = [base];
  
    for (let i = 1; i < 60; i++) {
      const next = parseFloat((liveData[i - 1] * (1 + (Math.random() - 0.5) * vol * 2)).toFixed(4));
      liveData.push(next);
    }
  
    const ctx = document.getElementById('dynamicChart').getContext('2d');
    if (liveChart) liveChart.destroy();
  
    liveChart = new Chart(ctx, {
      type: 'line',
      data: {
        labels: Array.from({ length: liveData.length }, (_, i) => i + 1),
        datasets: [{
          label: `${symbol.toUpperCase()} Price`,
          data: liveData,
          borderWidth: 2,
          tension: 0,
          segment: {
            borderColor: ctx => {
              const i = ctx.p0DataIndex;
              return liveData[i + 1] > liveData[i] ? '#00ff88' : '#ff5f5f';
            }
          }
        }]
      },
      options: {
        animation: false,
        plugins: {
          legend: { labels: { color: '#e6e6e6' } },
          zoom: {
            pan: { enabled: true, mode: 'x' },
            zoom: { wheel: { enabled: true }, mode: 'x' }
          }
        },
        scales: {
          x: { ticks: { color: '#ccc' } },
          y: { ticks: { color: '#ccc' } }
        }
      }
    });
  
    intervalId = setInterval(() => {
      const last = liveData.at(-1);
      const next = parseFloat((last * (1 + (Math.random() - 0.5) * 0.01)).toFixed(4));
      liveData.push(next);
      liveData.shift();
      liveChart.data.datasets[0].data = liveData;
      liveChart.update();
      updateUI();
    }, 2000);
  
    updateUI();
  }
  
  function setTimeframe(tf) {
    // optional: change data length based on timeframe
    showChart(currentCurrency);
  }
  
  function openModal(type) {
    if (!currentCurrency) return alert("Open a chart first.");
    tradeType = type;
    document.getElementById("modalTitle").innerText = type === "buy" ? "Buy Currency" : "Sell Currency";
    document.getElementById("tradeAmount").value = "";
    document.getElementById("tradeModal").style.display = "flex";
  }
  
  function closeModal() {
    document.getElementById("tradeModal").style.display = "none";
  }
  
  function confirmTrade() {
    const amount = parseFloat(document.getElementById("tradeAmount").value);
    const price = liveData.at(-1);
    if (!amount || amount <= 0) return alert("Enter a valid USD amount.");
    if (tradeType === "buy") {
      if (amount > portfolio.balance) return alert("Not enough cash.");
      portfolio.balance -= amount;
      portfolio.holdings[currentCurrency] += amount / price;
      alert("✅ Successfully bought!");
    } else {
      const coinAmount = amount / price;
      if (coinAmount > portfolio.holdings[currentCurrency]) return alert("Not enough holdings.");
      portfolio.holdings[currentCurrency] -= coinAmount;
      portfolio.balance += amount;
      alert("📤 Sold successfully!");
    }
    closeModal();
    updateUI();
  }
  </script>
  </body>
  </html>
  
  <body>
    <div class="centered" id="loginScreen">
      <div class="login-card">
        <h2>Welcome to Trading Game</h2>
        <input type="text" id="inputName" placeholder="Enter your name" />
        <input type="number" id="inputBalance" placeholder="Starting balance in USD" />
        <button onclick="startGame()">Start Trading</button>
      </div>
    </div>
  
    <div id="app" style="display: none;">
      <div class="app-container">
        <h1>Trading Demo Game</h1>
        <div class="chat-feed" id="chatFeed"></div>
  
        <div class="user-info">
          <p><strong>User:</strong> <span id="username"></span></p>
          <p><strong>Cash Balance:</strong> $<span id="balance">0.00</span></p>
          <p><strong>Total Portfolio Value:</strong> $<span id="totalValue">0.00</span></p>
        </div>
  
        <div class="account-details">
          <p><strong>Email:</strong> user@example.com</p>
          <p><strong>Account Created:</strong> Jan 10, 2024</p>
          <p><strong>Status:</strong> Active Trader</p>
          <p><strong>Performance:</strong> <span id="performance">0.00%</span></p>
        </div>
  
        <div class="currency-cards" id="currencyCards"></div>
  
        <div class="time-buttons">
          <button onclick="setTimeframe('1D')">1D</button>
          <button onclick="setTimeframe('1W')">1W</button>
          <button onclick="setTimeframe('1M')">1M</button>
          <button onclick="setTimeframe('1Y')">1Y</button>
        </div>
  
        <div class="chart-container">
          <canvas id="dynamicChart"></canvas>
        </div>
  
        <div class="invest-buttons">
          <button onclick="openModal('buy')">💰 Buy</button>
          <button onclick="openModal('sell')">📤 Sell</button>
        </div>
      </div>
    </div>
  
    <div class="modal" id="tradeModal">
      <div class="modal-content">
        <h3 id="modalTitle">Trade</h3>
        <input type="number" id="tradeAmount" placeholder="Amount in USD">
        <div class="actions">
          <button class="cancel" onclick="closeModal()">Cancel</button>
          <button class="confirm" onclick="confirmTrade()">Confirm</button>
        </div>
      </div>
    </div>
  
    <script>
      let username = '';
      let startingBalance = 0;
      let portfolio = { balance: 0, holdings: { zorax: 0, metax: 0, lusq: 0, nebula: 0, quantyx: 0 } };
      let prices = { zorax: 12, metax: 2.8, lusq: 0.65, nebula: 6.9, quantyx: 48 };
      let risks = { zorax: 'high', metax: 'medium', lusq: 'low', nebula: 'medium', quantyx: 'high' };
      let currentCurrency = '', tradeType = 'buy';
      let liveChart, liveData = [], intervalId;
  
      const users = ['NovaX', 'CryptoQueen', 'StonksGuy', 'Elon Jr.', 'BearHunter'];
      const messages = [
        'Zorax je totální sračka dneska 💀', 'Kdo koupil Lusq? Sračka týdne!',
        'Quantyx fakt letí nahoru 🚀', 'Nebula drží, ale pozor', 'Metax byl lepší minulý týden',
        'Sračka jako vždy... díky Lusq', 'Zorax je život!', 'Kdo prodal? Já cashoval 80%! 💸'
      ];
  
      function startGame() {
        username = document.getElementById('inputName').value || 'Dan';
        startingBalance = parseFloat(document.getElementById('inputBalance').value) || 10000;
        portfolio.balance = startingBalance;
        document.getElementById('username').innerText = username;
        document.getElementById('loginScreen').style.display = 'none';
        document.getElementById('app').style.display = 'block';
        generateCurrencyCards();
        updateUI();
        setInterval(fakeChat, 6000);
      }
  
      function fakeChat() {
        const chat = document.getElementById('chatFeed');
        const msg = `<div class="chat-message"><span class="chat-user">${users[Math.floor(Math.random()*users.length)]}:</span> ${messages[Math.floor(Math.random()*messages.length)]}</div>`;
        chat.innerHTML += msg;
        if (chat.children.length > 10) chat.removeChild(chat.firstChild);
        chat.scrollTop = chat.scrollHeight;
      }
  
      function generateCurrencyCards() {
        const container = document.getElementById('currencyCards');
        container.innerHTML = '';
        for (let key in prices) {
          container.innerHTML += `
            <div class="currency-card">
              <h3>${key.toUpperCase()}</h3>
              <p><strong>Price:</strong> $<span id="price-${key}">${prices[key].toFixed(2)}</span></p>
              <p><strong>Risk:</strong> ${risks[key]}</p>
              <p><strong>Holdings:</strong> <span id="hold-${key}">0.00</span></p>
              <p><strong>Change:</strong> <span id="chg-${key}">0.00%</span></p>
              <button onclick="showChart('${key}')">View Chart</button>
            </div>
          `;
        }
      }
  
      function updateUI() {
        const total = Object.keys(portfolio.holdings).reduce((sum, k) => {
          const price = (k === currentCurrency && liveData.length) ? liveData.at(-1) : prices[k];
          return sum + portfolio.holdings[k] * price;
        }, portfolio.balance);
  
        const perf = ((total - startingBalance) / startingBalance) * 100;
        document.getElementById('balance').innerText = portfolio.balance.toFixed(2);
        document.getElementById('totalValue').innerText = total.toFixed(2);
        const perfEl = document.getElementById('performance');
        perfEl.innerText = `${perf.toFixed(2)}%`;
        perfEl.className = perf >= 0 ? 'highlight-profit' : 'highlight-loss';
  
        for (let k in prices) {
          const price = (k === currentCurrency && liveData.length) ? liveData.at(-1) : prices[k];
          const val = price.toFixed(2);
          document.getElementById(`price-${k}`).innerText = val;
          document.getElementById(`hold-${k}`).innerText = portfolio.holdings[k].toFixed(2);
          const change = ((price - prices[k]) / prices[k]) * 100;
          const chgEl = document.getElementById(`chg-${k}`);
          chgEl.innerText = `${change.toFixed(2)}%`;
          chgEl.className = change >= 0 ? 'highlight-profit' : 'highlight-loss';
        }
      }
  
      function setTimeframe() {
        if (currentCurrency) showChart(currentCurrency);
      }
  
      function showChart(symbol) {
        clearInterval(intervalId);
        currentCurrency = symbol;
        const base = prices[symbol];
        const vol = { low: 0.002, medium: 0.01, high: 0.03 }[risks[symbol]];
        liveData = [base];
  
        for (let i = 1; i < 60; i++) {
          const last = liveData[i - 1];
          const next = last * (1 + (Math.random() - 0.5) * vol * 2);
          liveData.push(next);
        }
  
        const ctx = document.getElementById('dynamicChart').getContext('2d');
        if (liveChart) liveChart.destroy();
  
        liveChart = new Chart(ctx, {
          type: 'line',
          data: {
            labels: liveData.map((_, i) => i + 1),
            datasets: [{
              data: liveData,
              borderWidth: 2,
              tension: 0,
              segment: {
                borderColor: ctx => {
                  const i = ctx.p0DataIndex;
                  return liveData[i + 1] > liveData[i] ? '#00ff88' : '#ff5f5f';
                }
              }
            }]
          },
          options: {
            animation: false,
            plugins: {
              zoom: {
                pan: { enabled: true, mode: 'x' },
                zoom: { wheel: { enabled: true }, mode: 'x' }
              }
            },
            scales: {
              x: { ticks: { color: '#ccc' } },
              y: { ticks: { color: '#ccc' } }
            }
          }
        });
  
        intervalId = setInterval(() => {
          const last = liveData.at(-1);
          const next = parseFloat((last * (1 + (Math.random() - 0.5) * 0.01)).toFixed(4));
          liveData.push(next);
          liveData.shift();
          liveChart.data.datasets[0].data = liveData;
          liveChart.update();
          updateUI();
        }, 2000);
  
        updateUI();
      }
  
      function openModal(type) {
        if (!currentCurrency) return alert("Open a chart first.");
        tradeType = type;
        document.getElementById("modalTitle").innerText = type === "buy" ? "Buy Currency" : "Sell Currency";
        document.getElementById("tradeAmount").value = "";
        document.getElementById("tradeModal").style.display = "flex";
      }
  
      function closeModal() {
        document.getElementById("tradeModal").style.display = "none";
      }
  
      function confirmTrade() {
        const amount = parseFloat(document.getElementById("tradeAmount").value);
        const price = liveData.at(-1);
        if (!amount || amount <= 0) return alert("Enter a valid USD amount.");
  
        if (tradeType === "buy") {
          if (amount > portfolio.balance) return alert("Not enough cash.");
          portfolio.balance -= amount;
          portfolio.holdings[currentCurrency] += amount / price;
          alert("✅ Successfully bought!");
        } else {
          const coins = amount / price;
          if (coins > portfolio.holdings[currentCurrency]) return alert("Not enough holdings.");
          portfolio.holdings[currentCurrency] -= coins;
          portfolio.balance += amount;
          alert("📤 Sold successfully!");
        }
  
        closeModal();
        updateUI();
      }
    </script>
  </body>
  </html>
  
