<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Exception Report - Product Excellent</title>
    <style>
        :root { --primary: #2f80ed; --danger: #eb5757; --gray: #f4f6f8; }
        body { font-family: 'Segoe UI', sans-serif; background: var(--gray); padding: 10px; }
        .no-print { margin-bottom: 20px; }
        .header { background: white; padding: 20px; border-radius: 10px; margin-bottom: 15px; box-shadow: 0 2px 5px rgba(0,0,0,0.1); }
        .section-title { background: #333; color: white; padding: 8px 15px; font-weight: bold; border-radius: 5px; margin-top: 15px; font-size: 13px; }
        .card { background: #fff; padding: 12px; border-radius: 8px; margin-top: 8px; border: 1px solid #ddd; }
        .item-row { display: flex; justify-content: space-between; gap: 10px; align-items: center; }
        .item-label { flex: 1; font-size: 13px; }
        select { padding: 6px; border-radius: 5px; width: 100px; }
        .fail-box { display: none; margin-top: 10px; padding-top: 10px; border-top: 1px solid #eee; }
        textarea, input[type="text"] { width: 100%; margin-top: 5px; padding: 8px; box-sizing: border-box; border: 1px solid #ccc; border-radius: 4px; font-size: 12px; }
        
        /* Gaya khusus untuk PDF */
        #pdfOutput { display: none; background: white; padding: 20px; }
        .pdf-item { margin-bottom: 15px; border-bottom: 1px solid #eee; padding-bottom: 10px; }
        .pdf-label { font-weight: bold; color: var(--danger); font-size: 12px; }
        .pdf-val { font-size: 12px; margin-top: 3px; color: #333; }

        .btn-container { position: sticky; bottom: 10px; background: var(--gray); padding: 10px 0; }
        button { width: 100%; padding: 16px; background: var(--primary); color: white; border: none; border-radius: 10px; font-size: 16px; font-weight: bold; cursor: pointer; }
    </style>
</head>
<body>

<div id="inputForm">
    <div class="header">
        <h2 style="margin:0; font-size: 18px;">Input Temuan Harian</h2>
        <div style="display: flex; gap: 10px; margin-top: 10px;">
            <input type="text" id="modName" style="flex:1; padding:8px;" placeholder="Nama Manager">
            <input type="date" id="auditDate" style="flex:1; padding:8px;">
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
    <button onclick="generateExceptionPDF()">Download Temuan Saja (PDF)</button>
</div>

<script src="https://cdnjs.cloudflare.com/ajax/libs/html2pdf.js/0.10.1/html2pdf.bundle.min.js"></script>
<script>
    const auditPoints = [
        { cat: "UNLOADING", items: ["Sampling suhu & visual produk", "Frozen stored < 30 menit", "Kebersihan area & pintu dumpster"] },
        { cat: "STORING", items: ["Mie di atas pallet", "Chiller bebas bunga es", "Bahan sisa di-seal (No karet)", "Pemisahan bahan kimia"] },
        { cat: "PREPARATION", items: ["Reheating siomay 1-2 menit", "Kebersihan keranjang boiler", "Minyak goreng bersih", "Sendok takar topping"] },
        { cat: "SERVING & PEST", items: ["Kebersihan klakat/piring", "Buku menu update", "Bebas hama Rongo", "Bebas lalat"] }
    ];

    const container = document.getElementById('checklistContainer');

    auditPoints.forEach(group => {
        const title = document.createElement('div');
        title.className = 'section-title';
        title.innerText = group.cat;
        container.appendChild(title);

        group.items.forEach(text => {
            const card = document.createElement('div');
            card.className = 'card item-node';
            card.innerHTML = `
                <div class="item-row">
                    <span class="item-label">${text}</span>
                    <select class="status" onchange="this.parentElement.nextElementSibling.style.display = (this.value=='NOT OK'?'block':'none')">
                        <option value="OK">✅ OK</option>
                        <option value="NOT OK">❌ NOT OK</option>
                    </select>
                </div>
                <div class="fail-box">
                    <textarea class="note" placeholder="Detail temuan..."></textarea>
                    <input type="text" class="action" placeholder="Action Plan...">
                </div>
            `;
            container.appendChild(card);
        });
    });

    function generateExceptionPDF() {
        const mod = document.getElementById('modName').value;
        const tgl = document.getElementById('auditDate').value;
        if(!mod || !tgl) { alert("Isi Nama & Tanggal!"); return; }

        const pdfContent = document.getElementById('pdfContent');
        pdfContent.innerHTML = ''; // Reset isi
        document.getElementById('pdfMeta').innerText = `Manager: ${mod} | Tanggal: ${tgl}`;

        let hasFindings = false;

        document.querySelectorAll('.item-node').forEach(node => {
            const status = node.querySelector('.status').value;
            if(status === 'NOT OK') {
                hasFindings = true;
                const label = node.querySelector('.item-label').innerText;
                const note = node.querySelector('.note').value;
                const action = node.querySelector('.action').value;

                pdfContent.innerHTML += `
                    <div class="pdf-item">
                        <div class="pdf-label">⚠️ TEMUAN: ${label}</div>
                        <div class="pdf-val"><b>Masalah:</b> ${note || '-'}</div>
                        <div class="pdf-val"><b>Rencana Perbaikan:</b> ${action || '-'}</div>
                    </div>
                `;
            }
        });

        if(!hasFindings) {
            pdfContent.innerHTML = '<center><p style="color:green; margin-top:50px;">Semua poin audit sesuai standar (OK). Tidak ada temuan.</p></center>';
        }

        // Proses Export
        const element = document.getElementById('pdfOutput');
        element.style.display = 'block'; // Tampilkan sebentar untuk dicapture

        const opt = {
            margin: 0.5,
            filename: `Temuan_Audit_${tgl}.pdf`,
            html2canvas: { scale: 2 },
            jsPDF: { unit: 'in', format: 'a4', orientation: 'portrait' }
        };

        html2pdf().set(opt).from(element).save().then(() => {
            element.style.display = 'none'; // Sembunyikan kembali
        });
    }
</script>
</body>
</html>
