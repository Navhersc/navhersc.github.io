<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
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
      cursor: pointer;
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
      <label for="note"><strong>Write a Note</strong></label><br />
      <textarea id="note" placeholder="Today I felt..."></textarea>

      <label for="imageUpload"><strong>Upload a Photo</strong></label><br />
      <input type="file" id="imageUpload" accept="image/*" /><br />
      <img id="imagePreview" style="display:none; max-width: 100%; margin-top: 1rem;" />

      <br /><button onclick="saveEntry()">Save Entry</button>
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

  <script type="module">
    import { initializeApp } from "https://www.gstatic.com/firebasejs/9.22.2/firebase-app.js";
    import {
      getFirestore,
      collection,
      addDoc,
      getDocs,
      deleteDoc,
      doc,
      query,
      orderBy
    } from "https://www.gstatic.com/firebasejs/9.22.2/firebase-firestore.js";
    import {
      getStorage,
      ref,
      uploadBytes,
      getDownloadURL,
      deleteObject
    } from "https://www.gstatic.com/firebasejs/9.22.2/firebase-storage.js";

    const firebaseConfig = {
      apiKey: "AIzaSyD7mJP8U3IRLSv-DgVQJmNolG7ouRA-dv8",
      authDomain: "healing-journey-d7204.firebaseapp.com",
      projectId: "healing-journey-d7204",
      storageBucket: "healing-journey-d7204.appspot.com",
      messagingSenderId: "477815035331",
      appId: "1:477815035331:web:1ec12501a78f818d8a1976"
    };

    // Initialize Firebase
    const app = initializeApp(firebaseConfig);
    const db = getFirestore(app);
    const storage = getStorage(app);

    const noteInput = document.getElementById("note");
    const imageInput = document.getElementById("imageUpload");
    const imagePreview = document.getElementById("imagePreview");
    const entriesContainer = document.getElementById("savedEntries");

    let selectedFile = null;

    imageInput.addEventListener("change", (e) => {
      const file = e.target.files[0];
      if (file) {
        selectedFile = file;
        const reader = new FileReader();
        reader.onload = (evt) => {
          imagePreview.src = evt.target.result;
          imagePreview.style.display = "block";
        };
        reader.readAsDataURL(file);
      } else {
        selectedFile = null;
        imagePreview.style.display = "none";
        imagePreview.src = "";
      }
    });

    window.saveEntry = async function () {
      const noteText = noteInput.value.trim();
      if (!noteText && !selectedFile) {
        alert("Please add a note or upload a photo.");
        return;
      }

      let imageUrl = "";
      let imagePath = "";

      if (selectedFile) {
        const storageRef = ref(
          storage,
          "healing_images/" + Date.now() + "_" + selectedFile.name
        );
        await uploadBytes(storageRef, selectedFile);
        imageUrl = await getDownloadURL(storageRef);
        imagePath = storageRef.fullPath;
      }

      await addDoc(collection(db, "healingEntries"), {
        note: noteText,
        imageUrl: imageUrl,
        imagePath: imagePath,
        timestamp: Date.now()
      });

      noteInput.value = "";
      imageInput.value = "";
      imagePreview.style.display = "none";
      imagePreview.src = "";
      selectedFile = null;

      loadEntries();
    };

    window.deleteEntry = async function (id, imagePath) {
      await deleteDoc(doc(db, "healingEntries", id));
      if (imagePath) {
        const imgRef = ref(storage, imagePath);
        await deleteObject(imgRef).catch(() => {
          // Ignore error if file missing
        });
      }
      loadEntries();
    };

    async function loadEntries() {
      entriesContainer.innerHTML = "<h3>Saved Healing Entries</h3>";

      const q = query(collection(db, "healingEntries"), orderBy("timestamp", "desc"));
      const querySnapshot = await getDocs(q);

      if (querySnapshot.empty) {
        entriesContainer.innerHTML += "<p>No entries yet.</p>";
        return;
      }

      querySnapshot.forEach((docSnap) => {
        const entry = docSnap.data();
        const entryId = docSnap.id;

        const div = document.createElement("div");
        div.className = "entry";

        let imgHTML = entry.imageUrl
          ? `<img src="${entry.imageUrl}" alt="Saved Healing Image" />`
          : "";
        let noteHTML = entry.note ? `<p>${entry.note}</p>` : "";

        div.innerHTML = `
          <button onclick="deleteEntry('${entryId}', '${entry.imagePath || ""}')">Delete</button>
          ${noteHTML}
          ${imgHTML}
        `;

        entriesContainer.appendChild(div);
      });
    }

    window.onload = loadEntries;
  </script>
</body>
</html>

  
