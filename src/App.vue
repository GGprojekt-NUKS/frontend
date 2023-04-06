<template>
  <div>
    <input v-model="newText" placeholder="Enter new text" />
    <button @click="addText">Add</button>
    <div>
      <div class="topics-container">
        <button v-for="(topic, index) in topicList" :key="index" class="topic-button" @click="openPopup(topic)">
          {{ topic }}
        </button>
      </div>
    </div>
    <div>
      <p v-for="(text, index) in textList" :key="index" class="text-item">
        {{ text }}
      </p>
    </div>

    <div v-if="popupVisible" class="popup">
      <h2>{{ selectedTopic }}</h2>
      <p>Here's some information about {{ selectedTopic }}...</p>
      <button @click="closePopup">Close</button>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      newText: "",
      textList: [],
      topicList: [],
      popupVisible: false,
      selectedTopic: "",
    };
  },
  created() {
    // Make an API call to get all previous texts and update the textList property
    fetch("http://195.47.197.24:8000/text_list")
      .then(response => response.json())
      .then(data => {
        this.textList = data;
      })
      .catch(error => {
        console.error(error);
      });

    // Make an API call to get all previous topics and update the topicList property
    fetch("http://195.47.197.24:8000/topic_list")
      .then(response => response.json())
      .then(data => {
        this.topicList = data;
      })
      .catch(error => {
        console.error(error);
      });
  },
  methods: {
    addText() {
      if (this.newText) {
        // Make an API call to add the new text
        fetch("http://195.47.197.24:8000/add", {
          method: "POST",
          headers: {
            "Content-Type": "application/json"
          },
          body: JSON.stringify({ text: this.newText })
        })
          .then(response => {
            if (!response.ok) {
              throw new Error("Failed to add text");
            }
            // Update the textList property with the new text
            this.textList.push(this.newText);
            this.newText = "";
          })
          .catch(error => {
            console.error(error);
          });
      }
    },

    openPopup(topic) {
      this.popupVisible = true;
      this.selectedTopic = topic;
    },

    closePopup() {
      this.popupVisible = false;
      this.selectedTopic = "";
    },
  }
};
</script>

<style>
.text-item {
  border: 1px solid black;
  padding: 10px;
}

.topics-container {
  margin-bottom: 10px;
}

.topic-button {
  margin-right: 10px;
}

.popup {
  position: fixed;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  background-color: white;
  border: 1px solid black;
  padding: 20px;
  z-index: 999;
}
</style>
