 <!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width,initial-scale=1">
  <title>Crypto Vault Pro - Secure Storage & Authentication</title>
  <!-- Bootstrap CSS -->
  <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css" rel="stylesheet">
  <!-- Font Awesome -->
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.2/css/all.min.css">
  <style>
    body {
      background: linear-gradient(135deg, #e0eafc 0%, #cfdef3 100%);
      min-height: 100vh;
      color: #181830;
    }
    .container-main {
      max-width: 520px;
      margin: 3vh auto 2em auto;
      background: #fff;
      border-radius: 16px;
      padding: 2.2em 1.3em 2.2em 1.3em;
      box-shadow: 0 4px 18px #0002;
    }
    .section {
      background: #f6f7fb;
      border-radius: 11px;
      margin: 1.4em 0 1em 0;
      padding: 1.1em 1em 0.8em 1em;
    }
    h1, h2 {
      text-align: center;
      margin-bottom: 1.1em;
    }
    .form-label i {
      margin-right: 7px;
      color: #0d6efd;
    }
    .btn, input, select {
      font-size: 1em;
      border-radius: 7px;
    }
    input, select {
      width: 100%;
      background: #e9ecfa;
      color: #181830;
      border: 1px solid #c1c8e0;
    }
    .btn-gradient {
      background: linear-gradient(90deg, #4d97fa, #3ec6e0, #8de18c);
      color: #fff !important;
      border: none;
    }
    .btn-gradient:hover {
      background: linear-gradient(90deg, #276bd3, #1ba4bc, #56a05c);
    }
    .vault-wallet-list {
      margin: .5em 0 0 1em;
      padding: 0;
      list-style: disc;
    }
    .vault-wallet-list li {
      font-size: .97em;
      margin-bottom: .3em;
    }
    .msg {
      margin: 1em 0;
      background: #f6f8fa;
      border-left: 4px solid #4d97fa;
      padding: 1em;
      border-radius: 8px;
      display: none;
    }
    .msg.error {
      border-color: #f76d6d;
      color: #f76d6d;
      background: #fbe9ec;
    }
    code {
      background: #101020;
      color: #f8f8f8;
      padding: 0.09em 0.29em;
      border-radius: 4px;
      word-break: break-all;
    }
    .vault-label {
      font-weight: 600;
      margin-top: 0.7em;
    }
    @media (max-width:600px){
      .container-main{padding:1em;}
    }
  </style>
</head>
<body>
<div class="container-main shadow">
  <h1><i class="fas fa-shield-alt"></i> Crypto Vault Pro</h1>

  <div id="messageDisplay" class="msg"></div>

  <!-- Vault Unlock Section -->
  <div class="section">
    <h2>🔐 Unlock Vault</h2>
    <label class="form-label vault-label" for="pinInput"><i class="fa fa-key"></i>Enter PIN:</label>
    <input type="password" id="pinInput" class="form-control mb-2" autocomplete="off" minlength="6">
    <label class="form-label" for="phoneInput"><i class="fa fa-phone"></i>Phone for SMS Notification:</label>
    <input type="tel" id="phoneInput" class="form-control mb-2" placeholder="+1234567890">
    <button onclick="unlockVault()" class="btn btn-gradient w-100 mb-2">Unlock Vault</button>
  </div>

  <!-- Vault Display Section -->
  <div class="section">
    <h2><i class="fas fa-vault"></i> Vault</h2>
    <div id="vaultDisplay" style="min-height:2em"></div>

    <label class="form-label vault-label" for="ownerInput"><i class="fa fa-user"></i>Vault Owner Name:</label>
    <input type="text" id="ownerInput" class="form-control mb-2" placeholder="Set Name">
    <button onclick="setOwner()" class="btn btn-outline-primary w-100 mb-2">Set Name</button>

    <label class="form-label" for="cryptoType"><i class="fab fa-bitcoin"></i>Select Crypto Type:</label>
    <select id="cryptoType" class="form-select mb-2">
      <option>Ethereum</option>
      <option>Bitcoin</option>
      <option>Solana</option>
      <option>Cardano</option>
      <option>Polkadot</option>
      <option>Ripple</option>
      <option>Litecoin</option>
    </select>
    <label class="form-label" for="walletInput"><i class="fa fa-key"></i>Wallet Private Key:</label>
    <input type="text" id="walletInput" class="form-control mb-2" placeholder="Add Wallet">
    <button onclick="addWallet()" class="btn btn-outline-success w-100 mb-2">Add Wallet</button>

    <div class="d-flex gap-2 mb-2">
      <button onclick="exportVault()" class="btn btn-gradient flex-fill"><i class="fa fa-download"></i> Export Vault</button>
      <input type="file" id="importFile" class="form-control" style="max-width: 170px;" accept=".vault">
      <button onclick="importVault()" class="btn btn-outline-primary">Import Vault</button>
    </div>
  </div>

  <!-- Change PIN Section -->
  <div class="section">
    <h2><i class="fas fa-tools"></i> Change PIN</h2>
    <label class="form-label" for="currentPin"><i class="fa fa-key"></i>Current PIN:</label>
    <input type="password" id="currentPin" class="form-control mb-2">
    <label class="form-label" for="newPin"><i class="fa fa-key"></i>New PIN:</label>
    <input type="password" id="newPin" class="form-control mb-2">
    <label class="form-label" for="confirmPin"><i class="fa fa-key"></i>Confirm New PIN:</label>
    <input type="password" id="confirmPin" class="form-control mb-2">
    <button onclick="changePin()" class="btn btn-warning w-100">Change PIN</button>
  </div>

  <!-- Authentication Section (Login/Register) -->
  <div class="section">
    <h2><i class="fas fa-user-shield"></i> User Authentication</h2>
    <form id="loginForm">
      <div class="mb-3">
        <label class="form-label"><i class="fa fa-envelope"></i> Email address</label>
        <input type="email" class="form-control" placeholder="Enter email" required>
      </div>
      <div class="mb-3">
        <label class="form-label"><i class="fa fa-lock"></i> Password</label>
        <input type="password" class="form-control" placeholder="Password" required>
      </div>
      <button type="submit" class="btn btn-primary w-100 mb-2">Login</button>
      <div class="d-flex justify-content-between mb-2">
        <a href="#" data-bs-toggle="modal" data-bs-target="#forgotModal">Forgot password?</a>
        <a href="#" data-bs-toggle="modal" data-bs-target="#registerModal">Register</a>
      </div>
      <hr>
      <button type="button" class="btn btn-outline-dark w-100 mb-2" id="connectGoogleWalletBtn">
        <i class="fab fa-google-wallet me-2"></i>Connect Google Wallet
      </button>
      <button type="button" class="btn btn-outline-primary w-100" id="connectMetaMaskBtn">
        <i class="fab fa-ethereum me-2"></i>Connect MetaMask
      </button>
    </form>
  </div>
</div>

<!-- Register Modal -->
<div class="modal fade" id="registerModal" tabindex="-1" aria-labelledby="registerModalLabel" aria-hidden="true">
  <div class="modal-dialog">
    <div class="modal-content">
      <form id="registerForm">
        <div class="modal-header">
          <h5 class="modal-title" id="registerModalLabel">Register</h5>
          <button type="button" class="btn-close" data-bs-dismiss="modal"></button>
        </div>
        <div class="modal-body">
          <div class="mb-3">
            <label class="form-label"><i class="fa fa-user"></i> Username</label>
            <input type="text" class="form-control" placeholder="Enter username" required>
          </div>
          <div class="mb-3">
            <label class="form-label"><i class="fa fa-envelope"></i> Email address</label>
            <input type="email" class="form-control" placeholder="Enter email" required>
          </div>
          <div class="mb-3">
            <label class="form-label"><i class="fa fa-lock"></i> Password</label>
            <input type="password" class="form-control" placeholder="Password" required>
          </div>
        </div>
        <div class="modal-footer">
          <button type="submit" class="btn btn-success w-100">Register</button>
        </div>
      </form>
    </div>
  </div>
</div>

<!-- Forgot Password Modal -->
<div class="modal fade" id="forgotModal" tabindex="-1" aria-labelledby="forgotModalLabel" aria-hidden="true">
  <div class="modal-dialog">
    <div class="modal-content">
      <form id="forgotForm">
        <div class="modal-header">
          <h5 class="modal-title" id="forgotModalLabel">Forgot Password</h5>
          <button type="button" class="btn-close" data-bs-dismiss="modal"></button>
        </div>
        <div class="modal-body">
          <div class="mb-3">
            <label class="form-label"><i class="fa fa-envelope"></i> Email address</label>
            <input type="email" class="form-control" placeholder="Enter your email" required>
          </div>
        </div>
        <div class="modal-footer">
          <button type="submit" class="btn btn-warning w-100">Reset Password</button>
        </div>
      </form>
    </div>
  </div>
</div>

<!-- Bootstrap JS (for modals) -->
<script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/js/bootstrap.bundle.min.js"></script>
<script>
let vault = { owner: '', wallets: [] };
let failedAttempts = 0;

function showMessage(msg, type = "success") {
  const el = document.getElementById("messageDisplay");
  el.innerText = msg;
  el.className = "msg " + type;
  el.style.display = "block";
  setTimeout(() => { el.style.display = "none"; }, 3500);
}

function validatePin(pin) {
  if (pin.length < 6) return "PIN must be at least 6 characters.";
  if (!/\d/.test(pin)) return "PIN must contain digits.";
  return "OK";
}

async function sendFreeSMS(phone, message) {
  try {
    const response = await fetch("https://textbelt.com/text", {
      method: "POST",
      headers: {"Content-Type": "application/json"},
      body: JSON.stringify({ phone, message, key: "textbelt" })
    });
    const result = await response.json();
    if (result.success)
      showMessage("SMS sent successfully.");
    else
      showMessage("SMS failed: " + result.error, "error");
  } catch {
    showMessage("SMS request failed.", "error");
  }
}

async function encryptVault(pin) {
  const enc = new TextEncoder();
  const keyMaterial = await crypto.subtle.importKey("raw", enc.encode(pin), {name: "PBKDF2"}, false, ["deriveKey"]);
  const key = await crypto.subtle.deriveKey(
    {name: "PBKDF2", salt: enc.encode("vault_salt"), iterations: 100000, hash: "SHA-256"},
    keyMaterial, {name: "AES-GCM", length: 256}, false, ["encrypt"]
  );
  const iv = crypto.getRandomValues(new Uint8Array(12));
  const encrypted = await crypto.subtle.encrypt(
    {name: "AES-GCM", iv: iv}, key, enc.encode(JSON.stringify(vault))
  );
  return {cipher: Array.from(new Uint8Array(encrypted)), iv: Array.from(iv)};
}

async function decryptVault(pin, stored) {
  try {
    const enc = new TextEncoder();
    const keyMaterial = await crypto.subtle.importKey("raw", enc.encode(pin), {name: "PBKDF2"}, false, ["deriveKey"]);
    const key = await crypto.subtle.deriveKey(
      {name: "PBKDF2", salt: enc.encode("vault_salt"), iterations: 100000, hash: "SHA-256"},
      keyMaterial, {name: "AES-GCM", length: 256}, false, ["decrypt"]
    );
    const decrypted = await crypto.subtle.decrypt(
      {name: "AES-GCM", iv: new Uint8Array(stored.iv)}, key, new Uint8Array(stored.cipher)
    );
    const dec = new TextDecoder().decode(decrypted);
    vault = JSON.parse(dec);
    return true;
  } catch {
    return false;
  }
}

async function unlockVault() {
  const pin = document.getElementById("pinInput").value.trim();
  const phone = document.getElementById("phoneInput").value.trim();
  const stored = localStorage.getItem("vault");
  if (!stored) {
    showMessage("No vault found.", "error");
    return;
  }
  const validation = validatePin(pin);
  if (validation !== "OK") {
    showMessage(validation, "error");
    return;
  }
  const parsed = JSON.parse(stored);
  const success = await decryptVault(pin, parsed);
  if (success) {
    failedAttempts = 0;
    displayVault();
    showMessage("Vault unlocked successfully.");
    if (phone.startsWith("+")) {
      await sendFreeSMS(phone, `Vault accessed at ${new Date().toLocaleString()}`);
    }
  } else {
    failedAttempts++;
    showMessage("Incorrect PIN. Attempt " + failedAttempts, "error");
  }
}

async function changePin() {
  const current = document.getElementById("currentPin").value.trim();
  const newPin = document.getElementById("newPin").value.trim();
  const confirm = document.getElementById("confirmPin").value.trim();
  if (newPin !== confirm) {
    showMessage("New PINs do not match.", "error");
    return;
  }
  if (newPin.length < 6) {
    showMessage("New PIN must be at least 6 characters.", "error");
    return;
  }
  const stored = JSON.parse(localStorage.getItem("vault") || "null");
  if (!stored) {
    showMessage("No vault found.", "error");
    return;
  }
  const success = await decryptVault(current, stored);
  if (!success) {
    showMessage("Current PIN is incorrect.", "error");
    return;
  }
  const encrypted = await encryptVault(newPin);
  localStorage.setItem("vault", JSON.stringify(encrypted));
  showMessage("PIN changed successfully.");
  document.getElementById("currentPin").value = "";
  document.getElementById("newPin").value = "";
  document.getElementById("confirmPin").value = "";
}

function setOwner() {
  const owner = document.getElementById("ownerInput").value.trim();
  if (!owner) {
    showMessage("Owner name is required.", "error");
    return;
  }
  vault.owner = owner;
  displayVault();
  showMessage("Vault owner set.");
  document.getElementById("ownerInput").value = "";
}

function addWallet() {
  const key = document.getElementById("walletInput").value.trim();
  const type = document.getElementById("cryptoType").value;
  if (!key) {
    showMessage("Enter a private key.", "error");
    return;
  }
  vault.wallets.push({type: type, key: key});
  displayVault();
  showMessage("Wallet added.");
  document.getElementById("walletInput").value = "";
}

async function exportVault() {
  const pin = document.getElementById("pinInput").value.trim();
  if (!pin) {
    showMessage("Enter your PIN to export.", "error");
    return;
  }
  const encrypted = await encryptVault(pin);
  localStorage.setItem("vault", JSON.stringify(encrypted));
  const blob = new Blob([JSON.stringify(encrypted)], {type: "application/octet-stream"});
  const url = URL.createObjectURL(blob);
  const a = document.createElement("a");
  a.href = url;
  a.download = "vault_backup.vault";
  document.body.appendChild(a);
  a.click();
  a.remove();
  URL.revokeObjectURL(url);
  showMessage("Vault exported.");
}

function importVault() {
  const file = document.getElementById("importFile").files[0];
  if (!file) {
    showMessage("Select a file first.", "error");
    return;
  }
  const reader = new FileReader();
  reader.onload = function(e) {
    try {
      const imported = JSON.parse(e.target.result);
      localStorage.setItem("vault", JSON.stringify(imported));
      showMessage("Vault imported successfully.");
      displayVault();
    } catch {
      showMessage("Invalid vault file.", "error");
    }
  };
  reader.readAsText(file);
}

function displayVault() {
  const el = document.getElementById("vaultDisplay");
  let html = "";
  html += `<div><b>Owner:</b> ${vault.owner ? vault.owner : "(not set)"}</div>`;
  html += `<div><b>Wallets:</b></div>`;
  if (vault.wallets.length === 0) {
    html += "<span>none</span>";
  } else {
    html += `<ul class="vault-wallet-list">`;
    for (const w of vault.wallets) {
      html += `<li><b>${w.type}</b>: <code>${w.key}</code></li>`;
    }
    html += `</ul>`;
  }
  el.innerHTML = html;
}

// On load, try to display vault if present
window.addEventListener('DOMContentLoaded', () => {
  const stored = localStorage.getItem("vault");
  if (stored) {
    // Try default PIN "000000" for demonstration, vault remains locked if not matching
    decryptVault("000000", JSON.parse(stored)).then(success => {
      if (success) displayVault();
    });
  }
});

// Authentication demo handlers
document.getElementById('loginForm').addEventListener('submit', function(e) {
  e.preventDefault();
  alert('Login not implemented (demo only)');
});
document.getElementById('registerForm').addEventListener('submit', function(e) {
  e.preventDefault();
  alert('Registration not implemented (demo only)');
});
document.getElementById('forgotForm').addEventListener('submit', function(e) {
  e.preventDefault();
  alert('Reset password not implemented (demo only)');
});
document.getElementById('connectGoogleWalletBtn').addEventListener('click', function() {
  alert('Google Wallet connection not implemented (demo only)');
});
document.getElementById('connectMetaMaskBtn').addEventListener('click', function() {
  alert('MetaMask connection not implemented (demo only)');
});
</script>
</body>
</html>
