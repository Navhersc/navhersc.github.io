<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1" />
<title>Healing Journey with Firebase</title>
<style>
  body {
    font-family: Arial, sans-serif;
    background-color: #e6ccff;
    margin: 0; padding: 20px;
    max-width: 700px;
    margin-left: auto; margin-right: auto;
  }
  textarea {
    width: 100%; height: 100px;
    border-radius: 10px;
    padding: 10px;
    resize: vertical;
    font-family: inherit;
  }
  input[type="file"] {
    margin-top: 10px;
  }
  button {
    margin-top: 10px;
    padding: 10px 15px;
    background-color: #cc99ff;
    border: none;
    border-radius: 8px;
    color: white;
    cursor: pointer;
  }
  .entry {
    background: #fff0ff;
    padding: 10px;
    border-radius: 8px;
    margin-top: 20px;
    position: relative;
  }
  .entry img {
    max-width: 100%;
    margin-top: 10px;
    border-radius: 8px;
  }
  .entry button.delete {
    position: absolute;
    top: 10px;
    right: 10px;
    background-color: #ff6699;
    font-size: 0.8rem;
    padding: 5px 8px;
  }
</style>
</head>
<body>

<h1>My Healing Journey</h1>

<label for="note">Write a note:</label>
<textarea id="note" placeholder="Write your healing note here..."></textarea>

<label for="photo">Upload a photo:</label>
<input type="file" id="photo" accept="image/*" />

<button id="saveBtn">Save Entry</button>

<div id="entries">
  <h2>Healing Entries</h2>
  <!-- entries will appear here -->
</div>

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

  // Your Firebase config (replace with your own config)
  const firebaseConfig = {
    apiKey: "AIzaSyD7mJP8U3IRLSv-DgVQJmNolG7ouRA-dv8",
    authDomain: "healing-journey-d7204.firebaseapp.com",
    projectId: "healing-journey-d7204",
    storageBucket: "healing-journey-d7204.appspot.com",
    messagingSenderId: "477815035331",
    appId: "1:477815035331:web:1ec12501a78f818d8a1976"
  };

  const app = initializeApp(firebaseConfig);
  const db = getFirestore(app);
  const storage = getStorage(app);

  const noteInput = document.getElementById("note");
  const photoInput = document.getElementById("photo");
  const saveBtn = document.getElementById("saveBtn");
  const entriesDiv = document.getElementById("entries");

  saveBtn.addEventListener("click", async () => {
    const note = noteInput.value.trim();
    const file = photoInput.files[0];

    if (!note && !file) {
      alert("Please write a note or select a photo.");
      return;
    }

    saveBtn.disabled = true;
    saveBtn.textContent = "Saving...";

    let imageUrl = "";
    let imagePath = "";

    if (file) {
      try {
        const storageRef = ref(storage, `healing_photos/${Date.now()}_${file.name}`);
        await uploadBytes(storageRef, file);
        imageUrl = await getDownloadURL(storageRef);
        imagePath = storageRef.fullPath;
      } catch (error) {
        alert("Error uploading photo: " + error.message);
        saveBtn.disabled = false;
        saveBtn.textContent = "Save Entry";
        return;
      }
    }

    try {
      await addDoc(collection(db, "healingEntries"), {
        note,
        imageUrl,
        imagePath,
        timestamp: Date.now()
      });
      noteInput.value = "";
      photoInput.value = "";
      loadEntries();
    } catch (error) {
      alert("Error saving entry: " + error.message);
    }

    saveBtn.disabled = false;
    saveBtn.textContent = "Save Entry";
  });

  async function loadEntries() {
    entriesDiv.innerHTML = "<h2>Healing Entries</h2>";
    const q = query(collection(db, "healingEntries"), orderBy("timestamp", "desc"));
    const querySnapshot = await getDocs(q);

    if (querySnapshot.empty) {
      entriesDiv.innerHTML += "<p>No entries yet.</p>";
      return;
    }

    querySnapshot.forEach((docSnap) => {
      const data = docSnap.data();
      const id = docSnap.id;

      const entryDiv = document.createElement("div");
      entryDiv.className = "entry";

      const noteHTML = data.note ? `<p>${data.note}</p>` : "";
      const imgHTML = data.imageUrl ? `<img src="${data.imageUrl}" alt="Healing Photo" />` : "";

      entryDiv.innerHTML = `
        ${noteHTML}
        ${imgHTML}
        <button class="delete">Delete</button>
      `;

      const deleteBtn = entryDiv.querySelector(".delete");
      deleteBtn.onclick = async () => {
        if (confirm("Delete this entry?")) {
          await deleteDoc(doc(db, "healingEntries", id));
          if (data.imagePath) {
            const imgRef = ref(storage, data.imagePath);
            await deleteObject(imgRef).catch(() => {});
          }
          loadEntries();
        }
      };

      entriesDiv.appendChild(entryDiv);
    });
  }

  loadEntries();
</script>

</body>
</html>
