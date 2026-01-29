<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Exception Report - Product Excellent</title>
    <style>
        :root { --primary: #2f80ed; --danger: #eb5757; --success: #27ae60; --gray: #f4f6f8; }
        body { font-family: 'Segoe UI', sans-serif; background: var(--gray); padding: 10px; margin-bottom: 100px; }
        .header { background: white; padding: 20px; border-radius: 10px; margin-bottom: 15px; box-shadow: 0 2px 5px rgba(0,0,0,0.1); }
        .section-title { background: #333; color: white; padding: 8px 15px; font-weight: bold; border-radius: 5px; margin-top: 15px; font-size: 13px; text-transform: uppercase; }
        .card { background: #fff; padding: 12px; border-radius: 8px; margin-top: 8px; border: 1px solid #ddd; }
        .item-row { display: flex; justify-content: space-between; gap: 10px; align-items: center; }
        .item-label { flex: 1; font-size: 13px; font-weight: 500; }
        select { padding: 6px; border-radius: 5px; width: 100px; cursor: pointer; }
        .fail-box { display: none; margin-top: 10px; padding-top: 10px; border-top: 1px solid #eee; }
        textarea, input[type="text"] { width: 100%; margin-top: 5px; padding: 8px; box-sizing: border-box; border: 1px solid #ccc; border-radius: 4px; font-size: 12px; }
        
        /* Gaya khusus untuk PDF */
        #pdfOutput { display: none; background: white; padding: 20px; }
        .pdf-item { margin-bottom: 15px; border-bottom: 1px solid #eee; padding-bottom: 10px; }
        .pdf-label { font-weight: bold; color: var(--danger); font-size: 12px; }
        .pdf-val { font-size: 12px; margin-top: 3px; color: #333; }

        .btn-container { position: fixed; bottom: 0; left: 0; right: 0; background: white; padding: 15px; display: flex; gap: 8px; box-shadow: 0 -2px 10px rgba(0,0,0,0.1); z-index: 1000; }
        button { flex: 1; padding: 12px 5px; border: none; border-radius: 8px; font-size: 13px; font-weight: bold; cursor: pointer; color: white; transition: 0.3s; }
        .btn-pdf { background: #6c757d; }
        .btn-wa { background: #25D366; }
        .btn-copy { background: #2f80ed; }
        button:active { transform: scale(0.95); }
    </style>
</head>
<body>

<div id="inputForm">
    <div class="header">
        <h2 style="margin:0; font-size: 18px; color: #333;">Input Temuan Harian</h2>
        <div style="display: flex; gap: 10px; margin-top: 10px;">
            <input type="text" id="modName" placeholder="Nama Manager">
            <input type="date" id="auditDate">
        </div>
    </div>

    <div id="checklistContainer"></div>
</div>

<div id="pdfOutput">
    <center>
        <h2 style="margin-bottom:0;">REPORT TEMUAN (NOT OK)</h2>
        <p style="margin-top:5px; border-bottom: 2px solid #000; padding-bottom: 10px;" id="pdfMeta"></p>
    </center>
    <div id="pdfContent"></div>
</div>

<div class="btn-container">
    <button class="btn-copy" onclick="copyToClipboard()">📋 Salin Teks</button>
    <button class="btn-pdf" onclick="generateExceptionPDF()">📄 PDF</button>
    <button class="btn-wa" onclick="sendToWhatsApp()">📱 Kirim WA</button>
</div>

<script src="https://cdnjs.cloudflare.com/ajax/libs/html2pdf.js/0.10.1/html2pdf.bundle.min.js"></script>
<script>
    const auditPoints = [
        { cat: "UNLOADING", items: ["Sampling suhu & Organoleptik", "Frozen stored < 30 menit", "Kebersihan area & pintu dumpster","Terdapat minimal 3 thermo tusuk"] },
        { cat: "STORING", items: ["Semua chiller berfungsi optimal","Semua freezer berfungsi optimal","Semua product di storage memiliki kodelot","Display max 1 karton","Bahan di atas pallet", "Chiller bebas bunga es","Suhu di display storage sesuai dengan data logger","Bahan sisa toples (No karet)", "Pemisahan bahan kimia"] },
        { cat: "THAWING", items: ["Timer Thawing berfungsi normal","Terdapat Table Thawing","Thawing tidak overtime", "Tempat thawing dalam keadaan bersih", "Thawing adonan max 1 karton dan dalam keadaan tertutup","Tidak ada packaging adonan yang bocor","Thawing jauh dari sampah dan bahan kimia"] },
        { cat: "PERSONAL HYGIENE", items: ["Crew dalam kitchen menggunakan APD lengkap", "APD dalam kondisi layak dan bersih", "Terdapat roller rambut di area unloading dan kasir", "Terdapat panduan cuci tangan di wastafel", "Tidak ada aksesoris apapun di kitchen (karet gelang termasuk)"] },
        { cat: "EQUIPMENT", items: ["Boiler dan basket bersih dari kerak", "Gelas Takar bar tidak berkerak", "Minyak goreng bersih", "Sendok takar topping lengkap", "Tutup steamer tidak peyok"] },
        { cat: "PREPARATION & PROCEDUR", items: ["Timer Boiler 2.10", "Suhu air boiler 98", "Griddle 7 detik", "Suhu Gridle 85-110", "Spatula level 0", "Perendaman cabe 20 menit", "Perebusan cabe 10 menit", "Penirisan cabe max 120 menit", "Suhu Fryer 140-160", "Penggorengan KM 3 menit", "kode lot produksi KM (max 12jam)", "Sangrai ACIN 15-30 menit", "1 pack Acin=10gr minyak baru", "Reheating ACIN 5 menit", "kode lot ACIN (max 4 jam)", "berat adonan PG 14-16gr", "Penggorengan PG 5 menit", "Dimsum goreng 4:45", "Penggorengan UR max 5 porsi", "Penggorengan UK max 9 porsi", "Penggorengan Lumpia max 10 porsi", "Steam siomay 16 menit", "Reheating siomay 2 menit (5menit untuk produk sisa)"] },
        { cat: "SERVING & PEST", items: ["Kebersihan klakat/piring", "Buku menu update", "Bebas hama Rongo", "Bebas lalat", "Semua lampu menyala", "Dumpstare dalam keadaan tertutup", "Tidak ada tempat sampah terbuka", "Tersedia sabun cuci tangan", "Tidak ada kursi dan meja yang tidak layak"] }
    ];

    const container = document.getElementById('checklistContainer');

    // Generate Form
    auditPoints.forEach(group => {
        const title = document.createElement('div');
        title.className = 'section-title';
        title.innerText = group.cat;
        container.appendChild(title);

        group.items.forEach(text => {
            if(text.trim() === "") return;
            const card = document.createElement('div');
            card.className = 'card item-node';
            card.innerHTML = `
                <div class="item-row">
                    <span class="item-label">${text}</span>
                    <select class="status" onchange="toggleFailBox(this)">
                        <option value="OK">✅ OK</option>
                        <option value="NOT OK">❌ NOT OK</option>
                    </select>
                </div>
                <div class="fail-box">
                    <textarea class="note" placeholder="Detail temuan / Masalah..."></textarea>
                    <input type="text" class="action" placeholder="Action Plan / Rencana Perbaikan...">
                </div>
            `;
            container.appendChild(card);
        });
    });

    function toggleFailBox(select) {
        const failBox = select.parentElement.nextElementSibling;
        failBox.style.display = (select.value === 'NOT OK' ? 'block' : 'none');
    }

    function formatMessage() {
        const mod = document.getElementById('modName').value;
        const tgl = document.getElementById('auditDate').value;
        
        if(!mod || !tgl) return { error: "Isi Nama & Tanggal!" };

        let msg = `🚨 *EXCEPTION REPORT - PRODUCT EXCELLENT* 🚨\n`;
        msg += `━━━━━━━━━━━━━━━━━━━━\n`;
        msg += `👤 *PIC:* ${mod}\n`;
        msg += `📅 *Tanggal:* ${tgl}\n`;
        msg += `━━━━━━━━━━━━━━━━━━━━\n\n`;

        let hasFindings = false;
        let count = 1;

        document.querySelectorAll('.item-node').forEach(node => {
            const status = node.querySelector('.status').value;
            if(status === 'NOT OK') {
                hasFindings = true;
                const label = node.querySelector('.item-label').innerText;
                const note = node.querySelector('.note').value || '-';
                const action = node.querySelector('.action').value || '-';

                msg += `${count}. ⚠️ *TEMUAN: ${label}*\n`;
                msg += `   📍 *Masalah:* ${note}\n`;
                msg += `   ✅ *Action:* ${action}\n\n`;
                count++;
            }
        });

        if(!hasFindings) msg += `✅ *Semua standar terpenuhi (CLEAR).*`;
        
        return { text: msg, hasFindings };
    }

    function sendToWhatsApp() {
        const data = formatMessage();
        if(data.error) { alert(data.error); return; }
        const url = `https://api.whatsapp.com/send?text=${encodeURIComponent(data.text)}`;
        window.open(url, '_blank');
    }

    function copyToClipboard() {
        const data = formatMessage();
        if(data.error) { alert(data.error); return; }
        navigator.clipboard.writeText(data.text).then(() => {
            alert("Teks laporan berhasil disalin! Silakan paste di grup WA.");
        });
    }

    function generateExceptionPDF() {
        const mod = document.getElementById('modName').value;
        const tgl = document.getElementById('auditDate').value;
        if(!mod || !tgl) { alert("Isi Nama & Tanggal!"); return; }

        const pdfContent = document.getElementById('pdfContent');
        pdfContent.innerHTML = '';
        document.getElementById('pdfMeta').innerText = `Manager: ${mod} | Tanggal: ${tgl}`;

        let hasFindings = false;

        document.querySelectorAll('.item-node').forEach(node => {
            if(node.querySelector('.status').value === 'NOT OK') {
                hasFindings = true;
                const label = node.querySelector('.item-label').innerText;
                const note = node.querySelector('.note').value;
                const action = node.querySelector('.action').value;

                pdfContent.innerHTML += `
                    <div class="pdf-item">
                        <div class="pdf-label">⚠️ TEMUAN: ${label}</div>
                        <div class="pdf-val"><b>Masalah:</b> ${note || '-'}</div>
                        <div class="pdf-val"><b>Action Plan:</b> ${action || '-'}</div>
                    </div>
                `;
            }
        });

        if(!hasFindings) {
            pdfContent.innerHTML = '<center><p style="color:green; margin-top:50px;">Tidak ada temuan (Semua OK).</p></center>';
        }

        const element = document.getElementById('pdfOutput');
        element.style.display = 'block';

        const opt = {
            margin: 0.5,
            filename: `Audit_${tgl}.pdf`,
            html2canvas: { scale: 2 },
            jsPDF: { unit: 'in', format: 'a4', orientation: 'portrait' }
        };

        html2pdf().set(opt).from(element).save().then(() => {
            element.style.display = 'none';
        });
    }
</script>
</body>
</html>
