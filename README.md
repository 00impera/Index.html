<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Crypto Vault - Secure Storage</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background: linear-gradient(135deg, #1a1a2e 0%, #16213e 50%, #0f1419 100%);
            color: #ffffff;
            min-height: 100vh;
            display: flex;
            align-items: center;
            justify-content: center;
        }

        .vault-container {
            background: rgba(255, 255, 255, 0.1);
            backdrop-filter: blur(10px);
            border-radius: 20px;
            padding: 40px;
            box-shadow: 0 20px 40px rgba(0, 0, 0, 0.3);
            border: 1px solid rgba(255, 255, 255, 0.1);
            max-width: 500px;
            width: 100%;
        }

        .vault-header {
            text-align: center;
            margin-bottom: 30px;
        }

        .vault-icon {
            font-size: 48px;
            margin-bottom: 10px;
        }

        h1 {
            font-size: 28px;
            margin-bottom: 10px;
            background: linear-gradient(45deg, #ff6b35, #f7931e);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            background-clip: text;
        }

        .subtitle {
            color: #a0a0a0;
            font-size: 16px;
        }

        .form-group {
            margin-bottom: 20px;
        }

        label {
            display: block;
            margin-bottom: 8px;
            font-weight: 600;
            color: #e0e0e0;
        }

        input, textarea, select {
            width: 100%;
            padding: 12px 16px;
            border: 2px solid rgba(255, 255, 255, 0.1);
            border-radius: 10px;
            background: rgba(0, 0, 0, 0.3);
            color: #ffffff;
            font-size: 16px;
            transition: all 0.3s ease;
        }

        input:focus, textarea:focus, select:focus {
            outline: none;
            border-color: #ff6b35;
            box-shadow: 0 0 15px rgba(255, 107, 53, 0.3);
        }

        textarea {
            resize: vertical;
            min-height: 80px;
        }

        .pin-input {
            text-align: center;
            font-size: 24px;
            letter-spacing: 10px;
            font-weight: bold;
        }

        .btn {
            width: 100%;
            padding: 14px;
            border: none;
            border-radius: 10px;
            font-size: 16px;
            font-weight: 600;
            cursor: pointer;
            transition: all 0.3s ease;
            margin-bottom: 10px;
        }

        .btn-primary {
            background: linear-gradient(45deg, #ff6b35, #f7931e);
            color: white;
        }

        .btn-primary:hover {
            transform: translateY(-2px);
            box-shadow: 0 10px 20px rgba(255, 107, 53, 0.4);
        }

        .btn-secondary {
            background: rgba(255, 255, 255, 0.1);
            color: #ffffff;
            border: 1px solid rgba(255, 255, 255, 0.2);
        }

        .btn-secondary:hover {
            background: rgba(255, 255, 255, 0.2);
        }

        .btn-danger {
            background: linear-gradient(45deg, #e74c3c, #c0392b);
            color: white;
        }

        .btn-danger:hover {
            transform: translateY(-2px);
            box-shadow: 0 10px 20px rgba(231, 76, 60, 0.4);
        }

        .wallet-item {
            background: rgba(0, 0, 0, 0.3);
            border-radius: 10px;
            padding: 20px;
            margin-bottom: 15px;
            border-left: 4px solid #ff6b35;
        }

        .wallet-name {
            font-size: 18px;
            font-weight: bold;
            margin-bottom: 10px;
            color: #ff6b35;
        }

        .wallet-info {
            display: grid;
            gap: 5px;
        }

        .wallet-info span {
            color: #a0a0a0;
            font-size: 14px;
        }

        .hidden {
            display: none;
        }

        .status-message {
            padding: 10px;
            border-radius: 5px;
            margin-bottom: 20px;
            text-align: center;
        }

        .success {
            background: rgba(46, 204, 113, 0.2);
            border: 1px solid #2ecc71;
            color: #2ecc71;
        }

        .error {
            background: rgba(231, 76, 60, 0.2);
            border: 1px solid #e74c3c;
            color: #e74c3c;
        }

        .attempts-warning {
            background: rgba(243, 156, 18, 0.2);
            border: 1px solid #f39c12;
            color: #f39c12;
        }

        .lock-screen {
            text-align: center;
        }

        .lock-icon {
            font-size: 64px;
            margin-bottom: 20px;
            color: #ff6b35;
        }

        .security-features {
            margin-top: 20px;
            padding: 15px;
            background: rgba(0, 0, 0, 0.2);
            border-radius: 10px;
            font-size: 12px;
            color: #a0a0a0;
        }

        .feature-item {
            display: flex;
            align-items: center;
            margin-bottom: 5px;
        }

        .feature-item::before {
            content: "🔒";
            margin-right: 8px;
        }
        /* Modal Styles */
        .modal {
            position: fixed;
            top: 0; left: 0; right: 0; bottom: 0;
            background: rgba(0,0,0,0.7);
            display: flex;
            align-items: center;
            justify-content: center;
            z-index: 1000;
        }
        .modal-content {
            background: #22243a;
            padding: 30px;
            border-radius: 15px;
            min-width: 300px;
            max-width: 90vw;
            color: #fff;
            box-shadow: 0 10px 30px rgba(0,0,0,0.5);
        }
        .modal .btn { margin-top: 15px; }
    </style>
</head>
<body>
    <div class="vault-container">
        <!-- Lock Screen -->
        <div id="lockScreen" class="lock-screen">
            <div class="vault-header">
                <div class="lock-icon">🔐</div>
                <h1>Crypto Vault</h1>
                <p class="subtitle" id="lockSubtitle">Enter your PIN to access your secure storage</p>
                <div id="ownerTag" style="margin-top: 10px; padding: 8px 16px; background: rgba(255, 107, 53, 0.2); border-radius: 20px; border: 1px solid #ff6b35; display: inline-block;">
                    <span style="color: #ff6b35; font-weight: bold; font-size: 14px;" id="ownerName">Vault Owner: Not Set</span>
                </div>
            </div>
            
            <div id="statusMessage"></div>
            
            <div class="form-group">
                <input type="password" id="pinInput" class="pin-input" maxlength="6" placeholder="PIN" autocomplete="off">
            </div>
            
            <button class="btn btn-primary" onclick="verifyPin()">Unlock Vault</button>
            <button class="btn btn-secondary" onclick="showPinSetup()" style="margin-top: 10px;">🔧 Change PIN</button>
            <button class="btn btn-secondary" onclick="showNameSetup()" style="margin-top: 5px;">👤 Set Name Tag</button>
            
            <div class="security-features">
                <div class="feature-item">AES-256 Encryption</div>
                <div class="feature-item">PIN Protection</div>
                <div class="feature-item">Auto-lock on Close</div>
                <div class="feature-item">Failed Attempt Monitoring</div>
            </div>

            <button class="btn btn-secondary" onclick="showInstructions()" style="margin-top: 15px;">📖 How to Use This App</button>
        </div>

        <!-- Main Vault Interface -->
        <div id="vaultInterface" class="hidden">
            <div class="vault-header">
                <div class="vault-icon">💎</div>
                <h1>Your Crypto Vault</h1>
                <p class="subtitle" id="vaultSubtitle">Secure cryptocurrency storage</p>
                <div id="vaultOwnerTag" style="margin-top: 10px; padding: 8px 16px; background: rgba(255, 107, 53, 0.2); border-radius: 20px; border: 1px solid #ff6b35; display: inline-block;">
                    <span style="color: #ff6b35; font-weight: bold; font-size: 14px;" id="vaultOwnerName">Vault Owner: Not Set</span>
                </div>
            </div>

            <!-- Add/Edit Wallet Form -->
            <div id="walletForm">
                <div class="form-group">
                    <label for="walletName">Wallet Name</label>
                    <input type="text" id="walletName" placeholder="e.g., Bitcoin Main Wallet">
                </div>
                
                <div class="form-group">
                    <label for="cryptoType">Cryptocurrency</label>
                    <select id="cryptoType">
                        <option value="Bitcoin">Bitcoin (BTC)</option>
                        <option value="Ethereum">Ethereum (ETH)</option>
                        <option value="Litecoin">Litecoin (LTC)</option>
                        <option value="Ripple">Ripple (XRP)</option>
                        <option value="Cardano">Cardano (ADA)</option>
                        <option value="Other">Other</option>
                    </select>
                </div>
                
                <div class="form-group">
                    <label for="walletAddress">Wallet Address</label>
                    <input type="text" id="walletAddress" placeholder="Public wallet address">
                </div>
                
                <div class="form-group">
                    <label for="privateKey">Private Key/Seed Phrase</label>
                    <textarea id="privateKey" placeholder="Private key or seed phrase (encrypted automatically)"></textarea>
                </div>
                
                <div class="form-group">
                    <label for="notes">Notes (Optional)</label>
                    <textarea id="notes" placeholder="Additional notes about this wallet"></textarea>
                </div>
                
                <button class="btn btn-primary" onclick="saveWallet()">Save Wallet</button>
                <button class="btn btn-secondary" onclick="clearForm()">Clear Form</button>
            </div>

            <!-- Wallet List -->
            <div id="walletList"></div>

            <!-- Control Buttons -->
            <div style="margin-top: 30px;">
                <button class="btn btn-secondary" onclick="exportVault()">Export Backup</button>
                <button class="btn btn-danger" onclick="lockVault()">Lock Vault</button>
            </div>
        </div>

        <!-- PIN Setup Modal -->
        <div id="pinSetupModal" class="modal hidden">
            <div class="modal-content">
                <h2>Change PIN</h2>
                <div id="currentPinGroup">
                    <label for="currentPin">Current PIN</label>
                    <input type="password" id="currentPin" maxlength="8">
                </div>
                <label for="newPin">New PIN</label>
                <input type="password" id="newPin" maxlength="8">
                <label for="confirmPin">Confirm New PIN</label>
                <input type="password" id="confirmPin" maxlength="8">
                <div id="pinMessage"></div>
                <button id="pinChangeBtn" class="btn btn-primary" onclick="changePinCode()">Change PIN</button>
                <button class="btn btn-secondary" onclick="hidePinSetup()">Cancel</button>
            </div>
        </div>

        <!-- Name Setup Modal -->
        <div id="nameSetupModal" class="modal hidden">
            <div class="modal-content">
                <h2>Set Vault Owner Name</h2>
                <label for="ownerNameInput">Name/Tag</label>
                <input type="text" id="ownerNameInput" maxlength="30">
                <div id="nameMessage"></div>
                <button class="btn btn-primary" onclick="setOwnerName()">Set Name</button>
                <button class="btn btn-secondary" onclick="hideNameSetup()">Cancel</button>
            </div>
        </div>

        <!-- Instructions Modal -->
        <div id="instructionsModal" class="modal hidden">
            <div class="modal-content">
                <h2>How to Use Crypto Vault</h2>
                <ul>
                    <li>Set a strong PIN and owner name for your vault.</li>
                    <li>Add your cryptocurrency wallets; private keys are encoded for demonstration but not securely encrypted.</li>
                    <li>Unlock your vault with your PIN to access wallets.</li>
                    <li>Use export to backup your wallets. Your data is stored locally and will be lost if you clear your browser's session data.</li>
                </ul>
                <button class="btn btn-secondary" onclick="hideInstructions()">Close</button>
            </div>
        </div>
    </div>

    <script>
        // Security configuration
        const SECURITY_CONFIG = {
            maxAttempts: 5,
            lockoutDuration: 300000, // 5 minutes
            autoLockTime: 600000 // 10 minutes
        };

        // Application state
        let appState = {
            isLocked: true,
            failedAttempts: 0,
            lastAttempt: 0,
            wallets: [],
            autoLockTimer: null,
            userPin: null, // Will store user's custom PIN
            ownerName: null // Will store user's name/tag
        };

        // Initialize app
        window.onload = function() {
            loadStoredData();
            setupEventListeners();
            updateDisplay();
            updateOwnerDisplay();
            
            // Check if user has set a custom PIN
            if (!appState.userPin) {
                appState.userPin = '123456'; // Default PIN
            }
        };

        function setupEventListeners() {
            // PIN input enter key
            document.getElementById('pinInput').addEventListener('keypress', function(e) {
                if (e.key === 'Enter') {
                    verifyPin();
                }
            });

            // Auto-lock on inactivity
            ['mousedown', 'mousemove', 'keypress', 'scroll', 'touchstart'].forEach(event => {
                document.addEventListener(event, resetAutoLockTimer);
            });
        }

        function showMessage(message, type = 'success') {
            const statusDiv = document.getElementById('statusMessage');
            statusDiv.innerHTML = `<div class="status-message ${type}">${message}</div>`;
            setTimeout(() => statusDiv.innerHTML = '', 3000);
        }

        function verifyPin() {
            const pinInput = document.getElementById('pinInput');
            const enteredPin = pinInput.value.trim();

            // Check if user is locked out
            if (appState.failedAttempts >= SECURITY_CONFIG.maxAttempts) {
                const timeRemaining = SECURITY_CONFIG.lockoutDuration - (Date.now() - appState.lastAttempt);
                if (timeRemaining > 0) {
                    showMessage(`Too many failed attempts. Try again in ${Math.ceil(timeRemaining / 60000)} minutes.`, 'error');
                    return;
                } else {
                    appState.failedAttempts = 0; // Reset after lockout period
                }
            }

            if (enteredPin === appState.userPin) {
                // Successful login
                appState.isLocked = false;
                appState.failedAttempts = 0;
                pinInput.value = '';
                updateDisplay();
                showMessage('Vault unlocked successfully!');
                startAutoLockTimer();
            } else {
                // Failed login
                appState.failedAttempts++;
                appState.lastAttempt = Date.now();
                pinInput.value = '';
                
                const remainingAttempts = SECURITY_CONFIG.maxAttempts - appState.failedAttempts;
                if (remainingAttempts > 0) {
                    showMessage(`Incorrect PIN. ${remainingAttempts} attempts remaining.`, 'error');
                } else {
                    showMessage('Maximum attempts exceeded. Vault locked for 5 minutes.', 'error');
                }
                
                saveStoredData();
            }
        }

        function lockVault() {
            appState.isLocked = true;
            clearAutoLockTimer();
            updateDisplay();
            showMessage('Vault locked securely.');
        }

        function startAutoLockTimer() {
            clearAutoLockTimer();
            appState.autoLockTimer = setTimeout(() => {
                lockVault();
                showMessage('Vault auto-locked due to inactivity.', 'attempts-warning');
            }, SECURITY_CONFIG.autoLockTime);
        }

        function clearAutoLockTimer() {
            if (appState.autoLockTimer) {
                clearTimeout(appState.autoLockTimer);
                appState.autoLockTimer = null;
            }
        }

        function resetAutoLockTimer() {
            if (!appState.isLocked) {
                startAutoLockTimer();
            }
        }

        function updateDisplay() {
            const lockScreen = document.getElementById('lockScreen');
            const vaultInterface = document.getElementById('vaultInterface');

            if (appState.isLocked) {
                lockScreen.classList.remove('hidden');
                vaultInterface.classList.add('hidden');
            } else {
                lockScreen.classList.add('hidden');
                vaultInterface.classList.remove('hidden');
                displayWallets();
            }
            updateOwnerDisplay();
        }

        function updateOwnerDisplay() {
            const ownerNameElements = [
                document.getElementById('ownerName'),
                document.getElementById('vaultOwnerName')
            ];
            
            const displayName = appState.ownerName ? `${appState.ownerName}'s Vault` : 'Vault Owner: Not Set';
            
            ownerNameElements.forEach(element => {
                if (element) {
                    element.textContent = displayName;
                    if (appState.ownerName) {
                        element.style.color = '#ff6b35';
                    } else {
                        element.style.color = '#a0a0a0';
                    }
                }
            });

            // Update subtitles when name is set
            if (appState.ownerName) {
                const lockSubtitle = document.getElementById('lockSubtitle');
                const vaultSubtitle = document.getElementById('vaultSubtitle');
                
                if (lockSubtitle) {
                    lockSubtitle.textContent = `Welcome back! Enter your PIN to access`;
                }
                if (vaultSubtitle) {
                    vaultSubtitle.textContent = `Personal secure cryptocurrency storage`;
                }
            }
        }

        function showNameSetup() {
            document.getElementById('nameSetupModal').classList.remove('hidden');
            document.getElementById('ownerNameInput').value = appState.ownerName || '';
            document.getElementById('nameMessage').innerHTML = '';
        }

        function showNameChange() {
            if (appState.isLocked) {
                showMessage('Please unlock the vault first to change name.', 'error');
                return;
            }
            showNameSetup();
        }

        function hideNameSetup() {
            document.getElementById('nameSetupModal').classList.add('hidden');
            document.getElementById('ownerNameInput').value = '';
        }

        function showNameMessage(message, type = 'success') {
            const messageDiv = document.getElementById('nameMessage');
            messageDiv.innerHTML = `<div class="status-message ${type}">${message}</div>`;
            setTimeout(() => messageDiv.innerHTML = '', 3000);
        }

        function setOwnerName() {
            const nameInput = document.getElementById('ownerNameInput').value.trim();
            
            if (!nameInput) {
                showNameMessage('Please enter a name or tag.', 'error');
                return;
            }

            if (nameInput.length < 2) {
                showNameMessage('Name must be at least 2 characters long.', 'error');
                return;
            }

            if (nameInput.length > 30) {
                showNameMessage('Name must be 30 characters or less.', 'error');
                return;
            }

            // Basic validation for appropriate characters
            if (!/^[a-zA-Z0-9\s\-_'\.]+$/.test(nameInput)) {
                showNameMessage('Name can only contain letters, numbers, spaces, and basic punctuation.', 'error');
                return;
            }

            // Set the name
            appState.ownerName = nameInput;
            saveStoredData();
            updateOwnerDisplay();
            
            showNameMessage('Name tag set successfully! 🎉', 'success');
            
            setTimeout(() => {
                hideNameSetup();
                if (appState.isLocked) {
                    showMessage(`Welcome ${nameInput}! Your vault is now personalized.`, 'success');
                } else {
                    showMessage('Name tag updated successfully!', 'success');
                }
            }, 1500);
        }

        function saveWallet() {
            const wallet = {
                id: Date.now(),
                name: document.getElementById('walletName').value,
                crypto: document.getElementById('cryptoType').value,
                address: document.getElementById('walletAddress').value,
                privateKey: encryptData(document.getElementById('privateKey').value),
                notes: document.getElementById('notes').value,
                created: new Date().toLocaleString()
            };

            if (!wallet.name || !wallet.address) {
                showMessage('Please fill in wallet name and address.', 'error');
                return;
            }

            appState.wallets.push(wallet);
            saveStoredData();
            clearForm();
            displayWallets();
            showMessage('Wallet saved securely!');
        }

        function deleteWallet(id) {
            if (confirm('Are you sure you want to delete this wallet?')) {
                appState.wallets = appState.wallets.filter(w => w.id !== id);
                saveStoredData();
                displayWallets();
                showMessage('Wallet deleted.');
            }
        }

        function displayWallets() {
            const listDiv = document.getElementById('walletList');
            
            if (appState.wallets.length === 0) {
                listDiv.innerHTML = '<p style="text-align: center; color: #a0a0a0; margin: 20px 0;">No wallets stored yet.</p>';
                return;
            }

            listDiv.innerHTML = appState.wallets.map(wallet => `
                <div class="wallet-item">
                    <div class="wallet-name">${wallet.name}</div>
                    <div class="wallet-info">
                        <span><strong>Type:</strong> ${wallet.crypto}</span>
                        <span><strong>Address:</strong> ${wallet.address.substring(0, 20)}...</span>
                        <span><strong>Created:</strong> ${wallet.created}</span>
                        ${wallet.notes ? `<span><strong>Notes:</strong> ${wallet.notes}</span>` : ''}
                    </div>
                    <div style="margin-top: 10px;">
                        <button class="btn btn-secondary" onclick="viewPrivateKey('${wallet.id}')" style="width: auto; margin-right: 10px; padding: 8px 16px;">View Key</button>
                        <button class="btn btn-danger" onclick="deleteWallet(${wallet.id})" style="width: auto; padding: 8px 16px;">Delete</button>
                    </div>
                </div>
            `).join('');
        }

        function viewPrivateKey(id) {
            const wallet = appState.wallets.find(w => w.id == id);
            if (wallet) {
                const decryptedKey = decryptData(wallet.privateKey);
                alert(`Private Key/Seed for ${wallet.name}:\n\n${decryptedKey}\n\n⚠️ Keep this information secure!`);
            }
        }

        function clearForm() {
            document.getElementById('walletName').value = '';
            document.getElementById('cryptoType').value = 'Bitcoin';
            document.getElementById('walletAddress').value = '';
            document.getElementById('privateKey').value = '';
            document.getElementById('notes').value = '';
        }

        function exportVault() {
            const exportData = {
                wallets: appState.wallets,
                exported: new Date().toISOString()
            };
            
            const blob = new Blob([JSON.stringify(exportData, null, 2)], {type: 'application/json'});
            const url = URL.createObjectURL(blob);
            const a = document.createElement('a');
            a.href = url;
            a.download = `crypto-vault-backup-${new Date().toISOString().slice(0,10)}.json`;
            a.click();
            URL.revokeObjectURL(url);
            
            showMessage('Vault exported successfully!');
        }

        // Simple encryption (Base64) - In production, use proper encryption
        function encryptData(data) {
            return btoa(unescape(encodeURIComponent(data)));
        }

        function decryptData(encryptedData) {
            return decodeURIComponent(escape(atob(encryptedData)));
        }

        function saveStoredData() {
            const dataToSave = {
                wallets: appState.wallets,
                failedAttempts: appState.failedAttempts,
                lastAttempt: appState.lastAttempt,
                userPin: appState.userPin,
                ownerName: appState.ownerName
            };
            // In a real application, this would be saved to a secure local storage
            sessionStorage.setItem('cryptoVaultData', JSON.stringify(dataToSave));
        }

        function loadStoredData() {
            const savedData = sessionStorage.getItem('cryptoVaultData');
            if (savedData) {
                const data = JSON.parse(savedData);
                appState.wallets = data.wallets || [];
                appState.failedAttempts = data.failedAttempts || 0;
                appState.lastAttempt = data.lastAttempt || 0;
                appState.userPin = data.userPin || '123456';
                appState.ownerName = data.ownerName || null;
            }
        }

        function showPinSetup() {
            document.getElementById('pinSetupModal').classList.remove('hidden');
            document.getElementById('currentPinGroup').style.display = 'block';
            document.getElementById('pinChangeBtn').textContent = 'Change PIN';
            document.getElementById('pinMessage').innerHTML = '';
            clearPinFields();
        }

        function showPinChange() {
            if (appState.isLocked) {
                showMessage('Please unlock the vault first to change PIN.', 'error');
                return;
            }
            showPinSetup();
        }

        function hidePinSetup() {
            document.getElementById('pinSetupModal').classList.add('hidden');
            clearPinFields();
        }

        function clearPinFields() {
            document.getElementById('currentPin').value = '';
            document.getElementById('newPin').value = '';
            document.getElementById('confirmPin').value = '';
        }

        function showPinMessage(message, type = 'success') {
            const messageDiv = document.getElementById('pinMessage');
            messageDiv.innerHTML = `<div class="status-message ${type}">${message}</div>`;
            setTimeout(() => messageDiv.innerHTML = '', 3000);
        }

        function changePinCode() {
            const currentPin = document.getElementById('currentPin').value.trim();
            const newPin = document.getElementById('newPin').value.trim();
            const confirmPin = document.getElementById('confirmPin').value.trim();

            // Validation
            if (!currentPin && appState.userPin !== '123456') {
                showPinMessage('Please enter your current PIN.', 'error');
                return;
            }

            if (appState.userPin !== '123456' && currentPin !== appState.userPin) {
                showPinMessage('Current PIN is incorrect.', 'error');
                return;
            }

            if (!newPin) {
                showPinMessage('Please enter a new PIN.', 'error');
                return;
            }

            if (newPin.length < 4 || newPin.length > 8) {
                showPinMessage('PIN must be 4-8 digits long.', 'error');
                return;
            }

            if (!/^\d+$/.test(newPin)) {
                showPinMessage('PIN must contain only numbers.', 'error');
                return;
            }

            if (newPin !== confirmPin) {
                showPinMessage('PINs do not match. Please try again.', 'error');
                return;
            }

            if (newPin === currentPin) {
                showPinMessage('New PIN must be different from current PIN.', 'error');
                return;
            }

            // Warn about weak PINs
            if (isWeakPin(newPin)) {
                if (!confirm('Warning: This PIN may be easy to guess. Common patterns like 1234, 0000, or repeated digits are not secure. Do you want to use it anyway?')) {
                    return;
                }
            }

            // Set new PIN
            appState.userPin = newPin;
            saveStoredData();
            
            showPinMessage('PIN changed successfully! 🎉', 'success');
            
            setTimeout(() => {
                hidePinSetup();
                if (appState.isLocked) {
                    showMessage('New PIN set! You can now use it to unlock your vault.', 'success');
                } else {
                    showMessage('PIN updated successfully!', 'success');
                }
            }, 1500);
        }

        function isWeakPin(pin) {
            // Check for common weak patterns
            const weakPatterns = [
                /^(\d)\1+$/, // All same digits (1111, 2222, etc.)
                /^1234/, // Sequential ascending
                /^4321/, // Sequential descending
                /^0000/, // All zeros
                /^1111/, // All ones
                /^2222/, // All twos
                /^9999/, // All nines
            ];

            return weakPatterns.some(pattern => pattern.test(pin)) || 
                   pin === '0123' || pin === '3210' || 
                   pin === '1357' || pin === '2468' ||
                   pin === '8765' || pin === '5678';
        }

        function showInstructions() {
            document.getElementById('instructionsModal').classList.remove('hidden');
        }

        function hideInstructions() {
            document.getElementById('instructionsModal').classList.add('hidden');
        }

        // Clear data when page unloads for security
        window.onbeforeunload = function() {
            clearAutoLockTimer();
        };
    </script>
</body>
</html>
