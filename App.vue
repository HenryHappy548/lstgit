<template>
  <div class="whatsapp-app">
    <div class="chat-container">
      
      <div class="sidebar">
        <div class="header">
          <div class="user-profile">
            <div class="avatar">ME</div>
            <h2>Messages</h2>
          </div>
          <div class="header-icons">
            <button class="icon-btn">
              <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                <circle cx="11" cy="11" r="8"></circle>
                <line x1="21" y1="21" x2="16.65" y2="16.65"></line>
              </svg>
            </button>
            <button class="icon-btn">
             <a href="./src/Henry.vue"> <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                <path d="M4 4h16c1.1 0 2 .9 2 2v12c0 1.1-.9 2-2 2H4c-1.1 0-2-.9-2-2V6c0-1.1.9-2 2-2z"></path>
                <polyline points="22,6 12,13 2,6"></polyline>
              </svg></a>
            </button>
          </div>
        </div>
        <div class="search">
          <div class="search-container">
            <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
              <circle cx="11" cy="11" r="8"></circle>
              <line x1="21" y1="21" x2="16.65" y2="16.65"></line>
            </svg>
            <input type="text" placeholder="Search messages..." v-model="searchText">
          </div>
        </div>
        <div class="chat-list">
          <div 
            v-for="chat in filteredChats" 
            :key="chat.id"
            class="chat-item"
            :class="{ active: activeChat === chat.id }"
            @click="activeChat = chat.id"
          >
            <div class="avatar" :style="{ backgroundColor: chat.color }">{{ chat.name.charAt(0) }}</div>
            <div class="chat-info">
              <div class="name-time">
                <span class="name">{{ chat.name }}</span>
                <span class="time">{{ chat.lastMessage.time }}</span>
              </div>
              <div class="last-message">
                <span v-if="chat.unread" class="unread-badge">{{ chat.unread }}</span>
                {{ chat.lastMessage.text }}
              </div>
            </div>
          </div>
        </div>
      </div>

    
      <div class="chat-area">
        <div class="chat-header" :style="{ background: getActiveChat().gradient }">
          <div v-if="activeChat" class="contact-info">
            <div class="back-btn" @click="activeChat = null">
              <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                <line x1="19" y1="12" x2="5" y2="12"></line>
                <polyline points="12 19 5 12 12 5"></polyline>
              </svg>
            </div>
            <div class="avatar" :style="{ backgroundColor: getActiveChat().color }">{{ getActiveChat().name.charAt(0) }}</div>
            <div class="name-status">
              <div class="name">{{ getActiveChat().name }}</div>
              <div class="status">Online</div>
            </div>
            <div class="header-icons">
              <button class="icon-btn">
                <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                  <circle cx="12" cy="12" r="1"></circle>
                  <circle cx="12" cy="5" r="1"></circle>
                  <circle cx="12" cy="19" r="1"></circle>
                </svg>
              </button>
            </div>
          </div>
        </div>
        
        <div class="messages" ref="messagesContainer">
          <div 
            v-for="message in getActiveChat().messages" 
            :key="message.id"
            class="message"
            :class="{ 'sent': message.sent, 'received': !message.sent }"
          >
            <div class="bubble">{{ message.text }}</div>
            <div class="meta">
              <span class="time">{{ message.time }}</span>
              <span v-if="message.sent" class="status">
                <svg v-if="message.read" xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                  <path d="M18 6L7 17l-5-5"></path>
                  <path d="M22 10l-7.5 7.5L13 16"></path>
                </svg>
                <svg v-else xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                  <path d="M18 6L7 17l-5-5"></path>
                  <path d="M22 10l-7.5 7.5L13 16"></path>
                </svg>
              </span>
            </div>
          </div>
        </div>
        
        <div class="message-input">
          <button class="emoji-btn">
            <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
              <circle cx="12" cy="12" r="10"></circle>
              <path d="M8 14s1.5 2 4 2 4-2 4-2"></path>
              <line x1="9" y1="9" x2="9.01" y2="9"></line>
              <line x1="15" y1="9" x2="15.01" y2="9"></line>
            </svg>
          </button>
          <input 
            type="text" 
            v-model="newMessage" 
            placeholder="Type a message..."
            @keyup.enter="sendMessage"
          >
          <button v-if="!newMessage" class="attach-btn">
            <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
              <path d="M21.44 11.05l-9.19 9.19a6 6 0 0 1-8.49-8.49l9.19-9.19a4 4 0 0 1 5.66 5.66l-9.2 9.19a2 2 0 0 1-2.83-2.83l8.49-8.48"></path>
            </svg>
          </button>
          <button v-else @click="sendMessage" class="send-btn">
            <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
              <line x1="22" y1="2" x2="11" y2="13"></line>
              <polygon points="22 2 15 22 11 13 2 9 22 2"></polygon>
            </svg>
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted, watch } from 'vue';


