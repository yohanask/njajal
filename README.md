// Ambil data dari Google Spreadsheet (format CSV)
fetch("https://docs.google.com/spreadsheets/d/e/2PACX-1vRgbAHA0s5QOTBGjTKLzkqTrIYWopt-OrBqFdKh2fPmsWhSx4sELRiG8VaCTamtEXJVeucX2DsaVkMO/pub?output=csv")
  .then(res => res.text())
  .then(csv => {
    // Ubah CSV jadi array objek
    const rows = csv.trim().split("\n").map(r => r.split(","));
    const headers = rows.shift();
    const data = rows.map(r => Object.fromEntries(headers.map((h, i) => [h, r[i]])));

    // Tampilkan ke tabel di Canva
    const table = document.createElement("table");
    table.border = "1";
    table.style.borderCollapse = "collapse";
    table.style.width = "100%";

    // Header
    const headerRow = document.createElement("tr");
    headers.forEach(h => {
      const th = document.createElement("th");
      th.textContent = h;
      headerRow.appendChild(th);
    });
    table.appendChild(headerRow);

    // Isi data
    data.forEach(row => {
      const tr = document.createElement("tr");
      headers.forEach(h => {
        const td = document.createElement("td");
        td.textContent = row[h] || "";
        tr.appendChild(td);
      });
      table.appendChild(tr);
    });

    document.body.appendChild(table);
  })
  .catch(err => console.error("Gagal ambil data:", err));
