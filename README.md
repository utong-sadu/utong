<!DOCTYPE html>
<html lang="km">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>ទម្រង់ចុះឈ្មោះសិស្ស</title>
<style>
body { font-family: Arial; margin: 20px; background-color: #f9f9f9; }
h2, h3 { text-align: center; color: #333; }
form { max-width: 700px; margin: auto; background: #fff; padding: 20px; border-radius: 8px; box-shadow: 0 0 10px rgba(0,0,0,0.1); }
label { display: block; margin-top: 10px; font-weight: bold; }
input, select { width: 100%; padding: 8px; margin-top: 5px; border-radius: 4px; border: 1px solid #ccc; }
.row { display: flex; gap: 10px; }
.row div { flex: 1; }
button { margin-top: 10px; padding: 8px 12px; background-color: #4CAF50; color: white; border: none; border-radius: 4px; cursor: pointer; }
button:hover { background-color: #45a049; }
table { width: 100%; border-collapse: collapse; margin-top: 20px; }
th, td { border: 1px solid #ccc; padding: 8px; text-align: left; }
th { background-color: #eee; }
</style>
</head>
<body>

<h2>ទម្រង់ចុះឈ្មោះសិស្ស</h2>

<form id="studentForm">
  <label>លេខសំបុត្រកំណើត</label>
  <input type="text" id="idCard" required>

  <label>ឈ្មោះសិស្ស</label>
  <input type="text" id="studentName" required>

  <label>ភេទ</label>
  <select id="gender" required>
    <option value="">-- ជ្រើសរើស --</option>
    <option value="ប្រុស">ប្រុស</option>
    <option value="ស្រី">ស្រី</option>
  </select>

  <label>ថ្ងៃខែឆ្នាំកំណើត</label>
  <input type="date" id="dob" required>

  <label>អាយុ</label>
  <input type="text" id="age" readonly placeholder="អាយុត្រូវបង្ហាញស្វ័យប្រវត្តិ">

  <label>ទីកន្លែងកំណើត</label>
  <div class="row">
    <div>
      <label>ភូមិ</label>
      <input type="text" id="village" placeholder="បញ្ចូលភូមិ">
    </div>
    <div>
      <label>ឃុំ</label>
      <input type="text" id="commune" value="ស្ពានស្រែង">
    </div>
  </div>
  <div class="row">
    <div>
      <label>ស្រុក</label>
      <input type="text" id="district" value="ភ្នំស្រុក">
    </div>
    <div>
      <label>ខេត្ត</label>
      <input type="text" id="province" value="បន្ទាយមានជ័យ">
    </div>
  </div>

  <label>ព័ត៌មានគ្រួសារ</label>
  <div class="row">
    <div>
      <label>ឈ្មោះឪពុក</label>
      <input type="text" id="fatherName">
    </div>
    <div>
      <label>មុខរបរ</label>
      <input type="text" id="fatherJob">
    </div>
  </div>
  <div class="row">
    <div>
      <label>ឈ្មោះម្តាយ</label>
      <input type="text" id="motherName">
    </div>
    <div>
      <label>មុខរបរ</label>
      <input type="text" id="motherJob">
    </div>
  </div>

  <button type="button" onclick="addPreview()">Add to Preview</button>
</form>

<h3>Preview មុនផ្ញើ</h3>
<div id="previewTableContainer">
  <table id="previewTable">
    <thead>
      <tr>
        <th>លេខសំបុត្រកំណើត</th>
        <th>ឈ្មោះសិស្ស</th>
        <th>ភេទ</th>
        <th>ថ្ងៃខែឆ្នាំកំណើត</th>
        <th>អាយុ</th>
        <th>ទីកន្លែងកំណើត</th>
        <th>ឪពុក</th>
        <th>ម្តាយ</th>
        <th>Action</th>
      </tr>
    </thead>
    <tbody id="previewBody"></tbody>
  </table>
  <button onclick="submitAllPreview()">បញ្ជូនទាំងអស់ទៅ Google Sheet</button>
</div>

<h3>រាយនាមសិស្ស</h3>
<div id="studentList"></div>

<script>
let previewDataArray = [];

// Web App URL
const WEB_APP_URL = "https://script.google.com/macros/s/AKfycbzgHCMgRmr8cX4_2IDb3ox3neqqJNKHEoJoUqT_62BxzN6wMkUipe8ae8DB2-SULOt6/exec";

// Auto-calculate age
document.getElementById('dob').addEventListener('change', function() {
  const dob = new Date(this.value);
  const today = new Date();
  let age = today.getFullYear() - dob.getFullYear();
  const m = today.getMonth() - dob.getMonth();
  if (m < 0 || (m === 0 && today.getDate() < dob.getDate())) age--;
  document.getElementById('age').value = age;
});

// Add Preview Row
function addPreview() {
  const age = document.getElementById('age').value;
  const row = {
    idCard: document.getElementById('idCard').value,
    studentName: document.getElementById('studentName').value,
    gender: document.getElementById('gender').value,
    dob: document.getElementById('dob').value,
    age: age,
    village: document.getElementById('village').value,
    commune: document.getElementById('commune').value,
    district: document.getElementById('district').value,
    province: document.getElementById('province').value,
    fatherName: document.getElementById('fatherName').value,
    fatherJob: document.getElementById('fatherJob').value,
    motherName: document.getElementById('motherName').value,
    motherJob: document.getElementById('motherJob').value
  };
  previewDataArray.push(row);
  renderPreviewTable();
  document.getElementById('studentForm').reset();
  document.getElementById('age').value = '';
}

// Render Preview Table
function renderPreviewTable() {
  const tbody = document.getElementById('previewBody');
  tbody.innerHTML = '';
  previewDataArray.forEach((row, index) => {
    const tr = document.createElement('tr');
    tr.innerHTML = `
      <td>${row.idCard}</td>
      <td>${row.studentName}</td>
      <td>${row.gender}</td>
      <td>${row.dob}</td>
      <td>${row.age}</td>
      <td>ភូមិ: ${row.village}, ឃុំ: ${row.commune}, ស្រុក: ${row.district}, ខេត្ត: ${row.province}</td>
      <td>${row.fatherName} (${row.fatherJob})</td>
      <td>${row.motherName} (${row.motherJob})</td>
      <td>
        <button onclick="editPreview(${index})">Edit</button>
        <button onclick="deletePreview(${index})">Delete</button>
      </td>
    `;
    tbody.appendChild(tr);
  });
}

// Edit Preview Row
function editPreview(index) {
  const row = previewDataArray[index];
  document.getElementById('idCard').value = row.idCard;
  document.getElementById('studentName').value = row.studentName;
  document.getElementById('gender').value = row.gender;
  document.getElementById('dob').value = row.dob;
  document.getElementById('age').value = row.age;
  document.getElementById('village').value = row.village;
  document.getElementById('commune').value = row.commune;
  document.getElementById('district').value = row.district;
  document.getElementById('province').value = row.province;
  document.getElementById('fatherName').value = row.fatherName;
  document.getElementById('fatherJob').value = row.fatherJob;
  document.getElementById('motherName').value = row.motherName;
  document.getElementById('motherJob').value = row.motherJob;
  previewDataArray.splice(index, 1);
  renderPreviewTable();
}

// Delete Preview Row
function deletePreview(index) {
  if(confirm("តើអ្នកចង់លុបសិស្សនេះចេញពី Preview?")) {
    previewDataArray.splice(index, 1);
    renderPreviewTable();
  }
}

// Submit All Preview Data to Google Sheet
function submitAllPreview() {
  if(previewDataArray.length === 0) {
    alert("គ្មានទិន្នន័យក្នុង Preview ដើម្បីផ្ញើ!");
    return;
  }

  fetch(WEB_APP_URL, {
    method: "POST",
    body: JSON.stringify({students: previewDataArray}),
    headers: {"Content-Type": "application/json"}
  })
  .then(res => res.json())
  .then(res => {
    alert("ទិន្នន័យបានផ្ញើទៅ Google Sheet ជោគជ័យ!");
    previewDataArray = [];
    renderPreviewTable();
    loadStudentList();
  })
  .catch(err => alert("មានបញ្ហាក្នុងការផ្ញើទិន្នន័យ: "+err));
}

// Load Student List from Google Sheet
function loadStudentList() {
  fetch(WEB_APP_URL+"?action=get")
    .then(res => res.json())
    .then(data => {
      if(!data || !data.length) {
        document.getElementById('studentList').innerHTML = "គ្មានទិន្នន័យសិស្សនៅឡើយទេ។";
        return;
      }
      let html = `<table>
        <tr>
          <th>លេខសំបុត្រកំណើត</th><th>ឈ្មោះសិស្ស</th><th>ភេទ</th><th>ថ្ងៃខែឆ្នាំកំណើត</th><th>អាយុ</th><th>ទីកន្លែងកំណើត</th><th>ឪពុក</th><th>ម្តាយ</th>
        </tr>`;
      data.forEach(s => {
        html += `<tr>
          <td>${s.idCard}</td>
          <td>${s.studentName}</td>
          <td>${s.gender}</td>
          <td>${s.dob}</td>
          <td>${s.age}</td>
          <td>ភូមិ: ${s.village}, ឃុំ: ${s.commune}, ស្រុក: ${s.district}, ខេត្ត: ${s.province}</td>
          <td>${s.fatherName} (${s.fatherJob})</td>
          <td>${s.motherName} (${s.motherJob})</td>
        </tr>`;
      });
      html += `</table>`;
      document.getElementById('studentList').innerHTML = html;
    })
    .catch(err => document.getElementById('studentList').innerHTML = "មានបញ្ហាក្នុងការទាញទិន្នន័យ: " + err);
}

// Load Student List on Page Load
window.onload = function() {
  loadStudentList();
};
</script>

</body>
</html>