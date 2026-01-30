<template>
  <div class="container">
    <h1>Express + Vue 前后端分离项目</h1>
    
    <div class="stats">
      <h2>📊 服务器状态</h2>
      <button @click="fetchStats">获取统计数据</button>
      <div v-if="stats" class="stats-data">
        <p>用户数量: {{ stats.userCount }}</p>
        <p>留言数量: {{ stats.messageCount }}</p>
        <p>服务器: {{ stats.server }}</p>
        <p>时间戳: {{ stats.timestamp }}</p>
      </div>
    </div>

    <div class="users">
      <h2>👥 用户管理</h2>
      <button @click="fetchUsers">获取用户列表</button>
      <button @click="showAddUser = !showAddUser">添加用户</button>
      
      <div v-if="showAddUser" class="add-user-form">
        <input v-model="newUser.name" placeholder="姓名" />
        <input v-model="newUser.age" type="number" placeholder="年龄" />
        <input v-model="newUser.email" placeholder="邮箱" />
        <button @click="addUser">提交</button>
      </div>

      <div v-if="users.length" class="user-list">
        <div v-for="user in users" :key="user.id" class="user-item">
          <span>{{ user.name }} - {{ user.age }}岁 - {{ user.email }}</span>
          <button @click="deleteUser(user.id)">删除</button>
        </div>
      </div>
    </div>

    <div class="messages">
      <h2>💬 留言板</h2>
      <button @click="fetchMessages">获取留言</button>
      
      <div class="add-message">
        <input v-model="newMessage" placeholder="输入留言内容" />
        <button @click="addMessage">发送留言</button>
      </div>

      <div v-if="messages.length" class="message-list">
        <div v-for="msg in messages" :key="msg.id" class="message-item">
          <p>{{ msg.content }}</p>
          <small>{{ msg.createdAt }}</small>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { ref, reactive } from 'vue'

export default {
  name: 'App',
  setup() {
    const stats = ref(null)
    const users = ref([])
    const messages = ref([])
    const showAddUser = ref(false)
    const newMessage = ref('')
    const newUser = reactive({
      name: '',
      age: '',
      email: ''
    })

    const API_BASE = '/api'

    const fetchStats = async () => {
      try {
        const response = await fetch(`${API_BASE}/stats`)
        const data = await response.json()
        if (data.success) {
          stats.value = data.data
        }
      } catch (error) {
        console.error('获取统计数据失败:', error)
      }
    }

    const fetchUsers = async () => {
      try {
        const response = await fetch(`${API_BASE}/users`)
        const data = await response.json()
        if (data.success) {
          users.value = data.data
        }
      } catch (error) {
        console.error('获取用户列表失败:', error)
      }
    }

    const addUser = async () => {
      if (!newUser.name || !newUser.age || !newUser.email) {
        alert('请填写完整信息')
        return
      }
      try {
        const response = await fetch(`${API_BASE}/users`, {
          method: 'POST',
          headers: { 'Content-Type': 'application/json' },
          body: JSON.stringify(newUser)
        })
        const data = await response.json()
        if (data.success) {
          users.value.push(data.data)
          newUser.name = ''
          newUser.age = ''
          newUser.email = ''
          showAddUser.value = false
        }
      } catch (error) {
        console.error('添加用户失败:', error)
      }
    }

    const deleteUser = async (id) => {
      try {
        const response = await fetch(`${API_BASE}/users/${id}`, {
          method: 'DELETE'
        })
        const data = await response.json()
        if (data.success) {
          users.value = users.value.filter(u => u.id !== id)
        }
      } catch (error) {
        console.error('删除用户失败:', error)
      }
    }

    const fetchMessages = async () => {
      try {
        const response = await fetch(`${API_BASE}/messages`)
        const data = await response.json()
        if (data.success) {
          messages.value = data.data
        }
      } catch (error) {
        console.error('获取留言失败:', error)
      }
    }

    const addMessage = async () => {
      if (!newMessage.value.trim()) {
        alert('请输入留言内容')
        return
      }
      try {
        const response = await fetch(`${API_BASE}/messages`, {
          method: 'POST',
          headers: { 'Content-Type': 'application/json' },
          body: JSON.stringify({ content: newMessage.value })
        })
        const data = await response.json()
        if (data.success) {
          messages.value.push(data.data)
          newMessage.value = ''
        }
      } catch (error) {
        console.error('发送留言失败:', error)
      }
    }

    return {
      stats,
      users,
      messages,
      showAddUser,
      newMessage,
      newUser,
      fetchStats,
      fetchUsers,
      addUser,
      deleteUser,
      fetchMessages,
      addMessage
    }
  }
}
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: Arial, sans-serif;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  min-height: 100vh;
  padding: 20px;
}

.container {
  max-width: 800px;
  margin: 0 auto;
  background: white;
  border-radius: 10px;
  padding: 30px;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.2);
}

h1 {
  color: #333;
  text-align: center;
  margin-bottom: 30px;
}

h2 {
  color: #667eea;
  margin: 20px 0 15px;
  border-bottom: 2px solid #667eea;
  padding-bottom: 10px;
}

button {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
  border: none;
  padding: 10px 20px;
  border-radius: 5px;
  cursor: pointer;
  margin: 5px;
  transition: transform 0.2s;
}

button:hover {
  transform: translateY(-2px);
}

input {
  padding: 10px;
  border: 1px solid #ddd;
  border-radius: 5px;
  margin: 5px;
  width: 200px;
}

.stats-data, .user-list, .message-list {
  margin-top: 15px;
}

.user-item, .message-item {
  background: #f5f5f5;
  padding: 15px;
  margin: 10px 0;
  border-radius: 5px;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.add-user-form, .add-message {
  margin: 15px 0;
  display: flex;
  gap: 10px;
}

.add-user-form input {
  flex: 1;
}

.add-message input {
  flex: 1;
}

small {
  color: #666;
}
</style>
