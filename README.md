# Шаг 1: Создание простого API

1) Проверьте версию Node.js и npm
   
```
node -v
npm -v
```

2) VUE CLI

Vue CLI — полноценная система для быстрой разработки на Vue.js, предоставляющая:
- Интерактивное создание проекта через @vue/cli.
- Быстрое прототипирование через @vue/cli + @vue/cli-service-global без конфигурации.
- Runtime-зависимость (@vue/cli-service) предоставляющая:
  а) Возможность обновления;
  б) Создана поверх webpack, с оптимальными настройками по умолчанию;
  в) Настройка с помощью конфигурационного файла в проекте;
  г) Расширяемость плагинами
- Большая коллекция официальных плагинов, интегрирующих лучшие инструменты экосистемы фронтенда.
- Полноценный графический пользовательский интерфейс для создания и управления проектами Vue.js.
  
```
npm install -g @vue/cli
```

3) Установка Axios

Axios — это популярная JavaScript-библиотека для выполнения HTTP-запросов. Она работает как на стороне клиента (в браузерах), так и на стороне сервера (в Node.js). Axios упрощает выполнение HTTP-запросов, таких как GET, POST, PUT, DELETE и другие, и предоставляет удобный способ взаимодействия с API.

Основные функции и возможности Axios:
- Простота в использовании: Axios предоставляет интуитивно понятный API для выполнения HTTP-запросов.
- Поддержка промисов: Axios работает с промисами, что делает его совместимым с async/await синтаксисом.
- Автоматическое преобразование данных: Axios автоматически преобразует JSON-данные.
- Интерцепторы: Позволяет перехватывать запросы и ответы, добавлять токены аутентификации, обрабатывать ошибки и т.д.
- Поддержка тайм-аутов: Позволяет установить максимальное время ожидания для запросов.
- Отправка данных в формате URLSearchParams: Поддерживает отправку данных в различных форматах, таких как URLSearchParams, FormData и т.д.
- Отмена запросов: Поддержка отмены запросов с использованием токенов отмены.
- Работа с заголовками: Легко настраивать заголовки запросов.
   
```
npm install axios
```

4) Установка необходимых зависимостей

```
npm install express body-parser sqlite3
```


# Шаг 2: Создание клиента на Vue.js

1) Создание API модуля

Создайте файл src/api.js и добавьте туда методы для взаимодействия с сервером:

```
import axios from 'axios';

const apiClient = axios.create({
  baseURL: 'http://localhost:3000/api',
  withCredentials: false,
  headers: {
    Accept: 'application/json',
    'Content-Type': 'application/json',
  },
});

export default {
  getUsers() {
    return apiClient.get('/users');
  },
  addUser(user) {
    return apiClient.post('/users', user);
  },
  deleteUser(id) {
    return apiClient.delete(`/users/${id}`);
  },
};
```

2) Создание компонента для отображения списка пользователей

```
<template>
  <div>
    <h1>User List</h1>
    <ul>
      <li v-for="user in users" :key="user._id">
        {{ user.firstName }} {{ user.middleName }} {{ user.lastName }} ({{ user.birthYear }})
        <button @click="deleteUser(user._id)">Delete</button>
      </li>
    </ul>
    <input v-model="newUserFirstName" placeholder="First Name">
    <input v-model="newUserMiddleName" placeholder="Middle Name">
    <input v-model="newUserLastName" placeholder="Last Name">
    <input v-model="newUserBirthYear" placeholder="Birth Year" type="number">
    <button @click="addUser">Add User</button>
  </div>
</template>

<script>
import api from '../api';

export default {
  data() {
    return {
      users: [],
      newUserFirstName: '',
      newUserMiddleName: '',
      newUserLastName: '',
      newUserBirthYear: ''
    };
  },
  created() {
    this.fetchUsers();
  },
  methods: {
    fetchUsers() {
      api.getUsers().then(response => {
        this.users = response.data;
      });
    },
    addUser() {
      const newUser = {
        firstName: this.newUserFirstName,
        middleName: this.newUserMiddleName,
        lastName: this.newUserLastName,
        birthYear: parseInt(this.newUserBirthYear, 10)
      };
      api.addUser(newUser).then(response => {
        this.users.push(response.data);
        this.newUserFirstName = '';
        this.newUserMiddleName = '';
        this.newUserLastName = '';
        this.newUserBirthYear = '';
      });
    },
    deleteUser(id) {
      api.deleteUser(id).then(() => {
        this.users = this.users.filter(user => user._id !== id);
      });
    },
  },
};
</script>
```

3) Обновление основного компонента App.vue

```
<template>
  <div id="app">
    <UserList />
  </div>
</template>

<script>
import UserList from './components/UserList.vue';

export default {
  name: 'App',
  components: {
    UserList,
  },
};
</script>
```

4) Запуск Vue приложения

```
npm run serve
```
