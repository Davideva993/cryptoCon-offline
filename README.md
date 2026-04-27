# cryptoCon-offline
Offline E2EE encrypt/decrypt - QR key exchange - manual HKDF ratcheting 

-This script is designed to work completely offline. Standalone single HTML file, CDNs inlined.
    
    A) Password Protection
        When the app starts, the user is prompted to enter a personal password. This password is processed with PBKDF2 (600,000 iterations, SHA-256) to derive a strong protectionKey (AES-256-GCM). This protectionKey exists only in memory during the session and is never stored persistently. It is used to encrypt and decrypt all sensitive keys (rootKey and chain keys) before they are saved to localStorage).
        While the app is running, currentSessionChainKeys holds sensitive data (plaintext chain key) and is cleared on logout or page reload. Use this script locally.
        
    B) Secure Key Exchange (Offline Meeting)
        To establish communication, two (or more) users must meet in person. One user generates a fresh random rootKey (32 bytes) and displays it as a QR code. The other user(s) scans this QR code to receive the exact same rootKey.  This QR contains only the raw rootKey and is extremely sensitive. If it leaks, the whole system is compromised. 

    C) Message Encryption, Transmission and Decryption with Manual Ratcheting
        After the rootKey has been shared:  The sender types a message and clicks ENCRYPT. By default, the app uses the rootKey directly as the chain key for AES-GCM encryption. The resulting ciphertext (in base64) can be copied and sent to the receiver through any channel (even insecure ones). The receiver pastes the ciphertext and clicks DECRYPT to recover the original message.

    D) Manual HKDF ratcheting:
        At any time, either user can click "ADVANCE RATCHET". This derives a new chain key from the current one using HKDF and increases the ratchet counter. Both users must advance the ratchet the same number of times to stay synchronized. If their ratchet counters are different, decryption will fail. The first encryption/decryption operations always use the rootKey directly until the ratchet is advanced for the first time.

