<template>
  <div>
    <h1>FORUM</h1>
    <div>
      <h2>Pick a topic:</h2>
      <select id="textDropdown" v-model="selectedTopicId" @change="loadTexts">
        <option v-for="item in items" :value="item.id" :key="item.id">{{ item.topic }}</option>
      </select>
      <br>
    </div>
    <div>
      <h2>Add new text:</h2>
      <input type="text" id="newText" v-model="newText">
      <button @click="saveText">Save</button>
    </div>
    <ul>
      <li v-for="text in textList" :key="text.id">{{ text.text }}</li>
    </ul>
  </div>
</template>

<script>
export default {
  data() {
    return {
      items: [],
      selectedTopicId: 0,
      newText: '',
      textList: [],
    };
  },
  created() {
    // Load topics from server on component creation
    this.loadTopics();
  },
  methods: {
    async loadTopics() {
      // Send GET request to /topic_list to load topics from server
      const response = await fetch('http://localhost:8000/topic_list');
      const data = await response.json();
      this.items = data.map((topic, index) => {
        return { id: index + 1, topic: topic };
      });
    },
    async loadTexts() {
      // Send GET request to /texts_by_topic with selected topic ID to load texts from server
      const response = await fetch(`http://localhost:8000/texts_by_topic/${this.selectedTopicId}`);
      const data = await response.json();
      this.textList = data;
    },
    async saveText() {
      // Send POST request to /add to save new text to server
      const response = await fetch('http://localhost:8000/add', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
        },
        body: JSON.stringify({ text: this.newText, topic_id: this.selectedTopicId}),
      });
      if (response.ok) {
        this.newText = '';
        this.loadTexts();
      } else {
        console.error('Failed to save text:', response.statusText);
      }
    },
  },
};
</script>
