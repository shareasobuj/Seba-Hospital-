Seba- Hospital 
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Seba Hospital </title>
  <style>
    body { font-family: Arial, sans-serif; margin: 0; padding: 0; background: orchid; }
    header, footer { background: #0069d9; color: white; padding: 15px; text-align: center; }
    nav a { margin: 8px; color: white; text-decoration: none; font-weight: bold; }
    .container { padding: 20px; max-width: 960px; margin: auto; background: #fff; margin-top: 20px; border-radius: 8px; box-shadow: 0 0 10px rgba(0,0,0,0.1); }
    input, button, textarea, select { padding: 10px; margin: 5px 0; width: 100%; }
    table { width: 100%; border-collapse: collapse; margin-top: 20px; }
    th, td { border: 1px solid #ccc; padding: 10px; text-align: left; }
    .hidden { display: none; }
    h2 { color: #0069d9; }
    #messageBox {
      position: fixed;
      bottom: 20px;
      right: 20px;
      background: #28a745;
      color: white;
      padding: 10px 20px;
      border-radius: 5px;
      display: none;
      z-index: 1000;
    }
  </style>
</head>
<body>
  <header>
    <h1>Seba Hospital</h1>
    <nav>
      <a href="#home">Home</a>
      <a href="#about">About</a>
      <a href="#services">Services</a>
      <a href="#doctors">Doctors</a>
      <a href="#appointments">Appointments</a>
      <a href="#signup">Sign Up</a>
      <a href="#login">Login</a>
      <a href="#dashboard">Dashboard</a>
      <a href="#contact">Contact</a>
    </nav>
  </header>

  <div class="container" id="home">
    <h2>Welcome to Smart Clinic</h2>
    <p>Digital platform for managing your clinic effortlessly.</p>
  </div>

  <div class="container hidden" id="about">
    <h2>About Us</h2>
    <p>Smart Clinic is a next-gen solution for doctors and patients to interact easily and securely.</p>
  </div>

  <div class="container hidden" id="services">
    <h2>Our Services</h2>
    <ul>
      <li>Online Appointments</li>
      <li>Patient History Management</li>
      <li>PDF/CSV Report Generation</li>
    </ul>
  </div>

  <div class="container hidden" id="doctors">
    <h2>Our Doctors</h2>
    <p>We have a team of experienced medical professionals available for appointments.</p>
  </div>

  <div class="container hidden" id="appointments">
    <h2>Book Appointment</h2>
    <input type="text" placeholder="Your Name">
    <input type="email" placeholder="Your Email">
    <select>
      <option>General Physician</option>
      <option>Dentist</option>
      <option>Pediatrician</option>
    </select>
    <input type="date">
    <button>Book Now</button>
  </div>

  <div class="container hidden" id="signup">
    <h2>Sign Up</h2>
    <input type="text" id="signupName" placeholder="Full Name">
    <input type="email" id="signupEmail" placeholder="Email">
    <input type="text" id="signupPhone" placeholder="Phone Number">
    <input type="password" id="signupPassword" placeholder="Password">
    <button onclick="registerUser()">Register</button>
  </div>

  <div class="container hidden" id="login">
    <h2>Login</h2>
    <input type="email" id="loginEmail" placeholder="Email">
    <input type="password" id="loginPassword" placeholder="Password">
    <button onclick="loginUser()">Login</button>
  </div>

  <div class="container hidden" id="dashboard">
    <h2>Patient Dashboard</h2>
    <input type="text" id="patientName" placeholder="Patient Name">
    <input type="text" id="patientAge" placeholder="Age">
    <textarea id="patientNote" placeholder="Medical Note"></textarea>
    <button onclick="addPatient()">Add Patient</button>
    <button onclick="exportCSV()">Download CSV</button>
    <button onclick="exportPDF()">Download PDF</button>
    <button onclick="window.print()">Print</button>
    <table id="patientTable">
      <thead>
        <tr><th>Name</th><th>Age</th><th>Note</th><th>Delete</th></tr>
      </thead>
      <tbody></tbody>
    </table>
  </div>

  <div class="container hidden" id="contact">
    <h2>Contact Us</h2>
    <p>Email: contact@smartclinic.com</p>
    <p>Phone: +880-123456789</p>
  </div>

  <footer>
    <p>&copy; 2025 Smart Clinic. All rights reserved.</p>
  </footer>

  <div id="messageBox"></div>

  <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
  <script>
    let users = JSON.parse(localStorage.getItem('users') || '[]');
    let currentUser = null;
    let patients = [];

    function navigate(hash) {
      document.querySelectorAll('.container').forEach(div => div.classList.add('hidden'));
      const target = document.querySelector(hash);
      if (target) target.classList.remove('hidden');
    }
    window.onhashchange = () => navigate(location.hash);
    navigate(location.hash || '#home');

    function showMessage(text, success = true) {
      const box = document.getElementById("messageBox");
      box.style.background = success ? "#28a745" : "#dc3545";
      box.textContent = text;
      box.style.display = "block";
      setTimeout(() => { box.style.display = "none"; }, 3000);
    }

    function registerUser() {
      const name = document.getElementById('signupName').value;
      const email = document.getElementById('signupEmail').value;
      const phone = document.getElementById('signupPhone').value;
      const password = document.getElementById('signupPassword').value;
      if (!name || !email || !phone || !password) return showMessage("Fill all fields", false);
      const exists = users.find(u => u.email === email);
      if (exists) return showMessage("User already exists!", false);
      users.push({ name, email, phone, password });
      localStorage.setItem('users', JSON.stringify(users));
      showMessage("Registered successfully!", true);
      location.hash = '#login';
    }

    function loginUser() {
      const email = document.getElementById('loginEmail').value;
      const password = document.getElementById('loginPassword').value;
      users = JSON.parse(localStorage.getItem('users') || '[]');
      const found = users.find(u => u.email === email && u.password === password);
      if (found) {
        currentUser = found;
        showMessage("Login successful!", true);
        location.hash = '#dashboard';
      } else {
        showMessage("Invalid credentials!", false);
      }
    }

    function addPatient() {
      const name = document.getElementById('patientName').value;
      const age = document.getElementById('patientAge').value;
      const note = document.getElementById('patientNote').value;
      if (!name || !age) return showMessage("Please fill all fields", false);
      const row = `<tr><td>${name}</td><td>${age}</td><td>${note}</td><td><button onclick=\"this.parentElement.parentElement.remove()\">X</button></td></tr>`;
      document.getElementById('patientTable').querySelector('tbody').insertAdjacentHTML('beforeend', row);
      patients.push({ name, age, note });
      showMessage("Patient added successfully!", true);
    }

    function exportCSV() {
      let csv = 'Name,Age,Note\n';
      patients.forEach(p => {
        csv += `${p.name},${p.age},${p.note}\n`;
      });
      const blob = new Blob([csv], { type: 'text/csv' });
      const link = document.createElement('a');
      link.href = URL.createObjectURL(blob);
      link.download = 'patients.csv';
      link.click();
    }

    async function exportPDF() {
      const { jsPDF } = window.jspdf;
      const doc = new jsPDF();
      doc.text("Patient List", 10, 10);
      let y = 20;
      patients.forEach(p => {
        doc.text(`${p.name} | ${p.age} | ${p.note}`, 10, y);
        y += 10;
      });
      doc.save('patients.pdf');
    }
  </script>
</body>
</html>
