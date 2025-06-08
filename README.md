<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
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
      box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
      transition: transform 0.3s ease;
    }
    img:hover {
      transform: scale(1.02);
    }
    textarea {
      width: 100%;
      height: 100px;
      border-radius: 10px;
      border: none;
      padding: 1rem;
      margin-bottom: 1rem;
      font-family: inherit;
    }
    input[type="file"] {
      margin-bottom: 1rem;
    }
    audio {
      display: block;
      margin-top: 1rem;
    }
    .upload-box {
      background-color: #f4e6ff;
      border-radius: 12px;
      padding: 1.5rem;
      margin-top: 2rem;
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
    <img src="myhealing.jpg" alt/>

    <div style="margin-top: 1rem;">
      <h4>Additional Healing Moments</h4>
      <div style="display: flex; flex-direction: column; gap: 1rem;">
        <img src="redp.jpg" alt="Healing Portrait 1" />
        <img src="e3b6bc11-941d-4ecb-ada2-e11c38d0f362.jpg" alt="Healing Portrait 2" />
      </div>
    </div>
  </section>

  <section id="share">
    <h2>Share Your Healing</h2>
    <p>Add a note, upload a photo, or share a song that helps you feel better.</p>

    <div class="upload-box">
      <label for="note"><strong>Write a Note</strong></label><br>
      <textarea id="note" placeholder="Today I felt..."></textarea>

      <label for="imageUpload"><strong>Upload a Photo</strong></label><br>
      <input type="file" id="imageUpload" accept="image/*" /><br>
      <img id="imagePreview" style="display:none;" />

      <label for="musicUpload"><strong>Upload Music</strong></label><br>
      <input type="file" id="musicUpload" accept="audio/*" />
      <audio controls id="musicPlayer" style="display:none;"></audio>
    </div>
  </section>

  <section id="contact">
    <h2>Contact</h2>
    <p>Feel free to reach out to me: <strong>09677 965 325</strong></p>
  </section>

  <script>
    // Image preview
    document.getElementById('imageUpload').addEventListener('change', function(e) {
      const file = e.target.files[0];
      if (file) {
        const reader = new FileReader();
        reader.onload = function(evt) {
          const img = document.getElementById('imagePreview');
          img.src = evt.target.result;
          img.style.display = 'block';
          img.style.maxWidth = '100%';
          img.style.marginTop = '1rem';
        };
        reader.readAsDataURL(file);
      }
    });

    // Music preview
    document.getElementById('musicUpload').addEventListener('change', function(e) {
      const file = e.target.files[0];
      if (file) {
        const audio = document.getElementById('musicPlayer');
        audio.src = URL.createObjectURL(file);
        audio.style.display = 'block';
      }
    });
  </script>

</body>
</html>
