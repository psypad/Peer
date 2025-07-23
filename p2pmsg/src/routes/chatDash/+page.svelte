<!-- +page.svelte -->
<script>
  import { createTabs, melt } from '@melt-ui/svelte';

  // Sample data
  let users = [
    { id: 1, name: 'Alice Johnson', status: 'online', lastSeen: 'now' },
    { id: 2, name: 'Bob Smith', status: 'offline', lastSeen: '2 hours ago' },
    { id: 3, name: 'Charlie Brown', status: 'online', lastSeen: 'now' }
  ];

  let groups = [
    { id: 1, name: 'Team Alpha', members: 5, lastMessage: 'Hey everyone!' },
    { id: 2, name: 'Project Beta', members: 3, lastMessage: 'Meeting at 3 PM' },
    { id: 3, name: 'Random Chat', members: 12, lastMessage: 'LOL 😂' }
  ];

  let selectedChat = null;
  let messages = [];

  // Melt UI tabs for switching between users and groups
  const {
    elements: { root, list, content, trigger },
    states: { value }
  } = createTabs({
    defaultValue: 'users'
  });

  function selectChat(chat, type) {
    selectedChat = { ...chat, type };
    // Load messages for selected chat (mock data)
    messages = [
      { id: 1, sender: 'Alice Johnson', content: 'Hello there!', timestamp: '10:30 AM', own: false },
      { id: 2, sender: 'You', content: 'Hi! How are you?', timestamp: '10:31 AM', own: true },
      { id: 3, sender: 'Alice Johnson', content: 'I am doing great, thanks!', timestamp: '10:32 AM', own: false }
    ];
  }

  function sendMessage(event) {
    const input = event.target.querySelector('input');
    if (input.value.trim() && selectedChat) {
      const newMessage = {
        id: messages.length + 1,
        sender: 'You',
        content: input.value.trim(),
        timestamp: new Date().toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' }),
        own: true
      };
      messages = [...messages, newMessage];
      input.value = '';
    }
  }
</script>