const chats = ref([
  {
    id: 1,
    name: "Henry Happy",
    color: "#FF6B6B",
    gradient: "linear-gradient(135deg, #FF6B6B 0%, #FF8E8E 100%)",
    unread: 2,
    lastMessage: { text: "See you tomorrow!", time: "10:30 AM" },
    messages: [
      { id: 1, text: "Hey there!", time: "10:20 AM", sent: false, read: true },
      { id: 2, text: "Hi! How are you?", time: "10:22 AM", sent: true, read: true },
      { id: 3, text: "I'm good, thanks! How about you?", time: "10:25 AM", sent: false, read: true },
      { id: 4, text: "See you tomorrow!", time: "10:30 AM", sent: false, read: false }
    ]
  },
  {
    id: 2,
    name: "Murekezi",
    color: "#4ECDC4",
    gradient: "linear-gradient(135deg, #4ECDC4 0%, #88DAC7 100%)",
    unread: 0,
    lastMessage: { text: "The documents are ready", time: "9:15 AM" },
    messages: [
      { id: 1, text: "Hi Jane, about those files...", time: "9:00 AM", sent: true, read: true },
      { id: 2, text: "I'm working on them now", time: "9:05 AM", sent: false, read: true },
      { id: 3, text: "Great, thanks!", time: "9:10 AM", sent: true, read: true },
      { id: 4, text: "The documents are ready", time: "9:15 AM", sent: false, read: true },
      { id: 5, text: "What is it men!", time: "9:15 AM", sent: false, read: true }
    ]
  },
  {
    id: 3,
    name: "Gift",
    color: "#FFA34D",
    gradient: "linear-gradient(135deg, #FFA34D 0%, #FFC04D 100%)",
    unread: 5,
    lastMessage: { text: "Alice: Meeting at 3pm", time: "Yesterday" },
    messages: [
      { id: 1, text: "Bob: Good morning team!", time: "Yesterday", sent: false, read: true },
      { id: 2, text: "Charlie: Morning! Any updates?", time: "Yesterday", sent: false, read: true },
      { id: 3, text: "I'll send the report by noon", time: "Yesterday", sent: true, read: true },
      { id: 4, text: "Alice: Meeting at 3pm", time: "Yesterday", sent: false, read: false }
    ]
  },
  {
    id: 4,
    name: "Mom",
    color: "#A78BFA",
    gradient: "linear-gradient(135deg, #A78BFA 0%, #C4B5FD 100%)",
    unread: 0,
    lastMessage: { text: "Call me when you're free", time: "Tuesday" },
    messages: [
      { id: 1, text: "Hi sweetie, how are you?", time: "Tuesday", sent: false, read: true },
      { id: 2, text: "I'm good Mom, just busy with work", time: "Tuesday", sent: true, read: true },
      { id: 3, text: "Don't forget to eat properly", time: "Tuesday", sent: false, read: true },
      { id: 4, text: "Call me when you're free", time: "Tuesday", sent: false, read: true }
    ]
  }
]);

const activeChat = ref(1);
const newMessage = ref('');
const searchText = ref('');
const messagesContainer = ref(null);

// Filter chats based on search
const filteredChats = computed(() => {
  return chats.value.filter(chat => 
    chat.name.toLowerCase().includes(searchText.value.toLowerCase())
  );
});

// Get active chat data
function getActiveChat() {
  return chats.value.find(chat => chat.id === activeChat.value);
}

// Send new message
function sendMessage() {
  if (!newMessage.value.trim() || !activeChat.value) return;
  
  const chat = getActiveChat();
  const newMsg = {
    id: chat.messages.length + 1,
    text: newMessage.value,
    time: new Date().toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' }),
    sent: true,
    read: false
  };
  
  chat.messages.push(newMsg);
  chat.lastMessage = { text: newMessage.value, time: 'Just now' };
  newMessage.value = '';
  
  // Simulate reply after 1-3 seconds
  const delay = 1000 + Math.random() * 2000;
  setTimeout(() => {
    const replyMsg = {
      id: chat.messages.length + 1,
      text: getRandomReply(),
      time: new Date().toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' }),
      sent: false,
      read: true
    };
    chat.messages.push(replyMsg);
    chat.lastMessage = { text: replyMsg.text, time: 'Just now' };
    
    // Mark received messages as read
    chat.messages.forEach(msg => {
      if (!msg.sent) msg.read = true;
    });
    chat.unread = 0;
  }, delay);
}

