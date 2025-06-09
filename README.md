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
    .entry button.delete-btn {
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
      <label for="note"><strong>Write a Note</strong></label><br>
      <textarea id="note" placeholder="Today I felt..."></textarea>

      <label for="imageUpload"><strong>Upload a Photo</strong></label><br>
      <input type="file" id="imageUpload" accept="image/*" /><br>
      <img id="imagePreview" style="display:none; max-width: 100%; margin-top: 1rem;" />

      <br><button id="saveBtn">Save Entry</button>
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

  <!-- Firebase SDKs -->
  <script type="module">
    // Import Firebase functions
    import { initializeApp } from "https://www.gstatic.com/firebasejs/9.23.0/firebase-app.js";
    import { getFirestore, collection, addDoc, getDocs, deleteDoc, doc, query, orderBy } from "https://www.gstatic.com/firebasejs/9.23.0/firebase-firestore.js";
    import { getStorage, ref, uploadString, getDownloadURL, deleteObject } from "https://www.gstatic.com/firebasejs/9.23.0/firebase-storage.js";

    // Your web app's Firebase configuration
    const firebaseConfig = {
      apiKey: "AIzaSyD7mJP8U3IRLSv-DgVQJmNolG7ouRA-dv8",
      authDomain: "healing-journey-d7204.firebaseapp.com",
      projectId: "healing-journey-d7204",
      storageBucket: "healing-journey-d7204.appspot.com",
      messagingSenderId: "477815035331",
      appId: "1:477815035331:web:1ec12501a78f818d8a1976",
      measurementId: "G-LDMTQLDMGB"
    };

    // Initialize Firebase
    const app = initializeApp(firebaseConfig);
    const db = getFirestore(app);
    const storage = getStorage(app);

    const noteInput = document.getElementById('note');
    const imageUpload = document.getElementById('imageUpload');
    const imagePreview = document.getElementById('imagePreview');
    const saveBtn = document.getElementById('saveBtn');
    const savedEntriesContainer = document.getElementById('savedEntries');

    let savedImageData = "";

    // Handle image preview and store base64 string temporarily
    imageUpload.addEventListener('change', (e) => {
      const file = e.target.files[0];
      if (file) {
        const reader = new FileReader();
        reader.onload = function(evt) {
          savedImageData = evt.target.result;
          imagePreview.src = savedImageData;
          imagePreview.style.display = "block";
        };
        reader.readAsDataURL(file);
      } else {
        savedImageData = "";
        imagePreview.style.display = "none";
      }
    });

    // Save entry to Firestore and Storage
    saveBtn.addEventListener('click', async () => {
      const note = noteInput.value.trim();

      if (!note && !savedImageData) {
        alert("Please enter a note or upload a picture.");
        return;
      }

      saveBtn.disabled = true;
      saveBtn.textContent = "Saving...";

      let imageUrl = "";

      try {
        // If there is an image, upload it to Firebase Storage first
        if (savedImageData) {
          const imageRef = ref(storage, 'healing_images/' + Date.now() + '.jpg');
          // Upload base64 string
          await uploadString(imageRef, savedImageData, 'data_url');
          imageUrl = await getDownloadURL(imageRef);
        }

        // Save entry in Firestore
        await addDoc(collection(db, "healingEntries"), {
          note: note,
          imageUrl: imageUrl,
          createdAt: new Date()
        });

        // Reset inputs
        noteInput.value = "";
        imageUpload.value = "";
        imagePreview.style.display = "none";
        savedImageData = "";

        await loadEntries();

      } catch (error) {
        alert("Error saving entry: " + error.message);
      }

      saveBtn.disabled = false;
      saveBtn.textContent = "Save Entry";
    });

    // Load entries from Firestore and display
    async function loadEntries() {
      savedEntriesContainer.innerHTML = "<h3>Saved Healing Entries</h3>";

      const q = query(collection(db, "healingEntries"), orderBy("createdAt", "desc"));
      const querySnapshot = await getDocs(q);

      querySnapshot.forEach(docSnap => {
        const data = docSnap.data();
        const div = document.createElement('div');
        div.className = "entry";

        div.innerHTML = `
          <button class="delete-btn" data-id="${docSnap.id}">Delete</button>
          <p>${data.note ? data.note : ''}</p>
          ${data.imageUrl ? `<img src="${data.imageUrl}" alt="Saved Healing Image" />` : ''}
        `;

        savedEntriesContainer.appendChild(div);
      });

      // Add event listeners for all delete buttons
      document.querySelectorAll('.delete-btn').forEach(btn => {
        btn.addEventListener('click', async (e) => {
          const id = e.target.getAttribute('data-id');
          if(confirm("Are you sure you want to delete this entry?")) {
            await deleteEntry(id);
          }
        });
      });
    }

    // Delete entry and associated image
    async function deleteEntry(docId) {
      // Get document data to delete the image from Storage
      const docRef = doc(db, "healingEntries", docId);
      const docSnap = await getDocs(docRef);

      try {
        // Delete image in Storage if exists
        const entrySnapshot = await doc(db, "healingEntries", docId).get();
        if (entrySnapshot.exists()) {
          const data = entrySnapshot.data();
          if (data.imageUrl) {
            const imageRef = ref(storage, data.imageUrl);
            await deleteObject(imageRef).catch(() => {
              // ignore if image already deleted or not found
            });
          }
        }
      } catch {}

      // Delete Firestore document
      await deleteDoc(docRef);
      await loadEntries();
    }

    // Initial load
    window.onload = loadEntries;
  </script>
</body>
</html>
``

   
