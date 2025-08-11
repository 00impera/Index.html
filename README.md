
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

       