// Random replies for demo
function getRandomReply() {
  const replies = [
    "Ok, sounds good!",
    "I'll get back to you soon",
    "Thanks for letting me know!",
    "Let me check and confirm",
    "Can we discuss this later?",
    "👍 Got it!",
    "That's great news!",
    "I'm on it!",
    "Perfect! 😊",
    "Let's meet tomorrow to discuss",
    "I appreciate your message!",
    "Working on it now..."
  ];
  return replies[Math.floor(Math.random() * replies.length)];
}

// Auto-scroll to bottom of messages
watch(() => getActiveChat()?.messages, () => {
  setTimeout(() => {
    if (messagesContainer.value) {
      messagesContainer.value.scrollTop = messagesContainer.value.scrollHeight;
    }
  }, 50);
}, { deep: true });
</script>

<style>
:root {
  --primary: #6C63FF;
  --secondary: #4ECDC4;
  --accent: #FF6B6B;
  --background: #F8F9FA;
  --text: #2D3436;
  --text-light: #636E72;
  --white: #FFFFFF;
  --gray-light: #DFE6E9;
  --shadow: 0 4px 20px rgba(0, 0, 0, 0.08);
}

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: 'Segoe UI', 'Helvetica Neue', sans-serif;
  color: var(--text);
}

.whatsapp-app {
  height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  background-color: var(--background);
}

.chat-container {
  display: flex;
  width: 900px;
  height: 700px;
  background-color: var(--white);
  box-shadow: var(--shadow);
  border-radius: 16px;
  overflow: hidden;
}

/* Sidebar styles */
.sidebar {
  width: 35%;
  border-right: 1px solid var(--gray-light);
  display: flex;
  flex-direction: column;
  background-color: var(--white);
}

.header {
  padding: 20px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  background-color: var(--white);
}

.user-profile {
  display: flex;
  align-items: center;
  gap: 12px;
}

.header h2 {
  font-size: 20px;
  font-weight: 600;
  color: var(--text);
}

.header-icons {
  display: flex;
  gap: 12px;
}

.icon-btn {
  background: none;
  border: none;
  cursor: pointer;
  color: var(--text-light);
  transition: all 0.2s;
  padding: 4px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
}

.icon-btn:hover {
  background-color: rgba(0, 0, 0, 0.05);
  color: var(--text);
}

.search {
  padding: 12px 20px;
  background-color: var(--white);
}

.search-container {
  position: relative;
  display: flex;
  align-items: center;
}

.search-container svg {
  position: absolute;
  left: 12px;
  color: var(--text-light);
}

.search input {
  width: 100%;
  padding: 10px 12px 10px 36px;
  border: none;
  border-radius: 8px;
  outline: none;
  background-color: #F1F3F4;
  font-size: 14px;
  transition: all 0.2s;
}

.search input:focus {
  background-color: var(--white);
  box-shadow: 0 0 0 2px var(--primary);
}

.chat-list {
  flex: 1;
  overflow-y: auto;
}

.chat-item {
  display: flex;
  padding: 14px 20px;
  cursor: pointer;
  transition: all 0.2s;
  position: relative;
}

.chat-item:hover {
  background-color: #F8F9FA;
}

.chat-item.active {
  background-color: #EFF3F4;
}

.chat-item.active::after {
  content: '';
  position: absolute;
  left: 0;
  top: 0;
  height: 100%;
  width: 3px;
  background-color: var(--primary);
}

.avatar {
  width: 48px;
  height: 48px;
  border-radius: 50%;
  color: white;
  display: flex;
  justify-content: center;
  align-items: center;
  margin-right: 12px;
  font-weight: bold;
  font-size: 18px;
  flex-shrink: 0;
}

.chat-info {
  flex: 1;
  min-width: 0;
}

.name-time {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 4px;
}

