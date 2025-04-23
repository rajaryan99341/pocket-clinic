<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Pocket Clinic</title>
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta3/css/all.min.css"/>
  <style>
    * {
      margin: 0;
      padding: 0;
      box-sizing: border-box;
      font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    }

    body {
      background: linear-gradient(to right, #0f2027, #203a43, #2c5364);
      color: #fff;
      padding: 20px;
    }

    header {
      background-color: #1b1b1b;
      padding: 20px;
      text-align: center;
      color: #8BC34A;
      font-size: 2rem;
      font-weight: bold;
      border-radius: 10px;
      margin-bottom: 20px;
    }

    .container {
      max-width: 900px;
      margin: auto;
      background: #222;
      padding: 30px;
      border-radius: 15px;
      box-shadow: 0 0 15px rgba(0,255,0,0.2);
    }

    form {
      display: flex;
      gap: 10px;
      margin-bottom: 20px;
    }

    input[type="text"] {
      flex: 1;
      padding: 12px;
      border: none;
      border-radius: 8px;
      font-size: 1rem;
    }

    button {
      background-color: #8BC34A;
      color: #000;
      padding: 12px 20px;
      border: none;
      border-radius: 8px;
      font-weight: bold;
      cursor: pointer;
      transition: background 0.3s;
    }

    button:hover {
      background-color: #7CB342;
    }

    .card {
      background-color: #333;
      padding: 20px;
      border-radius: 10px;
      margin-bottom: 20px;
      box-shadow: 0 2px 10px rgba(0,255,0,0.1);
    }

    .card h3 {
      color: #8BC34A;
      margin-bottom: 10px;
    }

    .tag {
      display: inline-block;
      background: #4CAF50;
      padding: 5px 10px;
      border-radius: 20px;
      margin: 5px 5px 0 0;
      font-size: 0.9rem;
    }

    ul {
      padding-left: 20px;
    }

    ul li {
      margin-bottom: 5px;
    }
  </style>
</head>
<body>
  <header>
    <i class="fas fa-clinic-medical"></i> Pocket Clinic
  </header>
  <div class="container">
    <form id="symptom-form">
      <input type="text" id="symptoms-input" placeholder="Enter your symptoms (e.g. fever, cough)" required />
      <button type="submit">Submit</button>
    </form>

    <div class="card" id="result-section" style="display:none">
      <h3><i class="fas fa-diagnoses"></i> Diagnosis</h3>
      <p id="diagnosis-text"></p>
    </div>

    <div class="card" id="first-aid-section" style="display:none">
      <h3><i class="fas fa-first-aid"></i> First Aid</h3>
      <ul id="first-aid-list"></ul>
    </div>

    <div class="card" id="medication-section" style="display:none">
      <h3><i class="fas fa-pills"></i> Medications</h3>
      <ul id="medication-list"></ul>
    </div>

    <div class="card" id="warning-section" style="display:none">
      <h3><i class="fas fa-exclamation-triangle"></i> Warning Signs</h3>
      <ul id="warning-list"></ul>
    </div>
  </div>

  <script>
    const form = document.getElementById('symptom-form');
    const input = document.getElementById('symptoms-input');
    const result = document.getElementById('result-section');
    const firstAid = document.getElementById('first-aid-section');
    const medication = document.getElementById('medication-section');
    const warning = document.getElementById('warning-section');

    form.addEventListener('submit', async (e) => {
      e.preventDefault();
      const symptoms = input.value.trim();

      const res = await fetch('http://localhost:5000/api/analyze', {
        method: 'POST',
        headers: {'Content-Type': 'application/json'},
        body: JSON.stringify({ symptoms: symptoms.split(',').map(s => s.trim()) })
      });
      const data = await res.json();

      if (data.success) {
        document.getElementById('diagnosis-text').innerText = data.diagnosis.illness;

        const firstAidList = document.getElementById('first-aid-list');
        firstAidList.innerHTML = '';
        data.diagnosis.firstAid.forEach(item => {
          const li = document.createElement('li');
          li.textContent = item;
          firstAidList.appendChild(li);
        });

        const medicationList = document.getElementById('medication-list');
        medicationList.innerHTML = '';
        data.diagnosis.medications.forEach(med => {
          const li = document.createElement('li');
          li.textContent = `${med.name} (${med.dosage}) - ${med.purpose}`;
          medicationList.appendChild(li);
        });

        const warningList = document.getElementById('warning-list');
        warningList.innerHTML = '';
        data.diagnosis.warningSigns.forEach(sign => {
          const li = document.createElement('li');
          li.textContent = sign;
          warningList.appendChild(li);
        });

        result.style.display = 'block';
        firstAid.style.display = 'block';
        medication.style.display = 'block';
        warning.style.display = 'block';
      }
    });
  </script>
</body>
</html>
