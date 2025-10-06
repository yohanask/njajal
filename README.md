<!DOCTYPE html>
<html lang="id">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Data Kegiatan</title>
<style>
  body {
    font-family: "Segoe UI", Arial, sans-serif;
    background: #f5f7fa;
    color: #333;
    padding: 20px;
  }
  h2 {
    text-align: center;
    color: #003366;
    margin-bottom: 25px;
  }
  table {
    width: 100%;
    border-collapse: collapse;
    box-shadow: 0 2px 8px rgba(0,0,0,0.1);
    background: white;
    border-radius: 10px;
    overflow: hidden;
  }
  th, td {
    padding: 12px 15px;
    text-align: left;
  }
  th {
    background-color: #003366;
    color: white;
    text-transform: uppercase;
    letter-spacing: 0.5px;
  }
  tr:nth-child(even) {
    background-color: #f0f4f8;
  }
  tr:hover {
    background-color: #e6f0ff;
  }
  td {
    border-bottom: 1px solid #ddd;
  }
  @media (max-width: 600px) {
    table, thead, tbody, th, td, tr {
      display: block;
    }
    th {
      display: none;
    }
    td {
      display: flex;
      justify-content: space-between;
      padding: 10px;
      border: none;
      border-bottom: 1px solid #ccc;
    }
    td::before {
      content: attr(data-label);
      font-weight: bold;
      color: #003366;
    }
  }
</style>
</head>
<body>

<h2>Rekapitulasi Kegiatan</h2>
<div id="table-container">Memuat data...</div>

<script>
const csvUrl = "https://docs.google.com/spreadsheets/d/e/2PACX-1vRgbAHA0s5QOTBGjTKLzkqTrIYWopt-OrBqFdKh2fPmsWhSx4sELRiG8VaCTamtEXJVeucX2DsaVkMO/pub?output=csv";

fetch(csvUrl)
  .then(res => res.text())
  .then(csv => {
    const rows = csv.trim().split("\n").map(r => r.split(","));
    const headers = rows.shift();
    const data = rows.map(r => Object.fromEntries(headers.map((h, i) => [h.trim(), r[i]?.trim()])));

    const table = document.createElement("table");
    const thead = document.createElement("thead");
    const headerRow = document.createElement("tr");
    headers.forEach(h => {
      const th = document.createElement("th");
      th.textContent = h;
      headerRow.appendChild(th);
    });
    thead.appendChild(headerRow);
    table.appendChild(thead);

    const tbody = document.createElement("tbody");
    data.forEach(row => {
      const tr = document.createElement("tr");
      headers.forEach(h => {
        const td = document.createElement("td");
        td.textContent = row[h] || "";
        td.setAttribute("data-label", h);
        tr.appendChild(td);
      });
      tbody.appendChild(tr);
    });
    table.appendChild(tbody);

    document.getElementById("table-container").innerHTML = "";
    document.getElementById("table-container").appendChild(table);
  })
  .catch(err => {
    document.getElementById("table-container").textContent = "Gagal memuat data.";
    console.error(err);
  });
</script>

</body>
</html>