.name {
  font-weight: 600;
  font-size: 15px;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

.time {
  font-size: 12px;
  color: var(--text-light);
  flex-shrink: 0;
  margin-left: 8px;
}

.last-message {
  font-size: 13px;
  color: var(--text-light);
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
  display: flex;
  align-items: center;
}

.unread-badge {
  background-color: var(--primary);
  color: white;
  border-radius: 50%;
  width: 18px;
  height: 18px;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 10px;
  margin-right: 6px;
  flex-shrink: 0;
}

/* Chat area styles */
.chat-area {
  width: 65%;
  display: flex;
  flex-direction: column;
  background-color: #E5DDD5;
  background-image: url('data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAABQAAAAUCAYAAACNiR0NAAAABmJLR0QA/wD/AP+gvaeTAAAACXBIWXMAAAsTAAALEwEAmpwYAAAAB3RJTUUH4AkEEjIZJ3L1JQAAAB1pVFh0Q29tbWVudAAAAAAAQ3JlYXRlZCB3aXRoIEdJTVBkLmUHAAAALUlEQVQ4y2NgGAXDADDCGEuWLPlPqQH/QcwwBooNHDVk1JBRQ0YNITRgQDEAALQzA5F6NQ8ZAAAAAElFTkSuQmCC');
}

.chat-header {
  padding: 16px 20px;
  display: flex;
  align-items: center;
  color: white;
  transition: all 0.3s;
}

.back-btn {
  display: none;
  margin-right: 12px;
  cursor: pointer;
}

.contact-info {
  display: flex;
  align-items: center;
  flex: 1;
}

.name-status {
  margin-left: 12px;
  flex: 1;
}

.name {
  font-weight: 600;
  font-size: 16px;
}

.status {
  font-size: 12px;
  opacity: 0.9;
}

.messages {
  flex: 1;
  padding: 20px;
  overflow-y: auto;
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.message {
  display: flex;
  flex-direction: column;
  max-width: 70%;
  animation: fadeIn 0.3s ease-out;
}

@keyframes fadeIn {
  from { opacity: 0; transform: translateY(10px); }
  to { opacity: 1; transform: translateY(0); }
}

.message.sent {
  align-self: flex-end;
  align-items: flex-end;
}

.message.received {
  align-self: flex-start;
  align-items: flex-start;
}

.bubble {
  padding: 12px 16px;
  border-radius: 18px;
  margin-bottom: 4px;
  line-height: 1.4;
  font-size: 15px;
  word-wrap: break-word;
}

.sent .bubble {
  background-color: #DCF8C6;
  border-bottom-right-radius: 4px;
  color: #000;
}

.received .bubble {
  background-color: white;
  border-bottom-left-radius: 4px;
}

.meta {
  display: flex;
  align-items: center;
  gap: 4px;
}

.time {
  font-size: 11px;
  color: rgba(0, 0, 0, 0.45);
}

.sent .time {
  color: rgba(0, 0, 0, 0.45);
}

.received .time {
  color: rgba(255, 255, 255, 0.7);
}

.status {
  display: flex;
}

.status svg {
  width: 14px;
  height: 14px;
}

.message-input {
  padding: 12px 20px;
  display: flex;
  align-items: center;
  background-color: black;
  gap: 8px;
}

.message-input input {
  flex: 1;
  padding: 12px 16px;
  border: none;
  border-radius: 24px;
  outline: none;
  font-size: 15px;
  background-color: white;
  transition: all 0.2s;
}

.message-input input:focus {
  box-shadow: 0 0 0 2px var(--primary);
}

.emoji-btn, .attach-btn, .send-btn {
  background: none;
  border: none;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  color: var(--text-light);
  transition: all 0.2s;
  padding: 8px;
  border-radius: 50%;
}

.emoji-btn:hover, .attach-btn:hover {
  background-color: rgba(0, 0, 0, 0.05);
  color: var(--text);
}

.send-btn {
  background-color: var(--primary);
  color: white;
}

.send-btn:hover {
  background-color: #5A52E0;
}

/* Responsive adjustments */
@media (max-width: 768px) {
  .chat-container {
    width: 100%;
    height: 100vh;
    border-radius: 0;
  }
  
  .sidebar {
    width: 100%;
    display: block;
  }
  
  .chat-area {
    width: 100%;
    display: none;
  }
  
  .chat-area.active {
    display: flex;
  }
  
  .back-btn {
    display: block;
  }
}

/* Scrollbar styling */
::-webkit-scrollbar {
  width: 6px;
}

::-webkit-scrollbar-track {
  background: rgba(0, 0, 0, 0.05);
}

::-webkit-scrollbar-thumb {
  background: rgba(0, 0, 0, 0.2);
  border-radius: 3px;
}

::-webkit-scrollbar-thumb:hover {
  background: rgba(0, 0, 0, 0.3);
}
</style>