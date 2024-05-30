<template>
  <div class="container">
    <form @submit.prevent="save" class="form-container">
      <input type="text" v-model="form.title" placeholder="Title" class="form-input" /><br />
      <textarea v-model="form.content" placeholder="Content" class="form-input"></textarea><br />
      <button type="submit" class="btn">{{ form.id ? 'Update' : 'Save' }}</button>
    </form>
    <ul class="articles-list">
      <li v-for="article in articles" :key="article.id" class="article-item">
        <div class="article">
          <h3>{{ article.title }}</h3>
          <p>{{ article.content }}</p>
          <button @click="edit(article)" class="btn edit-btn">Edit</button>
          <button @click="remove(article.id)" class="btn delete-btn">Delete</button>
        </div>
      </li>
    </ul>
    <button @click="load" class="btn load-btn">Load</button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      articles: [],
      form: {
        id: null,
        title: '',
        content: ''
      }
    };
  },
  methods: {
    async save() {
      if (this.form.id) {
        // Update existing article
        const index = this.articles.findIndex(article => article.id === this.form.id);
        if (index !== -1) {
          this.articles.splice(index, 1, { ...this.form });
        }
      } else {
        // Add new article
        const newArticle = { ...this.form, id: Date.now().toString() };
        this.articles.push(newArticle);
      }
      this.resetForm();
    },
    edit(article) {
      this.form = { ...article };
    },
    remove(id) {
      const index = this.articles.findIndex(article => article.id === id);
      if (index !== -1) {
        this.articles.splice(index, 1);
      }
    },
    async load() {
      try {
        const response = await fetch('/db.json');
        const data = await response.json();
        this.articles = data.articles;
      } catch (error) {
        console.error('Error loading articles:', error);
      }
    },
    resetForm() {
      this.form = {
        id: null,
        title: '',
        content: ''
      };
    }
  },
  mounted() {
    this.load(); // Load articles when component is mounted
  }
};
</script>

<style scoped>
.container {
  font-family: Arial, sans-serif;
  padding: 20px;
  background-color: #F4EFEA; /* light brown background */
  color: #3E2723; /* dark brown text */
}

.form-container {
  margin-bottom: 20px;
  padding: 20px;
  background-color: #D7CCC8; /* light brown form background */
  border-radius: 10px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.form-input {
  width: calc(100% - 20px);
  padding: 10px;
  margin: 5px 0;
  border: 1px solid #A1887F; /* brown border */
  border-radius: 5px;
  background-color: #EFEBE9; /* very light brown input background */
}

.btn {
  padding: 10px 15px;
  margin: 5px;
  border: none;
  border-radius: 5px;
  color: #FFF;
  background-color: #6D4C41; /* dark brown button background */
  cursor: pointer;
  transition: background-color 0.3s;
}

.btn:hover {
  background-color: #8D6E63; /* lighter brown on hover */
}

.articles-list {
  list-style-type: none;
  padding: 0;
}

.article-item {
  margin-bottom: 20px;
  padding: 20px;
  background-color: #D7CCC8; /* light brown article background */
  border-radius: 10px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.article {
  display: flex;
  flex-direction: column;
}

.edit-btn {
  background-color: #FFD700; /* gold button background */
  color: #3E2723; /* dark brown text */
}

.edit-btn:hover {
  background-color: #FFB300; /* darker gold on hover */
}

.delete-btn {
  background-color: #D32F2F; /* red delete button background */
}

.delete-btn:hover {
  background-color: #B71C1C; /* darker red on hover */
}
</style>
