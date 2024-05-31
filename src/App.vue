<template>
  <div class="container">
    <h1 class="title">Literary Compilation</h1>
    <form @submit.prevent="save" class="form">
      <input type="text" v-model="form.title" placeholder="Title" class="input-field" /><br />
      <textarea v-model="form.content" placeholder="Content" class="textarea-field"></textarea><br />
      <button type="submit" class="submit-btn">Save</button>
    </form>
    <ul class="article-list">
      <li v-for="article in articles" :key="article.id" class="article-item">
        <strong>{{ article.title }}</strong><br />
        <span>{{ article.content }}</span><br />
        <button @click="edit(article)" class="edit-btn">Edit</button>
        <button @click="deleteArticle(article.id)" class="delete-btn">Delete</button>
      </li>
    </ul>
    <button @click="load" class="load-btn">Load</button>
  </div>
</template>

<script>
import { ref, onMounted, reactive } from 'vue';
import axios from 'axios';

export default {
  setup() {
    const form = reactive({
      id: null,
      title: '',
      content: '',
    });

    const articles = ref([]);

    async function load() {
      try {
        const response = await axios.get('http://localhost:3000/articles');
        articles.value = response.data;
      } catch (error) {
        console.error('Error loading articles:', error);
      }
    }

    async function save() {
      try {
        const url = form.id ? `http://localhost:3000/articles/${form.id}` : 'http://localhost:3000/articles';
        const method = form.id ? 'put' : 'post';
        const response = await axios[method](url, {
          title: form.title,
          content: form.content,
        });

        if (form.id) {
          articles.value = articles.value.map(article =>
            article.id === response.data.id ? response.data : article
          );
        } else {
          articles.value.push(response.data);
        }

        form.id = null;
        form.title = '';
        form.content = '';
      } catch (error) {
        console.error('Error saving article:', error);
      }
    }

    async function deleteArticle(id) {
      try {
        await axios.delete(`http://localhost:3000/articles/${id}`);
        articles.value = articles.value.filter(article => article.id !== id);
      } catch (error) {
        console.error('Error deleting article:', error);
      }
    }

    function edit(article) {
      form.id = article.id;
      form.title = article.title;
      form.content = article.content;
    }

    onMounted(load);

    return { form, articles, save, edit, load, deleteArticle };
  }
};
</script>

<style scoped>
.container {
  background-color: #C0D6E8;
  padding: 20px 40px;
  border-radius: 8px;
  max-width: 800px;
  margin: auto;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
}

.title {
  text-align: center;
  font-size: 2em;
  color: #333;
  margin-bottom: 20px;
}

.form {
  margin-bottom: 20px;
}

.input-field,
.textarea-field {
  width: 100%;
  padding: 12px;
  margin-bottom: 16px;
  border: 1px solid #ddd;
  border-radius: 8px;
  font-size: 16px;
  transition: border-color 0.3s ease, box-shadow 0.3s ease;
}

.input-field:focus,
.textarea-field:focus {
  outline: none;
  border-color: #D20062;
  box-shadow: 0 0 8px rgba(210, 0, 98, 0.2);
}

.submit-btn {
  background-color: #D20062;
  color: white;
  padding: 14px 24px;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  font-size: 16px;
  transition: background-color 0.3s ease;
}

.submit-btn:hover {
  background-color: #b30050;
}

.article-list {
  list-style-type: none;
  padding: 0;
}

.article-item {
  background-color: #FBF8DD;
  padding: 16px;
  margin-bottom: 16px;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.article-item strong {
  display: block;
  margin-bottom: 8px;
  font-size: 20px;
}

.article-item span {
  display: block;
  margin-bottom: 8px;
  font-size: 16px;
}

.edit-btn,
.delete-btn {
  background-color: #D20062;
  color: white;
  padding: 8px 16px;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  font-size: 14px;
  transition: background-color 0.3s ease;
}

.edit-btn:hover,
.delete-btn:hover {
  background-color: #b30050;
}

.load-btn {
  display: block;
  background-color: #D20062;
  color: white;
  padding: 14px 24px;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  font-size: 16px;
  transition: background-color 0.3s ease;
  margin: 20px auto;
  text-align: center;
}

.load-btn:hover {
  background-color: #b30050;
}
</style>
