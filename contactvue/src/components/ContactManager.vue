<template>
  <div class="container">
    <h1>联系人管理</h1>
    <div class="button-container">
      <button @click="showAddModal">添加联系人</button>
    </div>
    <table>
      <thead>
      <tr>
        <th>姓名</th>
        <th>电话</th>
        <th>年龄</th>
        <th>性别</th>
        <th>地址</th>
        <th>操作</th>
      </tr>
      </thead>
      <tbody>
      <tr v-for="contact in contacts" :key="contact.id">
        <td>{{ contact.name }}</td>
        <td>{{ contact.phone }}</td>
        <td>{{ contact.age }}</td>
        <td>{{ contact.gender }}</td>
        <td>{{ contact.address }}</td>
        <td>
          <button @click="editContact(contact)">修改</button>
          <button @click="deleteContact(contact.id)">删除</button>
        </td>
      </tr>
      </tbody>
    </table>
    <modal v-if="showModal" @close="closeModal">
      <template #header>
        <h2>{{ editMode ? '修改联系人' : '添加联系人' }}</h2>
      </template>
      <template #body>
        <div>
          <label>姓名:</label>
          <input v-model="currentContact.name" />
          <label>电话:</label>
          <input v-model="currentContact.phone" />
          <label>年龄:</label>
          <input type="number" v-model="currentContact.age" />
          <label>性别:</label>
          <input v-model="currentContact.gender" />
          <label>地址:</label>
          <input v-model="currentContact.address" />
        </div>
      </template>
      <template #footer>
        <div class="button-group">
          <button @click="saveContact">确认</button>
          <button @click="closeModal">取消</button>
        </div>
      </template>
    </modal>
  </div>
</template>

<script>
import axios from 'axios';
import Modal from './Modal.vue';

export default {
  components: { Modal },
  data() {
    return {
      contacts: [],
      currentContact: { name: '', phone: '', age: null, gender: '', address: '' },
      showModal: false,
      editMode: false,
    };
  },
  methods: {
    fetchContacts() {
      axios.get('/api/contacts').then(response => {
        this.contacts = response.data;
      });
    },
    showAddModal() {
      this.currentContact = { name: '', phone: '', age: null, gender: '', address: '' };
      this.editMode = false;
      this.showModal = true;
    },
    editContact(contact) {
      this.currentContact = { ...contact };
      this.editMode = true;
      this.showModal = true;
    },
    deleteContact(id) {
      axios.delete(`/api/contacts/${id}`).then(() => {
        this.fetchContacts();
      });
    },
    saveContact() {
      const method = this.editMode ? 'put' : 'post';
      const url = this.editMode ? '/api/contacts' : '/api/contacts';

      axios[method](url, this.currentContact).then(() => {
        this.closeModal();
        this.fetchContacts();
      });
    },
    closeModal() {
      this.showModal = false;
    }
  },
  mounted() {
    this.fetchContacts();
  }
};
</script>


<style scoped>
/* 页面主样式 */
h1 {
  text-align: center;
  font-size: 2rem;
  margin: 20px 0;
  color: #333;
}

/* 表格容器 */
.container {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  min-height: 100vh;
}

/* 表格样式 */
table {
  width: 50vw; /* 设置表格宽度为视口宽度的一半 */
  margin: 20px auto; /* 表格水平居中 */
  border-collapse: collapse;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
}

th, td {
  padding: 12px;
  border: 1px solid #ccc;
  text-align: center;
  word-wrap: break-word;
  white-space: nowrap; /* 防止内容换行 */
  overflow: hidden; /* 隐藏溢出文本 */
  text-overflow: ellipsis; /* 使用省略号表示溢出文本 */
}

th {
  background-color: #f2f2f2;
  font-weight: bold;
}

tbody tr:hover {
  background-color: #f5f5f5;
}

/* 媒体查询 */
@media (max-width: 768px) {
  table {
    display: block; /* 在小屏幕上使用块布局 */
    overflow-x: auto; /* 超出部分水平滚动 */
    white-space: nowrap; /* 避免单元格内容换行 */
  }

  th, td {
    display: block; /* 每个单元格为块元素 */
    text-align: right; /* 文字右对齐 */
  }

  th {
    text-align: left; /* 表头文字左对齐 */
  }

  tbody tr {
    margin-bottom: 10px; /* 行间距 */
    border: none; /* 移除行的边框 */
  }
}


/* 按钮容器样式 */
.button-container {
  display: flex; /* 使用 Flexbox 排列按钮 */
  justify-content: center; /* 水平居中 */
  margin-bottom: 20px; /* 下方外边距 */
}

/* 按钮组样式 */
.button-group {
  display: flex; /* 使用 Flexbox 布局 */
  justify-content: center; /* 水平居中 */
  gap: 10px; /* 按钮之间的间距 */
  flex-wrap: nowrap; /* 防止换行 */
  max-width: 100%; /* 最大宽度限制 */
}

/* 按钮样式 */
button {
  padding: 10px 15px; /* 内边距 */
  background-color: #007bff; /* 按钮背景颜色 */
  color: white; /* 按钮字体颜色 */
  border: none; /* 去掉边框 */
  border-radius: 5px; /* 圆角 */
  cursor: pointer; /* 鼠标悬停时的指针样式 */
  transition: background-color 0.3s; /* 背景颜色过渡 */
  margin: 0 5px; /* 按钮间距 */
  font-size: 1rem; /* 字体大小 */
}

button:hover {
  background-color: #0056b3; /* 鼠标悬停时背景颜色变化 */
}



/* 输入框样式 */
input {
  width: 100%; /* 输入框宽度 */
  padding: 10px; /* 内边距 */
  margin: 10px 0; /* 上下外边距 */
  border: 1px solid #ccc; /* 边框 */
  border-radius: 5px; /* 圆角 */
  box-shadow: inset 0 1px 3px rgba(0, 0, 0, 0.1); /* 内部阴影 */
}

input:focus {
  border-color: #007bff; /* 聚焦时边框颜色 */
  outline: none; /* 去掉默认聚焦轮廓 */
}

.button-group {
  display: flex; /* 使用 flexbox 布局 */
  justify-content: center; /* 水平居中 */
  gap: 10px; /* 按钮之间的间距 */
}

button {
  padding: 10px 20px; /* 增加按钮内边距 */
  border: none; /* 去掉边框 */
  border-radius: 5px; /* 圆角 */
  background-color: #007bff; /* 按钮背景色 */
  color: white; /* 文字颜色 */
  cursor: pointer; /* 鼠标指针效果 */
  transition: background-color 0.3s; /* 背景色过渡效果 */
}

button:hover {
  background-color: #0056b3; /* 鼠标悬停时背景色 */
}

</style>


