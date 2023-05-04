<script setup>
import { ref, onMounted } from "vue";
import { format } from "date-fns";

const showModal = ref(false);
const newNotes = ref("");
const errorMessage = ref("");
const notes = ref([]);
const updateNotes = ref(false);
const updateID = ref();

onMounted(() => {
  const storedNotes = localStorage.getItem("notes");
  if (storedNotes) {
    notes.value = JSON.parse(storedNotes);
  }
});

const openModal = () => {
  updateNotes.value = false;
  showModal.value = true;
};

const closeModal = () => {
  showModal.value = false;
  errorMessage.value = "";
  newNotes.value = "";
  updateID.value = null;
};

function getRandomColor() {
  return "hsl(" + Math.random() * 360 + ", 100%, 75%)";
}

const addNotes = () => {
  if (newNotes.value.length < 10) {
    return (errorMessage.value = "Note needs to be 10 characters or more.");
  }
  notes.value.push({
    id: Math.floor(Math.random() * 1000000),
    text: newNotes.value,
    date: new Date(),
    backgroundColor: getRandomColor(),
  });
  showModal.value = false;
  newNotes.value = "";
  errorMessage.value = "";
  localStorage.setItem("notes", JSON.stringify(notes.value));
};

const editNotes = (note) => {
  newNotes.value = note.text;
  updateID.value = note.id;
  showModal.value = true;
  updateNotes.value = true;
};

const updateNote = (id, newText) => {
  const noteToUpdateIndex = notes.value.findIndex((note) => note.id === id);
  notes.value[noteToUpdateIndex].text = newText;
  showModal.value = false;
  updateNotes.value = false;
  updateID.value = null;
  newNotes.value = "";
  localStorage.setItem("notes", JSON.stringify(notes.value));
};

const formatDate = (date) => {
  const options = { month: "short", day: "2-digit", year: "numeric" };
  return date.toLocaleDateString("en-US", options);
};
</script>

<template>
  <main>
    <div v-if="showModal" class="overlay">
      <div class="modal">
        <textarea
          v-model.trim="newNotes"
          name="note"
          id="note"
          cols="30"
          rows="10"
        ></textarea>
        <p v-if="errorMessage">{{ errorMessage }}</p>
        <button v-if="!updateNotes" @click="addNotes()">Add Note</button>
        <button v-if="updateNotes" @click="updateNote(updateID, newNotes)">
          Update Note
        </button>
        <button @click="closeModal()" class="close">Close</button>
      </div>
    </div>
    <div class="container">
      <header>
        <h1>Vue Notes</h1>
        <button @click="openModal()">+</button>
      </header>
      <div class="cards-container">
        <div
          v-for="note in notes"
          class="card"
          :key="note.id"
          :style="{ backgroundColor: note.backgroundColor }"
        >
          <p class="main-text">{{ note.text }}</p>
          <div class="cards-footer">
            <p class="date">{{ formatDate(new Date(note.date)) }}</p>
            <div @click="editNotes(note)" class="icon-container">
              <svg
                class="icon-pen"
                xmlns="http://www.w3.org/2000/svg"
                viewBox="0 0 512 512"
              >
                <path
                  d="M362.7 19.3L314.3 67.7 444.3 197.7l48.4-48.4c25-25 25-65.5 0-90.5L453.3 19.3c-25-25-65.5-25-90.5 0zm-71 71L58.6 323.5c-10.4 10.4-18 23.3-22.2 37.4L1 481.2C-1.5 489.7 .8 498.8 7 505s15.3 8.5 23.7 6.1l120.3-35.4c14.1-4.2 27-11.8 37.4-22.2L421.7 220.3 291.7 90.3z"
                />
              </svg>
            </div>
          </div>
        </div>
      </div>
    </div>
  </main>
</template>

<style scope>
main {
  height: 100vh;
  width: 100vw;
  font-family: Arial, Helvetica, sans-serif;
}

.container {
  max-width: 1000px;
  padding: 10px;
  margin: 0 auto;
}

header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

h1 {
  font-weight: bold;
  margin-bottom: 25px;
  font-size: 75px;
}

button {
  border: none;
  padding: 10px;
  width: 50px;
  height: 50px;
  cursor: pointer;
  background-color: rgb(21, 20, 20);
  border-radius: 100%;
  color: white;
  font-size: 20px;
}

.card {
  width: 225px;
  height: 225px;
  padding: 10px;
  border-radius: 15px;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  margin-right: 20px;
  margin-bottom: 20px;
  flex-wrap: nowrap;
}

.date {
  font-size: 12.5px;
  font-weight: bold;
}

.cards-container {
  display: flex;
  flex-wrap: wrap;
  word-wrap: break-word;
}

.overlay {
  position: absolute;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.77);
  z-index: 10;
  display: flex;
  align-items: center;
  justify-content: center;
}

.modal {
  width: 750px;
  background-color: white;
  border-radius: 10px;
  padding: 30px;
  position: relative;
  display: flex;
  flex-direction: column;
}

.modal button {
  padding: 10px 20px;
  font-size: 20px;
  width: 100%;
  background-color: blueviolet;
  border: none;
  color: white;
  cursor: pointer;
  margin-top: 15px;
  border-radius: 0%;
}

.modal .close {
  background-color: rgb(193, 15, 15);
  margin-top: 7px;
}

.modal p {
  color: rgb(193, 15, 15);
  font-weight: bold;
}

.icon-pen {
  margin: 0;
  padding: 0;
  height: 20px;
  width: 20px;
  margin-right: 10px;
}

.cards-footer {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.icon-container {
  cursor: pointer;
}

.card .main-text {
  font-size: 14px;
}
</style>
