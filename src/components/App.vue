<template>
  <title></title>
  <div class="container column">
    <form class="card card-w30" @submit.prevent="addBlock">
      <div class="form-control">
        <label for="type">Тип блока</label>
        <select id="type" v-model="blockType">
          <option value="title">Заголовок</option>
          <option value="subtitle">Подзаголовок</option>
          <option value="avatar">Аватар</option>
          <option value="text">Текст</option>
        </select>
      </div>

      <div class="form-control">
        <label for="value">Значение</label>
        <textarea id="value" rows="3" v-model="blockValue"></textarea>
      </div>

      <button type="submit" class="btn primary">Добавить</button>
    </form>

    
    <div class="card card-w70">
      <h1>{{ title }}</h1>
      <div  class="avatar">
        <img :src="avatarUrl">
      </div>
      <h2>{{ subtitle }}</h2>
      <p>
        {{ text }}
      </p>
      <h3 v-if="!title && !subtitle && !text"></h3>
    </div>
  </div>
  <div class="container">
    <p v-if="!commentsLoaded">
      <button  @click="loadComments" class="btn primary">Загрузить комментарии</button>
    </p>
    <div class="card">
      <h2>Комментарии</h2>
      <ul class="list">
        <li class="list-item" v-for="(comment, index) in comments" :key="index">
          <div>
            <p><strong>{{ comment.email }}</strong></p>
            <small>{{ comment.body }}</small>
          </div>
        </li>
      </ul>
    </div>
    <div class="loader" v-if="loadingComments"></div>
  </div>
</template>

<script>


export default {
  data() {
    return {
      blockType: 'title',
      blockValue: '',
      title: '',
      subtitle: '',
      text: '',
      avatarUrl: '',
      comments: [],
      loadingComments: false,
      commentsLoaded: false // Добавленное свойство для отслеживания загрузки комментариев
    };
  },
  methods: {
    addBlock() {
      if (this.blockType === 'title') {
        this.title = this.blockValue;
      } else if (this.blockType === 'subtitle') {
        this.subtitle = this.blockValue;
      } else if (this.blockType === 'text') {
        this.text = this.blockValue;
      } else if (this.blockType === 'avatar') {
        this.avatarUrl = this.blockValue; // сохраняем url в переменную для отображения аватара
      }
      this.blockValue = ''; // очищаем значение после добавления блока
      this.blockType = 'title'; 
       
    } ,
    async loadComments() {
      this.loadingComments = true;
      try {
        const response = await fetch('https://jsonplaceholder.typicode.com/comments?_limit=42');
        if (!response.ok) {
          throw new Error('Failed to fetch comments');
        }
        const comments = await response.json();
        this.comments = comments;
        this.commentsLoaded = true; // Устанавливаем флаг загрузки комментариев в true
      } catch (error) {
        console.error('Error fetching comments:', error);
      } finally {
        this.loadingComments = false;
      }
    }
  }
  }
  

</script>

<style>
  .avatar {
    display: flex;
    justify-content: center;
  }

  .avatar img {
    width: 150px;
    height: 150px;
    border-radius: 50%;
  }
</style>
