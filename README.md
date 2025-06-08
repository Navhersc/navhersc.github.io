<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>My Healing Journey</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      background-color: #e6ccff;
      margin: 0;
    }
    header {
      text-align: center;
      padding: 2rem;
    }
    nav {
      display: flex;
      justify-content: center;
      background-color: #d9b3ff;
      padding: 1rem;
    }
    nav a {
      margin: 0 15px;
      color: #333;
      text-decoration: none;
      font-weight: bold;
    }
    section {
      max-width: 800px;
      margin: auto;
      padding: 2rem;
    }
    img {
      max-width: 100%;
      border-radius: 10px;
    }
    textarea {
      width: 100%;
      height: 100px;
      border-radius: 10px;
      padding: 1rem;
      margin-bottom: 1rem;
      font-family: inherit;
      resize: vertical;
    }
    input[type="file"] {
      margin-bottom: 1rem;
    }
    .upload-box {
      background-color: #f4e6ff;
      border-radius: 12px;
      padding: 1.5rem;
      margin-top: 2rem;
    }
    button {
      background-color: #cc99ff;
      color: #fff;
      padding: 0.5rem 1rem;
      border: none;
      border-radius: 8px;
      cursor: pointer;
      margin-top: 1rem;
    }
    .entry {
      background-color: #fff0ff;
      padding: 1rem;
      border-radius: 10px;
      margin-bottom: 1rem;
      position: relative;
    }
    .entry button {
      background-color: #ff6699;
      padding: 0.3rem 0.8rem;
      font-size: 0.8rem;
      position: absolute;
      top: 10px;
      right: 10px;
    }
  </style>
</head>
<body>

  <header>
    <h1>🐱 My Healing Journey</h1>
    <p><em>"Like a cat, I land on my feet. Healing one purr at a time."</em></p>
    <img src="https://placekitten.com/300/200" alt="Cute Cat" />
  </header>

  <nav>
    <a href="#home">Home</a>
    <a href="#about">About</a>
    <a href="#share">Share</a>
    <a href="#contact">Contact</a>
  </nav>

  <section id="home">
    <h2>Welcome</h2>
    <p>This is my space to heal, reflect, and share — with a cat by my side.</p>
  </section>

  <section id="about">
    <h2>About My Healing</h2>
    <p>
      After a tough breakup, I chose to focus on myself. I surrounded myself with quiet moments, journaling, and the gentle company of my cat. Every day I found a little more peace, and I want to share my journey with you here.
    </p>

    <h3>My Healing Photo</h3>
    <img src="myhealing.jpg" alt="My Healing Photo" />
    <div style="margin-top: 1rem;">
      <h4>Additional Healing Moments</h4>
      <img src="redp.jpg" alt="Healing Portrait 1" />
      <img src="e3b6bc11-941d-4ecb-ada2-e11c38d0f362.jpg" alt="Healing Portrait 2" />
    </div>
  </section>

  <section id="share">
    <h2>Share Your Healing</h2>
    <p>Add a note, upload a photo, and save or delete them freely.</p>

    <div class="upload-box">
      <label for="note"><strong>Write a Note</strong></label><br>
      <textarea id="note" placeholder="Today I felt..."></textarea>

      <label for="imageUpload"><strong>Upload a Photo</strong></label><br>
      <input type="file" id="imageUpload" accept="image/*" /><br>
      <img id="imagePreview" style="display:none; max-width: 100%; margin-top: 1rem;" />

      <br><button onclick="saveEntry()">Save Entry</button>
    </div>

    <div id="savedEntries">
      <h3>Saved Healing Entries</h3>
      <!-- Entries will appear here -->
    </div>
  </section>

  <section id="contact">
    <h2>Contact</h2>
    <p>Feel free to reach out to me: <strong>09677 965 325</strong></p>
  </section>

  <script>
    let savedImageData = "";

    // Handle image preview and store as base64
    document.getElementById('imageUpload').addEventListener('change', function (e) {
      const file = e.target.files[0];
      if (file) {
        const reader = new FileReader();
        reader.onload = function (evt) {
          savedImageData = evt.target.result;
          const img = document.getElementById('imagePreview');
          img.src = savedImageData;
          img.style.display = 'block';
        };
        reader.readAsDataURL(file);
      }
    });

    // Save entry to localStorage
    function saveEntry() {
      const note = document.getElementById('note').value.trim();
      if (!note && !savedImageData) {
        alert("Please enter a note or upload a picture.");
        return;
      }

      let entries = JSON.parse(localStorage.getItem("healingEntries")) || [];
      entries.push({ note: note, image: savedImageData });
      localStorage.setItem("healingEntries", JSON.stringify(entries));

      // Reset input
      document.getElementById('note').value = "";
      document.getElementById('imagePreview').style.display = 'none';
      document.getElementById('imageUpload').value = "";
      savedImageData = "";

      loadEntries();
    }

    // Delete entry by index
    function deleteEntry(index) {
      let entries = JSON.parse(localStorage.getItem("healingEntries")) || [];
      entries.splice(index, 1);
      localStorage.setItem("healingEntries", JSON.stringify(entries));
      loadEntries();
    }

    // Load and show saved entries
    function loadEntries() {
      const entries = JSON.parse(localStorage.getItem("healingEntries")) || [];
      const container = document.getElementById('savedEntries');
      container.innerHTML = "<h3>Saved Healing Entries</h3>";

      entries.forEach((entry, index) => {
        const div = document.createElement('div');
        div.className = "entry";
        div.innerHTML = `
          <button onclick="deleteEntry(${index})">Delete</button>
          <p>${entry.note ? entry.note : ''}</p>
          ${entry.image ? `<img src="${entry.image}" alt="Saved Healing Image" />` : ''}
        `;
        container.appendChild(div);
      });
    }

    // On page load
    window.onload = loadEntries;
  </script>

</body>
</html>

