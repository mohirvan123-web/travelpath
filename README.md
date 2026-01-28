<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Full Audit Checklist - Product Excellent</title>
    <style>
        :root { --primary: #2f80ed; --danger: #eb5757; --success: #27ae60; --gray: #f4f6f8; }
        body { font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; background: var(--gray); padding: 10px; color: #333; }
        .header { text-align: center; background: white; padding: 20px; border-radius: 10px; margin-bottom: 15px; box-shadow: 0 2px 5px rgba(0,0,0,0.1); }
        h2 { margin: 0; color: #222; font-size: 1.4rem; }
        .section-title { background: #333; color: white; padding: 10px 15px; font-weight: bold; border-radius: 5px; margin-top: 25px; font-size: 14px; text-transform: uppercase; }
        .card { background: #fff; padding: 15px; border-radius: 8px; margin-top: 10px; box-shadow: 0 2px 6px rgba(0,0,0,0.05); }
        .item-row { display: flex; justify-content: space-between; align-items: flex-start; gap: 10px; }
        .item-label { flex: 1; font-size: 13.5px; line-height: 1.4; font-weight: 500; }
        select { padding: 6px; border-radius: 5px; border: 1px solid #ccc; width: 100px; font-size: 12px; }
        .fail-box { display: none; background: #fff5f5; border-left: 4px solid var(--danger); padding: 12px; border-radius: 0 8px 8px 0; margin-top: 10px; }
        .fail-box input, .fail-box textarea { width: 100%; margin-top: 8px; padding: 8px; box-sizing: border-box; border: 1px solid #ddd; border-radius: 4px; font-size: 13px; }
        .btn-container { position: sticky; bottom: 10px; padding: 10px 0; }
        button { width: 100%; padding: 16px; background: var(--primary); color: white; border: none; border-radius: 10px; font-size: 16px; font-weight: bold; cursor: pointer; box-shadow: 0 4px 12px rgba(47,128,237,0.3); }
        input[type="text"], input[type="date"] { border: 1px solid #ddd; border-radius: 5px; }
    </style>
</head>
<body>

<div id="printableArea">
    <div class="header">
        <h2>DAILY CHECKLIST AUDIT</h2>
        <p style="font-size: 12px; color: #666;">Store: Malang Raya Mondoroko</p>
        <div style="display: flex; gap: 10px; margin-top: 15px;">
            <input type="text" id="modName" style="flex:1; padding:8px;" placeholder="Nama Manager">
            <input type="date" id="auditDate" style="flex:1; padding:8px;">
        </div>
    </div>

    <div id="checklistContainer"></div>
</div>

<div class="btn-container">
    <button onclick="generatePDF()">DOWNLOAD LAPORAN PDF</button>
</div>

<script src="https://cdnjs.cloudflare.com/ajax/libs/html2pdf.js/0.10.1/html2pdf.bundle.min.js"></script>
<script>
    const fullAuditData = [
        { 
            section: "1. Procedure Unloading", 
            items: [
                "Sampling produk barang datang (Suhu, Visual, Organoleptik cabai)",
                "Frozen product disimpan ke Cold Storage (CS) maksimal 30 menit",
                "Kebersihan area unloading (Bebas sampah plastik/semen/material)",
                "Pintu Dumpster menutup rapat (Mencegah lalat masuk ke area loading)"
            ]
        },
        { 
            section: "2. Procedure Storing & Inspection", 
            items: [
                "Penyimpanan Mie di atas pallet (Tidak overload/gepeng)",
                "Chiller/CS bebas bunga es (Defrosting rutin)",
                "Bahan baku sisa (powder/plastik) di-seal rapat (Dilarang pakai karet gelang)",
                "Pemisahan mutlak bahan kimia/cleaning dari area bahan baku"
            ]
        },
        { 
            section: "3. Equipment & Utensil", 
            items: [
                "Kondisi Basket Boiler (Tidak berkerak/besi mencuat/bolong)",
                "Ketersediaan Spatula (Pemisahan spatula level & non-level)",
                "Kondisi Steamer (Tutup lengkap, ada pegangan, tidak bocor uap)",
                "Ketersediaan termometer sampling (Frozen vs Produk matang)"
            ]
        },
        { 
            section: "4. Cooking & Preparation", 
            items: [
                "Standar Reheating Siomay (Waktu 1-2 menit, tidak overload)",
                "Personal Hygiene crew (Dilarang karet gelang di atas handgloves)",
                "Kebersihan minyak Fryer (Bebas remahan/kontaminasi silang antar produk)",
                "Ketersediaan sendok takar topping (Gramasi sesuai standar)"
            ]
        },
        { 
            section: "5. Procedure Serving & Brand Image", 
            items: [
                "Kebersihan Klakat & Piring (Bebas sisa makanan/saus kering)",
                "Standar porsi produk sesuai (Jumlah pcs per porsi)",
                "Update Buku Menu/POS (Produk discontinue/out of stock tidak dipajang)",
                "Kebersihan Seragam & Penampilan Crew"
            ]
        },
        { 
            section: "6. Pest Control", 
            items: [
                "Area Topping bebas hama Rongo (Cek sela-sela meja)",
                "Cek toples Susu Kental Manis (Bebas kontaminasi semut/rongo)",
                "Produk cabai di area level bebas kontaminasi hama",
                "Tidak ada lalat di area produksi/preparation"
            ]
        }
    ];

    const container = document.getElementById('checklistContainer');

    fullAuditData.forEach(sec => {
        const title = document.createElement('div');
        title.className = 'section-title';
        title.innerText = sec.section;
        container.appendChild(title);

        sec.items.forEach(item => {
            const card = document.createElement('div');
            card.className = 'card';
            card.innerHTML = `
                <div class="item-row">
                    <span class="item-label">${item}</span>
                    <select class="status-select" onchange="toggleFail(this)">
                        <option value="OK">✅ OK</option>
                        <option value="NOT OK">❌ NOT OK</option>
                    </select>
                </div>
                <div class="fail-box">
                    <textarea rows="2" placeholder="Detail Masalah..."></textarea>
                    <input type="text" placeholder="Action Plan (Tindakan Perbaikan)">
                    <div style="margin-top:10px;">
                        <input type="file" accept="image/*" style="font-size:11px;">
                    </div>
                </div>
            `;
            container.appendChild(card);
        });
    });

    function toggleFail(select) {
        const failBox = select.parentElement.nextElementSibling;
        failBox.style.display = select.value === 'NOT OK' ? 'block' : 'none';
    }

    function generatePDF() {
        const mod = document.getElementById('modName').value;
        const date = document.getElementById('auditDate').value;
        if(!mod || !date) { alert("Nama Manager dan Tanggal wajib diisi!"); return; }

        const element = document.getElementById('printableArea');
        const opt = {
            margin: 0.3,
            filename: `FullAudit_${mod}_${date}.pdf`,
            image: { type: 'jpeg', quality: 0.98 },
            html2canvas: { scale: 2 },
            jsPDF: { unit: 'in', format: 'a4', orientation: 'portrait' },
            pagebreak: { mode: 'avoid-all' }
        };

        // Trick agar isi dropdown dan input terbaca di PDF
        document.querySelectorAll('input[type="text"], textarea, select').forEach(el => {
            if(el.tagName === 'SELECT') {
                const span = document.createElement('span');
                span.innerText = el.value;
                span.style.fontWeight = 'bold';
                span.style.color = el.value === 'NOT OK' ? 'red' : 'green';
                el.after(span);
                el.style.display = 'none';
            } else {
                el.setAttribute('value', el.value);
            }
        });

        html2pdf().set(opt).from(element).save().then(() => {
            alert("Laporan berhasil diunduh!");
            setTimeout(() => { location.reload(); }, 1000);
        });
    }
</script>
</body>
</html>
