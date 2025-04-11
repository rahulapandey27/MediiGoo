<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>MediGo - Your Medicine Info Hub</title>
  <script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-app-compat.js"></script>
  <script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-auth-compat.js"></script>
  <style>
    :root {
      --bg-color: #ffffff;
      --text-color: #000000;
      --accent-color: #0078d7;
    }

    body.dark-mode {
      --bg-color: #1e1e1e;
      --text-color: #ffffff;
    }

    body {
      margin: 0;
      font-family: Arial, sans-serif;
      background-color: var(--bg-color);
      color: var(--text-color);
    }

    header {
      display: flex;
      justify-content: space-between;
      align-items: center;
      padding: 10px 20px;
      background-color: var(--accent-color);
      color: #fff;
    }

    header nav a {
      margin: 0 10px;
      color: white;
      text-decoration: none;
      font-weight: bold;
      cursor: pointer;
    }

    .search-section {
      text-align: center;
      margin-top: 40px;
    }

    .search-section input {
      padding: 10px;
      font-size: 18px;
      width: 60%;
      max-width: 600px;
      border: 1px solid #ccc;
      border-radius: 5px;
    }

    .content {
      padding: 40px 20px;
      text-align: center;
    }

    .company-banner {
      background: url("medigo.jpg") no-repeat center center;
      background-size: cover;
      height: 300px;
      display: flex;
      align-items: center;
      justify-content: center;
      color: white;
      font-size: 40px;
      font-weight: bold;
      text-shadow: 1px 1px 5px rgba(0, 0, 0, 0.7);
    }

    .examples, #search-results {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
      gap: 20px;
      margin-top: 20px;
    }

    .medicine-card {
      background-color: #f5f5f5;
      padding: 20px;
      border-radius: 10px;
      text-align: left;
    }

    .settings-dropdown {
      position: relative;
      display: inline-block;
    }

    .settings-dropdown-content {
      display: none;
      position: absolute;
      right: 0;
      background-color: white;
      min-width: 160px;
      box-shadow: 0px 8px 16px 0px rgba(0, 0, 0, 0.2);
      z-index: 1;
    }

    .settings-dropdown-content a {
      color: black;
      padding: 12px 16px;
      text-decoration: none;
      display: block;
    }

    .settings-dropdown:hover .settings-dropdown-content {
      display: block;
    }

    #about {
      padding: 60px 20px;
      background-color: #eef4fb;
      text-align: center;
    }

    #about h2 {
      font-size: 28px;
    }

    #about p {
      font-size: 18px;
      max-width: 800px;
      margin: 0 auto;
    }
  </style>
</head>

<body>
  <header>
    <div><strong>MediGo</strong></div>
    <nav>
      <a href="#" onclick="scrollToTop()">Home</a>
      <a href="#about">About</a>
      <div class="settings-dropdown">
        <a href="#">Settings</a>
        <div class="settings-dropdown-content">
          <a href="#" onclick="toggleDarkMode()">Toggle Dark/Light Mode</a>
          <a href="#" onclick="loginUser()">Login</a>
          <a href="#" onclick="logoutUser()">Logout</a>
        </div>
      </div>
    </nav>
  </header>

  <section class="search-section">
    <input type="text" id="searchBar" placeholder="Search for a medicine..." oninput="searchMedicine()" />
  </section>

  <section class="company-banner">
    MediGo - Trusted Medical Info
  </section>

  <section id="about">
    <h2>About MediGo</h2>
    <p>
      MediGo is a simple and helpful platform where you can get clear, trustworthy information about your medicines. Whether you're wondering what your medicine does, how it affects your body, or what the long-term side effects might be — MediGo makes it easy to understand. Perfect for everyday users, especially the older generation, MediGo provides easy-to-read details on how medicines work, why they are prescribed, and what you should be cautious about.
    </p>
  </section>

  <section class="content">
    <h2>General Information</h2>
    <p>
      MediGo helps you understand what your medicine does, what drugs it contains, its short-term and long-term effects, and any potential harmful side effects.
    </p>

    <h2>Common Medicines</h2>
    <div id="search-results"></div>
  </section>

  <script>
    const firebaseConfig = {
      apiKey: "YOUR_API_KEY",
      authDomain: "YOUR_AUTH_DOMAIN",
      projectId: "YOUR_PROJECT_ID",
      storageBucket: "YOUR_STORAGE_BUCKET",
      messagingSenderId: "YOUR_SENDER_ID",
      appId: "YOUR_APP_ID"
    };

    firebase.initializeApp(firebaseConfig);
    const auth = firebase.auth();

    function loginUser() {
      const email = prompt("Enter your email:");
      const password = prompt("Enter your password:");
      auth.signInWithEmailAndPassword(email, password)
        .then(userCredential => {
          alert(`Welcome back, ${userCredential.user.email}!\n\nFrom: no-reply@medigo.com\nSubject: MediGo Login Confirmation\n\nThank you for logging into MediGo. Stay informed, stay safe.`);
        })
        .catch(error => alert("Login failed: " + error.message));
    }

    function logoutUser() {
      auth.signOut().then(() => {
        alert("You have been logged out successfully.");
      });
    }

    const medicines = [
      {
        name: "Terbinafine",
        use: "Fungal infections of skin/nails",
        when: "White patches, peeling skin, itching",
        why: "Kills fungal cells by damaging their membrane",
        warn: "Avoid in liver disease, pregnancy unless prescribed",
        usage: "250mg daily / Apply cream twice daily",
        tip: "Avoid alcohol, complete full course"
      },
      {
        name: "Minoxidil",
        use: "Hairfall, pattern baldness",
        when: "Thinning hair, bald spots",
        why: "Increases blood flow to hair follicles",
        warn: "Scalp use only, patch test first",
        usage: "Apply 1ml on scalp twice a day",
        tip: "Consistent use 3+ months"
      },
      {
        name: "Sumatriptan",
        use: "Migraine attacks",
        when: "Pulsating headache, nausea, light sensitivity",
        why: "Narrows blood vessels, reduces pain signals",
        warn: "Avoid in heart problems, high BP",
        usage: "50-100mg at onset, max 200mg/day",
        tip: "Avoid > 2 doses in 24 hrs"
      },
      {
        name: "Isosorbide Mononitrate",
        use: "Chest pain (angina)",
        when: "Tightness/heaviness in chest during exertion",
        why: "Relaxes heart vessels",
        warn: "Avoid with Viagra, low BP meds",
        usage: "10-20mg twice daily",
        tip: "Don't skip/stop suddenly"
      }
    ];

    function searchMedicine() {
      const input = document.getElementById("searchBar").value.toLowerCase();
      const results = medicines.filter(m => m.name.toLowerCase().includes(input));
      const container = document.getElementById("search-results");
      container.innerHTML = results.map(m => `
        <div class="medicine-card">
          <h3>${m.name}</h3>
          <p><strong>Used for:</strong> ${m.use}</p>
          <p><strong>Take it when:</strong> ${m.when}</p>
          <p><strong>Why:</strong> ${m.why}</p>
          <p><strong>Determinants:</strong> ${m.warn}</p>
          <p><strong>Usage:</strong> ${m.usage}</p>
          <p><strong>Moderation:</strong> ${m.tip}</p>
        </div>
      `).join('');
    }

    function toggleDarkMode() {
      document.body.classList.toggle("dark-mode");
    }

    function scrollToTop() {
      window.scrollTo({ top: 0, behavior: 'smooth' });
    }
  </script>
</body>

</html>
