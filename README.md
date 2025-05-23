<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>Simulasi Slot & Mahjong & Bonus</title>
  <link href="https://cdn.jsdelivr.net/npm/tailwindcss@2.2.19/dist/tailwind.min.css" rel="stylesheet" />
  <style>
    body { background: #121212; color: white; font-family: sans-serif; }
    .reel { animation: spin 0.5s ease-in-out; }
    @keyframes spin {
      0% { transform: rotateX(0); }
      100% { transform: rotateX(360deg); }
    }
    .blink {
      animation: blink 1s step-start 0s infinite;
    }
    @keyframes blink {
      50% { opacity: 0; }
    }
    .shake {
      animation: shake 0.5s;
    }
    @keyframes shake {
      0% { transform: translate(1px, 1px) rotate(0deg); }
      25% { transform: translate(-1px, -2px) rotate(-1deg); }
      50% { transform: translate(-3px, 0) rotate(1deg); }
      75% { transform: translate(3px, 2px) rotate(0deg); }
      100% { transform: translate(1px, -1px) rotate(1deg); }
    }
  </style>
</head>
<body class="min-h-screen flex flex-col items-center p-4 bg-gray-900">

  <audio id="spinSound" src="https://www.soundjay.com/button/beep-07.wav"></audio>
  <audio id="scatterSound" src="https://www.soundjay.com/button/sounds/button-16.mp3"></audio>
  <audio id="jackpotSound" src="https://www.soundjay.com/button/sounds/button-10.mp3"></audio>
  <audio id="freeSpinSound" src="https://www.soundjay.com/button/sounds/button-3.mp3"></audio>
  <audio id="backgroundMusic" src="https://www.bensound.com/bensound-music/bensound-funkyelement.mp3" loop></audio>

  <div class="max-w-5xl w-full space-y-6">

    <header class="flex justify-between items-center mb-4">
      <h1 class="text-3xl font-bold">Lobby Game Simulasi</h1>
      <button id="toggleThemeBtn" class="bg-indigo-600 px-3 py-1 rounded">Mode Siang</button>
    </header>

    <section class="flex items-center space-x-4">
      <input id="username" placeholder="Nama / Email" class="text-black px-2 py-1 rounded" />
      <button onclick="login()" class="bg-indigo-600 px-4 py-2 rounded">Login</button>
      <span id="userStatus" class="ml-4"></span>
    </section>

    <section class="flex justify-between items-center">
      <p>Saldo: <span id="balance">1000</span> koin</p>
      <div class="space-x-2">
        <button onclick="simulateDeposit()" class="bg-green-500 hover:bg-green-600 px-4 py-2 rounded">Deposit +500</button>
        <button onclick="simulateWithdraw()" class="bg-blue-500 hover:bg-blue-600 px-4 py-2 rounded">Withdraw -500</button>
      </div>
    </section>

    <section class="grid md:grid-cols-2 gap-6">

      <!-- Slot Game -->
      <div class="bg-gray-800 p-6 rounded-xl shadow-lg">
        <h2 class="text-2xl font-semibold mb-2">Slot Buah</h2>
        <div class="grid grid-cols-3 gap-2 text-4xl mb-4 text-center" id="reels">
          <div class="reel">🍒</div>
          <div class="reel">🍋</div>
          <div class="reel">🍇</div>
        </div>
        <div class="flex justify-center mb-2 space-x-2">
          <button onclick="setBet(10)" class="bg-yellow-600 px-3 py-1 rounded">BET 10</button>
          <button onclick="setBet(50)" class="bg-yellow-600 px-3 py-1 rounded">BET 50</button>
          <button onclick="setBet(100)" class="bg-yellow-600 px-3 py-1 rounded">BET 100</button>
        </div>
        <div class="text-center mb-2">Taruhan: <span id="bet">10</span> koin</div>
        <button onclick="spin()" class="bg-yellow-500 hover:bg-yellow-600 text-black font-bold py-2 px-4 rounded-full w-full">Putar</button>
        <p class="mt-2 text-center" id="result">Tekan Putar untuk mulai!</p>
      </div>

      <!-- Mahjong Game -->
      <div class="bg-gray-800 p-6 rounded-xl shadow-lg text-center">
        <h2 class="text-2xl font-semibold mb-4">Mahjong Mini</h2>
        <p class="mb-2 text-gray-400">Cocokkan dua simbol Mahjong yang sama:</p>
        <div class="grid grid-cols-4 gap-2 justify-center text-3xl" id="mahjong-board"></div>
        <p id="mahjong-status" class="mt-4 text-green-400"></p>
        <button onclick="resetMahjong()" class="mt-4 bg-purple-600 hover:bg-purple-700 px-4 py-2 rounded">Reset Mahjong</button>
      </div>

      <!-- Lucky 7 Game -->
      <div class="bg-gray-800 p-6 rounded-xl shadow-lg text-center">
        <h2 class="text-2xl font-semibold mb-4">Lucky 7</h2>
        <p class="text-gray-400">Tekan tombol, dapatkan angka 7 untuk menang besar!</p>
        <div class="text-6xl my-4" id="luckyNumber">-</div>
        <button onclick="playLucky7()" class="bg-red-600 hover:bg-red-700 px-6 py-3 rounded-full">Putar Lucky 7</button>
        <p class="mt-2" id="luckyResult"></p>
      </div>

      <!-- Mini Game Tebak Angka -->
      <div class="bg-gray-800 p-6 rounded-xl shadow-lg text-center">
        <h2 class="text-2xl font-semibold mb-4">Tebak Angka Cepat</h2>
        <p class="mb-2 text-gray-400">Tebak angka antara 0-9, dapatkan bonus jika benar!</p>
        <input id="guessInput" type="number" min="0" max="9" class="text-black px-2 py-1 rounded w-24 mx-auto mb-2" />
        <button onclick="guessNumber()" class="bg-green-600 hover:bg-green-700 px-4 py-2 rounded">Tebak</button>
        <p id="guessResult" class="mt-2"></p>
      </div>

      <!-- Chat Pemain -->
      <div class="bg-gray-800 p-6 rounded-xl shadow-lg">
        <h2 class="text-2xl font-semibold mb-4">Chat Pemain (Simulasi)</h2>
        <div id="chatBox" class="h-48 overflow-auto bg-gray-900 p-2 rounded mb-2 text-sm"></div>
        <input id="chatInput" type="text" placeholder="Ketik pesan..." class="w-full text-black px-2 py-1 rounded" />
        <button onclick="sendMessage()" class="mt-2 bg-indigo-600 px-4 py-2 rounded w-full">Kirim</button>
      </div>

    </section>

    <section class="flex justify-between mt-6">
      <button onclick="resetData()" class="bg-red-600 hover:bg-red-700 px-4 py-2 rounded">Reset Semua Data</button>
      <button onclick="toggleMusic()" id="musicBtn" class="bg-blue-600 hover:bg-blue-700 px-4 py-2 rounded">Musik: Mati</button>
    </section>

  </div>

  <script>
    // Data dasar
    let balance = parseInt(localStorage.getItem('balance')) || 1000;
    let currentBet = 10;
    let username = '';
    let freeSpins = 0;
    let jackpot = parseInt(localStorage.getItem('jackpot')) || 1000;
    let leaderboard = JSON.parse(localStorage.getItem('leaderboard')) || [];
    let scatterCount = 0;

    // Elemen
    const balanceEl = document.getElementById('balance');
    const betEl = document.getElementById('bet');
    const resultEl = document.getElementById('result');
    const userStatusEl = document.getElementById('userStatus');
    const reels = document.querySelectorAll('#reels div');
    const luckyNumberEl = document.getElementById('luckyNumber');
    const luckyResultEl = document.getElementById('luckyResult');
    const mahjongBoard = document.getElementById('mahjong-board');
    const mahjongStatus = document.getElementById('mahjong-status');
    const guessResultEl = document.getElementById('guessResult');
    const chatBox = document.getElementById('chatBox');
    const chatInput = document.getElementById('chatInput');
    const musicBtn = document.getElementById('musicBtn');
    const backgroundMusic = document.getElementById('backgroundMusic');
    const toggleThemeBtn = document.getElementById('toggleThemeBtn');

    // Suara
    const spinSound = document.getElementById('spinSound');
    const scatterSound = document.getElementById('scatterSound');
    const jackpotSound = document.getElementById('jackpotSound');
    const freeSpinSound = document.getElementById('freeSpinSound');

    // Simbol slot, termasuk scatter
    const symbols = ['🍒', '🍋', '🍇', '🔔', '⭐', '💎', '💥']; // 💥 scatter

    // Set taruhan
    function setBet(amount) {
      currentBet = amount;
      betEl.textContent = amount;
    }

    // Update saldo di layar dan localStorage
    function updateBalance() {
      balanceEl.textContent = balance;
      localStorage.setItem('balance', balance);
    }

    // Deposit simulasi
    function simulateDeposit() {
      balance += 500;
      updateBalance();
      alert('Deposit +500 koin berhasil!');
    }

    // Withdraw simulasi
    function simulateWithdraw() {
      if (balance >= 500) {
        balance -= 500;
        updateBalance();
        alert('Withdraw -500 koin berhasil!');
      } else {
        alert('Saldo tidak cukup untuk withdraw.');
      }
    }

    // Login simulasi
    function login() {
      const input = document.getElementById('username').value.trim();
      if (!input) {
        alert('Masukkan nama atau email terlebih dahulu.');
        return;
      }
      username = input;
      userStatusEl.textContent = 'Selamat datang, ' + username;
      loadLeaderboard();
      checkDailyBonus();
    }

    // Free spin
