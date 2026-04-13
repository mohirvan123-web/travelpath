<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Thawing Sync & Log</title>
    
    <link rel="manifest" href="manifest.json">
    <meta name="theme-color" content="#4A90E2">
    <link href="https://fonts.googleapis.com/css2?family=Poppins:wght@300;400;600;700&display=swap" rel="stylesheet">
    <script src="https://www.gstatic.com/firebasejs/8.10.0/firebase-app.js"></script>
    <script src="https://www.gstatic.com/firebasejs/8.10.0/firebase-database.js"></script>
    
    <style>
        :root {
            --color-white: #ffffff;
            --color-light-bg: #f7f9ff;
            --color-primary-blue: #4A90E2;
            --color-accent-pink: #FF69B4;
            --color-text-dark: #333333;
            --color-warning: #FFC0CB;
            --color-alert: #DC3545;
        }
        
        body {
            font-family: 'Poppins', sans-serif;
            background-color: var(--color-light-bg);
            margin: 0; padding: 20px 10px;
            color: var(--color-text-dark);
        }

        .main-container {
            background-color: var(--color-white);
            padding: 25px;
            border-radius: 16px;
            box-shadow: 0 8px 30px rgba(0, 0, 0, 0.08);
            max-width: 850px;
            margin: 0 auto 20px auto;
        }

        h1, h2 { text-align: center; color: var(--color-primary-blue); }

        /* GRID TIMER */
        .timer-list {
            display: grid;
            grid-template-columns: repeat(2, 1fr); 
            gap: 15px;
            margin-top: 20px;
        }

        .timer-card {
            border: 1px solid #e0e0e0;
            padding: 15px;
            border-radius: 12px;
            text-align: center;
            transition: all 0.3s;
        }

        .timer-card.running-mode { border-left: 5px solid var(--color-primary-blue); background-color: #f0f7ff; }
        .countdown-display { font-size: 2.2em; font-weight: 700; color: var(--color-primary-blue); margin: 10px 0; }
        
        .timer-controls { display: flex; gap: 8px; flex-wrap: wrap; justify-content: center; }
        .timer-controls input { width: 60px; padding: 8px; border-radius: 8px; border: 1px solid #ddd; text-align: center; }
        
        button { padding: 10px 15px; border: none; border-radius: 8px; cursor: pointer; font-weight: 600; }
        .start-btn { background-color: var(--color-primary-blue); color: white; }
        .reset-btn { background-color: #6c757d; color: white; }
        .stop-alarm-btn { background-color: var(--color-alert) !important; animation: pulse 0.5s infinite alternate; }

        /* TABLE LOG */
        .log-section { overflow-x: auto; margin-top: 15px; }
        table { width: 100%; border-collapse: collapse; font-size: 0.9em; }
        th { background-color: var(--color-primary-blue); color: white; padding: 10px; }
        td { padding: 10px; border-bottom: 1px solid #eee; text-align: center; }

        @keyframes pulse { from { opacity: 1; } to { opacity: 0.7; } }
        @media (max-width: 600px) { .timer-list { grid-template-columns: 1fr; } }
        .flash-red { background-color: #ff4d6d !important; }
    </style>
</head>
<body>

    <div class="main-container">
        <h1>Gacoan Timer Thawing 🧊</h1>
        <div class="timer-list" id="timer-list"></div>
    </div>

    <div class="main-container">
        <h2>Riwayat Thawing (Spreadsheet)</h2>
        <div class="log-section">
            <table>
                <thead>
                    <tr>
                        <th>Bahan</th>
                        <th>SOP</th>
                        <th>Ambil</th>
                        <th>Status</th>
                    </tr>
                </thead>
                <tbody id="log-body"></tbody>
            </table>
        </div>
    </div>

    <script>
        // CONFIG
        const SCRIPT_URL = "https://script.google.com/macros/s/AKfycbySO9S3nTjWTLb82cYqqL85YyudtFlWZz_3Ak6mYzm_fZED6XUq8ZGqgrNnj51Tvy9Hpw/exec";
        
        const firebaseConfig = {
            apiKey: "AIzaSyBtUlghTw806GuGuwOXGNgoqN6Rkcg0IMM",
            authDomain: "thawing-ec583.firebaseapp.com",
            databaseURL: "https://thawing-ec583-default-rtdb.asia-southeast1.firebasedatabase.app",
            projectId: "thawing-ec583"
        };

        firebase.initializeApp(firebaseConfig);
        const dbRef = firebase.database().ref('thawingTimers');
        const logRef = firebase.database().ref('thawingLogs');

        const THAWING_ITEMS = [
            { id: 1, name: "ADONAN", defaultTime: 40 },
            { id: 2, name: "ACIN", defaultTime: 120 },
            { id: 3, name: "MIE", defaultTime: 120 },
            { id: 4, name: "PENTOL", defaultTime: 120 },
            { id: 5, name: "SURAI NAGA", defaultTime: 120 },
            { id: 8, name: "UDANG KEJU", defaultTime: 120 }
        ];

        let activeIntervals = {};
        let speechQueue = [];
        let isSpeaking = false;

        // --- LOGIC: SPREADSHEET ---
        function sendToSpreadsheet(logData) {
            fetch(SCRIPT_URL, {
                method: "POST",
                mode: "no-cors",
                headers: { "Content-Type": "application/json" },
                body: JSON.stringify(logData)
            }).catch(e => console.error("Spreadsheet Error:", e));
        }

        function saveLog(itemName, endTimeMs) {
            const now = Date.now();
            const sop = new Date(endTimeMs).toLocaleTimeString('id-ID', {hour:'2-digit', minute:'2-digit'});
            const ambil = new Date(now).toLocaleTimeString('id-ID', {hour:'2-digit', minute:'2-digit'});
            
            const diff = Math.floor((now - endTimeMs) / 60000);
            const status = diff > 0 ? `Terlambat ${diff} mnt` : "Tepat Waktu";

            const data = { itemName, sopTime: sop, actualTime: ambil, status, timestamp: firebase.database.ServerValue.TIMESTAMP };
            
            logRef.push(data);
            sendToSpreadsheet(data);
        }

        // --- LOGIC: TIMER ---
        function formatTime(sec) {
            const h = Math.floor(sec / 3600).toString().padStart(2, '0');
            const m = Math.floor((sec % 3600) / 60).toString().padStart(2, '0');
            const s = (sec % 60).toString().padStart(2, '0');
            return `${h}:${m}:${s}`;
        }

        function speak(msg) {
            if (!speechQueue.includes(msg)) speechQueue.push(msg);
            if (!isSpeaking) {
                isSpeaking = true;
                const ut = new SpeechSynthesisUtterance(speechQueue.shift());
                ut.lang = 'id-ID';
                ut.onend = () => { isSpeaking = false; if(speechQueue.length > 0) speak(); };
                window.speechSynthesis.speak(ut);
            }
        }

        function tick(itemId, endTimeMs, inputMin) {
            const card = document.getElementById(`card-${itemId}`);
            const display = document.getElementById(`display-${itemId}`);
            if (!card || !display) return;

            const duration = Math.floor((endTimeMs - Date.now()) / 1000);
            
            if (duration > 0) {
                display.textContent = formatTime(duration);
                card.classList.add('running-mode');
                document.getElementById(`start-btn-${itemId}`).style.display = 'none';
                const rb = document.getElementById(`reset-btn-${itemId}`);
                rb.style.display = 'block';
                rb.textContent = 'RESET';
                rb.classList.remove('stop-alarm-btn');
                
                if (duration === 900) speak(`Peringatan, ${THAWING_ITEMS.find(i=>i.id==itemId).name} sisa 15 menit`);
                
                activeIntervals[itemId] = setTimeout(() => tick(itemId, endTimeMs, inputMin), 1000);
            } else {
                display.textContent = "WAKTU HABIS!";
                card.classList.add('flash-red');
                const rb = document.getElementById(`reset-btn-${itemId}`);
                rb.textContent = "AMBIL & STOP";
                rb.classList.add('stop-alarm-btn');
                speak(`Waktu habis, segera ambil ${THAWING_ITEMS.find(i=>i.id==itemId).name}`);
            }
        }

        function startTimer(id) {
            const min = document.getElementById(`input-${id}`).value;
            const end = Date.now() + (min * 60000);
            dbRef.child(id).set({ endTime: end, inputMinutes: min });
        }

        function resetTimer(id) {
            dbRef.child(id).once('value').then(snap => {
                const data = snap.val();
                if (data) saveLog(THAWING_ITEMS.find(i=>i.id==id).name, data.endTime);
                dbRef.child(id).remove();
                document.body.classList.remove('flash-red');
                window.speechSynthesis.cancel();
            });
        }

        // --- UI RENDER ---
        function init() {
            const list = document.getElementById('timer-list');
            THAWING_ITEMS.forEach(item => {
                const div = document.createElement('div');
                div.className = 'timer-card';
                div.id = `card-${item.id}`;
                div.innerHTML = `
                    <h3>${item.name}</h3>
                    <div class="countdown-display" id="display-${item.id}">${formatTime(item.defaultTime*60)}</div>
                    <div class="timer-controls">
                        <input type="number" id="input-${item.id}" value="${item.defaultTime}">
                        <button class="start-btn" id="start-btn-${item.id}" onclick="startTimer(${item.id})">START</button>
                        <button class="reset-btn" id="reset-btn-${item.id}" style="display:none" onclick="resetTimer(${item.id})">RESET</button>
                    </div>
                `;
                list.appendChild(div);
            });

            // Sync Timers
            dbRef.on('value', snap => {
                const data = snap.val() || {};
                THAWING_ITEMS.forEach(item => {
                    clearTimeout(activeIntervals[item.id]);
                    if (data[item.id]) {
                        tick(item.id, data[item.id].endTime, data[item.id].inputMinutes);
                    } else {
                        const card = document.getElementById(`card-${item.id}`);
                        card.classList.remove('running-mode', 'flash-red');
                        document.getElementById(`display-${item.id}`).textContent = formatTime(item.defaultTime*60);
                        document.getElementById(`start-btn-${item.id}`).style.display = 'block';
                        document.getElementById(`reset-btn-${item.id}`).style.display = 'none';
                    }
                });
            });

            // Sync Logs (Display last 5)
            logRef.orderByChild('timestamp').limitToLast(5).on('value', snap => {
                const body = document.getElementById('log-body');
                body.innerHTML = '';
                const logs = [];
                snap.forEach(s => { logs.unshift(s.val()); });
                logs.forEach(l => {
                    body.innerHTML += `<tr><td>${l.itemName}</td><td>${l.sopTime}</td><td>${l.actualTime}</td><td style="color:${l.status=='Tepat Waktu'?'green':'red'}">${l.status}</td></tr>`;
                });
            });
        }

        document.addEventListener('DOMContentLoaded', init);
    </script>
</body>
</html>