<main class="chat-app">
  <!-- Sidebar -->
  <aside class="sidebar">
    <div class="sidebar-header">
      <h2>ChatDash</h2>
    </div>
    
    <div class="tabs" use:melt={$root}>
      <div class="tab-list" use:melt={$list}>
        <button class="tab-trigger" use:melt={$trigger('users')}>
          Users
        </button>
        <button class="tab-trigger" use:melt={$trigger('groups')}>
          Groups
        </button>
      </div>

      <div class="tab-content" use:melt={$content('users')}>
        <div class="chat-list">
          {#each users as user}
            <button 
              class="chat-item" 
              class:active={selectedChat?.id === user.id && selectedChat?.type === 'user'}
              on:click={() => selectChat(user, 'user')}
            >
              <div class="avatar">
                <div class="status-indicator" class:online={user.status === 'online'}></div>
                {user.name.charAt(0)}
              </div>
              <div class="chat-info">
                <h4>{user.name}</h4>
                <p class="last-seen">{user.lastSeen}</p>
              </div>
            </button>
          {/each}
        </div>
      </div>

      <div class="tab-content" use:melt={$content('groups')}>
        <div class="chat-list">
          {#each groups as group}
            <button 
              class="chat-item"
              class:active={selectedChat?.id === group.id && selectedChat?.type === 'group'}
              on:click={() => selectChat(group, 'group')}
            >
              <div class="avatar group-avatar">
                #
              </div>
              <div class="chat-info">
                <h4>{group.name}</h4>
                <p class="members">{group.members} members</p>
                <p class="last-message">{group.lastMessage}</p>
              </div>
            </button>
          {/each}
        </div>
      </div>
    </div>
  </aside>

  <!-- Main Chat Area -->
  <section class="chat-main">
    {#if selectedChat}
      <header class="chat-header">
        <div class="chat-title">
          <h3>{selectedChat.name}</h3>
          <p class="chat-subtitle">
            {#if selectedChat.type === 'user'}
              {selectedChat.status}
            {:else}
              {selectedChat.members} members
            {/if}
          </p>
        </div>
      </header>

      <div class="messages-container">
        <div class="messages">
          {#each messages as message}
            <div class="message" class:own={message.own}>
              <div class="message-content">
                {#if !message.own}
                  <span class="sender">{message.sender}</span>
                {/if}
                <p>{message.content}</p>
                <span class="timestamp">{message.timestamp}</span>
              </div>
            </div>
          {/each}
        </div>
      </div>

      <form class="message-input" on:submit|preventDefault={sendMessage}>
        <input 
          type="text" 
          placeholder="Type a message..." 
          class="input-field"
        />
        <button type="submit" class="send-button">Send</button>
      </form>
    {:else}
      <div class="empty-state">
        <h3>Select a chat to start messaging</h3>
        <p>Choose a user or group from the sidebar to begin your conversation.</p>
      </div>
    {/if}
  </section>
</main>

<style>
  .chat-app {
    display: flex;
    height: 100vh;
    font-family: 'Segoe UI', system-ui, sans-serif;
    background: #f5f5f5;
  }

  /* Sidebar Styles */
  .sidebar {
    width: 320px;
    background: white;
    border-right: 1px solid #e0e0e0;
    display: flex;
    flex-direction: column;
  }

  .sidebar-header {
    padding: 20px;
    border-bottom: 1px solid #e0e0e0;
    background: #fafafa;
  }

  .sidebar-header h2 {
    margin: 0;
    color: #333;
    font-size: 1.5rem;
  }

  /* Tabs */
  .tabs {
    flex: 1;
    display: flex;
    flex-direction: column;
  }

  .tab-list {
    display: flex;
    background: #fafafa;
    border-bottom: 1px solid #e0e0e0;
  }

  .tab-trigger {
    flex: 1;
    padding: 12px 16px;
    border: none;
    background: transparent;
    cursor: pointer;
    font-weight: 500;
    color: #666;
    transition: all 0.2s ease;
  }

  .tab-trigger[data-state="active"] {
    color: #007bff;
    background: white;
    border-bottom: 2px solid #007bff;
  }

  .tab-content {
    flex: 1;
    overflow-y: auto;
  }

  /* Chat List */
  .chat-list {
    padding: 8px 0;
  }

  .chat-item {
    width: 100%;
    padding: 12px 16px;
    border: none;
    background: transparent;
    cursor: pointer;
    display: flex;
    align-items: center;
    gap: 12px;
    text-align: left;
    transition: background-color 0.2s ease;
  }

  .chat-item:hover {
    background: #f8f9fa;
  }

  .chat-item.active {
    background: #e3f2fd;
    border-right: 3px solid #007bff;
  }

  .avatar {
    width: 40px;
    height: 40px;
    border-radius: 50%;
    background: #007bff;
    color: white;
    display: flex;
    align-items: center;
    justify-content: center;
    font-weight: 600;
    position: relative;
    flex-shrink: 0;
  }

  .group-avatar {
    background: #28a745;
  }

  .status-indicator {
    width: 12px;
    height: 12px;
    border-radius: 50%;
    background: #dc3545;
    position: absolute;
    bottom: -2px;
    right: -2px;
    border: 2px solid white;
  }

  .status-indicator.online {
    background: #28a745;
  }

  .chat-info {
    flex: 1;
    min-width: 0;
  }

  .chat-info h4 {
    margin: 0 0 4px 0;
    font-size: 0.95rem;
    font-weight: 600;
    color: #333;
  }

  .last-seen, .members, .last-message {
    margin: 0;
    font-size: 0.85rem;
    color: #666;
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
  }

  /* Main Chat Area */
  .chat-main {
    flex: 1;
    display: flex;
    flex-direction: column;
    background: white;
  }

  .chat-header {
    padding: 16px 24px;
    border-bottom: 1px solid #e0e0e0;
    background: #fafafa;
  }

  .chat-title h3 {
    margin: 0 0 4px 0;
    color: #333;
    font-size: 1.1rem;
  }

  .chat-subtitle {
    margin: 0;
    font-size: 0.9rem;
    color: #666;
  }

  .messages-container {
    flex: 1;
    overflow-y: auto;
    padding: 16px;
  }

  .messages {
    display: flex;
    flex-direction: column;
    gap: 12px;
  }

  .message {
    display: flex;
  }

  .message.own {
    justify-content: flex-end;
  }

  .message-content {
    max-width: 70%;
    padding: 8px 12px;
    border-radius: 12px;
    background: black;
  }

  .message.own .message-content {
    background: #007bff;
    color: black;
  }

  .sender {
    font-size: 0.8rem;
    font-weight: 600;
    color: #007bff;
    display: block;
    margin-bottom: 4px;
  }

  .message.own .sender {
    color: #333;
  }

  .message-content p {
    margin: 0 0 4px 0;
    line-height: 1.4;
  }

  .timestamp {
    font-size: 0.75rem;
    opacity: 0.7;
  }

  /* Message Input */
  .message-input {
    display: flex;
    gap: 8px;
    padding: 16px;
    border-top: 1px solid #e0e0e0;
    background: #fafafa;
  }

  .input-field {
    flex: 1;
    padding: 12px 16px;
    border: 1px solid #ddd;
    border-radius: 24px;
    outline: none;
    font-size: 0.95rem;
  }

  .input-field:focus {
    border-color: #007bff;
  }

  .send-button {
    padding: 12px 24px;
    background: #007bff;
    color: white;
    border: none;
    border-radius: 24px;
    cursor: pointer;
    font-weight: 500;
    transition: background-color 0.2s ease;
  }

  .send-button:hover {
    background: #0056b3;
  }

  /* Empty State */
  .empty-state {
    flex: 1;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    text-align: center;
    color: #666;
  }

  .empty-state h3 {
    margin: 0 0 8px 0;
    color: #333;
  }

  .empty-state p {
    margin: 0;
    font-size: 0.9rem;
  }

  /* Responsive */
  @media (max-width: 768px) {
    .sidebar {
      width: 280px;
    }
    
    .message-content {
      max-width: 85%;
    }
  }
</style>
