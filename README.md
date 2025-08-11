<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Crypto Vault - Secure Storage</title>
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <link rel="icon" href="data:image/svg+xml,<svg xmlns=%22http://www.w3.org/2000/svg%22 viewBox=%220 0 24 24%22><text y=%2218%22 font-size=%2218%22>🔐</text></svg>">
  <link rel="stylesheet" href="styles.css">
  <style>
    body { font-family: system-ui, sans-serif; background: #181830; color: #f8f8f8; }
    main { max-width: 480px; margin: 2em auto; background: #23234a; border-radius: 14px; padding: 2em; box-shadow: 0 4px 18px #0004; }
    h1 { text-align: center; }
    .section { background: #232350; border-radius: 10px; margin: 1.5em 0; padding: 1em; }
    label { display: block; margin: .7em 0 .2em; }
    input, select, button { font-size: 1em; border-radius: 6px; border: none; padding: 0.45em 0.8em; margin-bottom: .4em; }
    input, select { width: 100%; background: #282850; color: #f8f8f8; border: 1px solid #393974; }
    button { background: #4d97fa; color: #fff; border: none; padding: .5em 1.1em; cursor: pointer; margin-top: .5em; }
    button:hover { background: #276bd3; }
    .wallet-list { margin: .5em 0 0 1em; padding: 0; }
    .wallet-list li { font-size: .97em; margin-bottom: .3em; }
    .msg { margin: 1em 0; background: #282850; border-left: 4px solid #4d97fa; padding: 1em; border-radius: 7px; }
    .msg.error { border-color: #f76d6d; color: #f76d6d; background: #391d2a; }
    code { background: #101020; color: #f8f8f8; padding: 0.08em 0.28em; border-radius: 4px; word-break: break-all; }
    @media (max-width:600px){ main{padding:1em;} }
  </style>
</head>
<body>
<main>
  <h1>🔐 Crypto Vault</h1>

  <form class="section" onsubmit="unlockVault(); return false;" autocomplete="off">
    <label for="pinInput">Enter PIN:</label>
    <input type="password" id="pinInput" autocomplete="new-password" required minlength="6">
    <label for="phoneInput">Phone Number for SMS Notification:</label>
    <input type="text" id="phoneInput" placeholder="+33612345678">
    <button type="submit">Unlock Vault</button>
  </form>

  <form class="section" onsubmit="setOwner(); return false;">
    <label for="ownerInput">Vault Owner Name:</label>
    <input type="text" id="ownerInput" maxlength="50" required>
    <button type="submit">Set Name</button>
  </form>

  <form class="section" onsubmit="addWallet(); return false;">
    <label for="cryptoType">Select Crypto Type:</label>
    <select id="cryptoType">
      <option value="ETH">Ethereum</option>
      <option value="BTC">Bitcoin</option>
      <option value="SOL">Solana</option>
      <option value="ADA">Cardano</option>
      <option value="DOT">Polkadot</option>
      <option value="XRP">Ripple</option>
      <option value="LTC">Litecoin</option>
    </select>
    <label for="walletInput">Wallet Private Key:</label>
    <input type="text" id="walletInput" autocomplete="off" required>
    <button type="submit">Add Wallet</button>
  </form>

  <div class="section">
    <button onclick="exportVault()">Export Vault</button>
    <input type="file" id="importFile" style="margin-top:0.5em;">
    <button onclick="importVault()">Import Vault</button>
  </div>

  <form class="section" onsubmit="changePin(); return false;">
    <h3>🔧 Change PIN</h3>
    <label for="currentPin">Current PIN:</label>
    <input type="password" id="currentPin" autocomplete="off" required>
    <label for="newPin">New PIN:</label>
    <input type="password" id="newPin" autocomplete="off" required minlength="6">
    <label for="confirmPin">Confirm New PIN:</label>
    <input type="password" id="confirmPin" autocomplete="off" required minlength="6">
    <button type="submit">Change PIN</button>
  </form>

  <section id="vaultDisplay" class="section" aria-live="polite"></section>
  <div id="messageDisplay" class="msg" style="display:none;"></div>
</main>

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
      if (result.success) showMessage("SMS sent successfully.");
      else showMessage("SMS failed: " + result.error, "error");
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
    const encrypted = await crypto.subtle.encrypt({name: "AES-GCM", iv: iv}, key, enc.encode(JSON.stringify(vault)));
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
        {name: "AES-GCM", iv: new Uint8Array(stored.iv)},
        key,
        new Uint8Array(stored.cipher)
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
    if (!stored) { showMessage("No vault found.", "error"); return; }
    const validation = validatePin(pin);
    if (validation !== "OK") { showMessage(validation, "error"); return; }
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
    if (newPin !== confirm) { showMessage("New PINs do not match.", "error"); return; }
    if (newPin.length < 6) { showMessage("New PIN must be at least 6 characters.", "error"); return; }
    const stored = JSON.parse(localStorage.getItem("vault") || "null");
    if (!stored) { showMessage("No vault found.", "error"); return; }
    const success = await decryptVault(current, stored);
    if (!success) { showMessage("Current PIN is incorrect.", "error"); return; }
    const encrypted = await encryptVault(newPin);
    localStorage.setItem("vault", JSON.stringify(encrypted));
    showMessage("PIN changed successfully.");
    document.getElementById("currentPin").value = "";
    document.getElementById("newPin").value = "";
    document.getElementById("confirmPin").value = "";
  }

  function setOwner() {
    const owner = document.getElementById("ownerInput").value.trim();
    if (!owner) { showMessage("Owner name is required.", "error"); return; }
    vault.owner = owner;
    displayVault();
    showMessage("Vault owner set.");
    document.getElementById("ownerInput").value = "";
  }

  function addWallet() {
    const key = document.getElementById("walletInput").value.trim();
    const type = document.getElementById("cryptoType").value;
    if (!key) { showMessage("Enter a private key.", "error"); return; }
    vault.wallets.push({type: type, key: key});
    displayVault();
    showMessage("Wallet added.");
    document.getElementById("walletInput").value = "";
  }

  async function exportVault() {
    const pin = document.getElementById("pinInput").value.trim();
    if (!pin) { showMessage("Enter your PIN to export.", "error"); return; }
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
    if (!file) { showMessage("Select a file first.", "error"); return; }
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
    html += `<strong>Owner:</strong> ${vault.owner ? vault.owner : "<em>(not set)</em>"}<br>`;
    html += `<strong>Wallets:</strong>`;
    if (vault.wallets.length === 0) {
      html += " <em>none</em>";
    } else {
      html += `<ul class="wallet-list">`;
      for (const w of vault.wallets) {
        html += `<li><strong>${w.type}:</strong> <code>${w.key}</code></li>`;
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
</script>
</body>
</html>
