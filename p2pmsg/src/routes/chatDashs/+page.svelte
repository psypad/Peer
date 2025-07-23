<script lang="ts">
    // This is where you'd put your frontend TypeScript.
    // Note: Browsers run JavaScript. This code needs to be compiled from TS to JS.
    // For development, you can use a tool like `ts-node` or the `tsc` compiler.

    // Define a type for the message payload for better type safety


    // API Call Stub:
    // fetch('/api/send_message', {
    //     method: 'POST',
    //     headers: { 'Content-Type': 'application/json' },
    //     body: JSON.stringify(payload)
    // }).then(response => response.json()).then(data => {
    //     console.log('Message sent:', data);
    //     // Add the new message to the UI
    // });\

    import { onMount } from 'svelte';
    import DashboardHeader from '../../components/Dashboard-Header.svelte';

    // Type definitions
    interface Message {
        id: string;
        content: string;
        sender: 'user' | 'bot';
        timestamp: Date;
        chatId: string;
    }

    interface SendMessagePayload {
        chatId: string;
        content: string;
        sender: string;
    }

    interface Chat {
        id: string;
        name: string;
        lastMessage: string;
        timestamp: string;
        unreadCount?: number;
        avatar: string;
        avatarColor?: string;
    }

    // State variables
    let messages: Message[] = [];
    let chats: Chat[] = [];
    let currentChatId = 'ghost-chat';
    let messageInput = '';

    // Dummy data for testing
    const dummyChats: Chat[] = [
        {
            id: 'ghost-chat',
            name: 'Ghost',
            lastMessage: 'Draft: g',
            timestamp: '02:55 PM',
            avatar: 'G',
            avatarColor: '#6a81ff'
        },
        {
            id: 'peer-chat',
            name: 'Peer',
            lastMessage: 'Login code: ... Do not give this...',
            timestamp: '02:53 PM',
            unreadCount: 1,
            avatar: 'P',
            avatarColor: '#3498db'
        },
        {
            id: 'aiden-chat',
            name: 'Aiden',
            lastMessage: 'Hi! How can I assist you today?',
            timestamp: 'Jul 11',
            avatar: 'A',
            avatarColor: '#e84393'
        }
    ];

    const dummyMessages: Message[] = [
        {
            id: '1',
            content: 'What can this bot do?',
            sender: 'bot',
            timestamp: new Date('2022-09-21T20:20:00'),
            chatId: 'ghost-chat'
        },
        {
            id: '2',
            content: '/start',
            sender: 'user',
            timestamp: new Date('2022-09-21T20:22:00'),
            chatId: 'ghost-chat'
        }
    ];

    // API Functions
    async function sendMessage(payload: SendMessagePayload): Promise<any> {
        try {
            const response = await fetch('/api/send_message', {
                method: 'POST',
                headers: { 'Content-Type': 'application/json' },
                body: JSON.stringify(payload)
            });
            
            if (!response.ok) {
                throw new Error(`HTTP error! status: ${response.status}`);
            }
            
            const data = await response.json();
            console.log('Message sent:', data);
            return data;
        } catch (error) {
            console.error('Error sending message:', error);
            // For now, return dummy success response
            return { 
                success: true, 
                messageId: Date.now().toString(),
                timestamp: new Date().toISOString()
            };
        }
    }

    async function fetchMessages(chatId: string): Promise<Message[]> {
        try {
            const response = await fetch(`/api/messages/${chatId}`);
            if (!response.ok) {
                throw new Error(`HTTP error! status: ${response.status}`);
            }
            return await response.json();
        } catch (error) {
            console.error('Error fetching messages:', error);
            // Return dummy messages for now
            return dummyMessages.filter(msg => msg.chatId === chatId);
        }
    }

    async function fetchChats(): Promise<Chat[]> {
        try {
            const response = await fetch('/api/chats');
            if (!response.ok) {
                throw new Error(`HTTP error! status: ${response.status}`);
            }
            return await response.json();
        } catch (error) {
            console.error('Error fetching chats:', error);
            // Return dummy chats for now
            return dummyChats;
        }
    }

    // Event handlers
    async function handleSendMessage(event: Event) {
        event.preventDefault();
        
        if (!messageInput.trim()) return;

        const payload: SendMessagePayload = {
            chatId: currentChatId,
            content: messageInput.trim(),
            sender: 'user'
        };

        // Add message to local state immediately for better UX
        const newMessage: Message = {
            id: Date.now().toString(),
            content: payload.content,
            sender: 'user',
            timestamp: new Date(),
            chatId: currentChatId
        };

        messages = [...messages, newMessage];
        messageInput = '';

        // Send to backend
        try {
            const result = await sendMessage(payload);
            
            // Update message with server response if needed
            if (result.messageId) {
                messages = messages.map(msg => 
                    msg.id === newMessage.id 
                        ? { ...msg, id: result.messageId }
                        : msg
                );
            }

            // Simulate bot response after a delay
            setTimeout(() => {
                const botResponse: Message = {
                    id: (Date.now() + 1).toString(),
                    content: `Echo: ${payload.content}`,
                    sender: 'bot',
                    timestamp: new Date(),
                    chatId: currentChatId
                };
                messages = [...messages, botResponse];
            }, 1000);

        } catch (error) {
            console.error('Failed to send message:', error);
        }
    }

    function handleKeyPress(event: KeyboardEvent) {
        if (event.key === 'Enter' && !event.shiftKey) {
            event.preventDefault();
            handleSendMessage(event);
        }
    }

    function switchChat(chatId: string) {
        currentChatId = chatId;
        loadMessages(chatId);
    }

    async function loadMessages(chatId: string) {
        try {
            messages = await fetchMessages(chatId);
        } catch (error) {
            console.error('Failed to load messages:', error);
        }
    }

    function formatTime(date: Date): string {
        return date.toLocaleTimeString('en-US', { 
            hour: 'numeric', 
            minute: '2-digit',
            hour12: true 
        });
    }

    function formatDate(date: Date): string {
        return date.toLocaleDateString('en-US', { 
            year: 'numeric', 
            month: 'long', 
            day: 'numeric' 
        });
    }

    // Initialize data when component mounts
    onMount(async () => {
        try {
            chats = await fetchChats();
            if (chats.length > 0) {
                currentChatId = chats[0].id;
                await loadMessages(currentChatId);
            }
        } catch (error) {
            console.error('Failed to initialize chat data:', error);
            // Use dummy data as fallback
            chats = dummyChats;
            messages = dummyMessages.filter(msg => msg.chatId === currentChatId);
        }
    });

    // Reactive statements
    $: currentChat = chats.find(chat => chat.id === currentChatId);
    $: currentMessages = messages.filter(msg => msg.chatId === currentChatId);
</script>

<div class="app-container">
    <!-- Left Panel: Chat List -->
    <aside class="sidebar">
        <div class="sidebar-header">
            <button class="icon-button"><i class="fas fa-bars"></i></button>
            <div class="search-bar">
                <i class="fas fa-search"></i>
                <input type="text" placeholder="Search">
            </div>
        </div>

        <div class="chat-list">
            {#each chats as chat (chat.id)}
                <div 
                    class="chat-item {currentChatId === chat.id ? 'active' : ''}"
                    on:click={() => switchChat(chat.id)}
                    role="button"
                    tabindex="0"
                    on:keydown={(e) => e.key === 'Enter' && switchChat(chat.id)}
                >
                    {#if chat.id === 'ghost-chat'}
                        <div class="avatar" style="background-color: #6a81ff;">G</div>
                    {:else if chat.id === 'peer-chat'}
                        <img src="https://placehold.co/40x40/3498db/ffffff?text=P" alt="Peer" class="avatar">
                    {:else if chat.id === 'aiden-chat'}
                        <div class="avatar" style="background-color: #e84393;">A</div>
                    {/if}
                    <div class="chat-details">
                        <div class="chat-name">{chat.name}</div>
                        <div class="chat-message">
                            {#if chat.id === 'ghost-chat'}
                                <span class="draft">Draft:</span> {chat.lastMessage.replace('Draft: ', '')}
                            {:else}
                                {chat.lastMessage}
                            {/if}
                        </div>
                    </div>
                    <div class="chat-meta">
                        <span class="chat-time">{chat.timestamp}</span>
                        {#if chat.unreadCount}
                            <span class="unread-badge">{chat.unreadCount}</span>
                        {/if}
                    </div>
                </div>
            {/each}
        </div>
    </aside>

    <!-- Right Panel: Chat View -->
    <main class="chat-view">
        <header class="chat-header">
            {#if currentChat}
                <div class="chat-info">
                    {#if currentChat.id === 'ghost-chat'}
                        <div class="avatar" style="background-color: #6a81ff;">G</div>
                    {:else if currentChat.id === 'peer-chat'}
                        <img src="https://placehold.co/40x40/3498db/ffffff?text=P" alt="Peer" class="avatar">
                    {:else if currentChat.id === 'aiden-chat'}
                        <div class="avatar" style="background-color: #e84393;">A</div>
                    {/if}
                    <div class="chat-identity">
                        <div class="chat-name">{currentChat.name}</div>
                        <div class="chat-status">bot</div>
                    </div>
                </div>
            {/if}
            <div class="chat-actions">
                <button class="icon-button"><i class="fas fa-search"></i></button>
                <button class="icon-button"><i class="fas fa-ellipsis-v"></i></button>
            </div>
        </header>

        <div class="message-container">
            {#if currentMessages.length > 0}
                <div class="message-day-separator">
                    <span>{formatDate(currentMessages[0].timestamp)}</span>
                </div>

                {#each currentMessages as message (message.id)}
                    {#if message.sender === 'bot'}
                        <div class="message-group received">
                            <div class="message-card">
                                <div class="message-content">{message.content}</div>
                                <div class="message-subtext">Personal assistant</div>
                            </div>
                        </div>
                    {:else}
                        <div class="message-group sent">
                            <div class="message">
                                <div class="message-content">{message.content}</div>
                                <div class="message-time">
                                    {formatTime(message.timestamp)} <i class="fas fa-check-double"></i>
                                </div>
                            </div>
                        </div>
                    {/if}
                {/each}
            {:else}
                <div class="message-day-separator">
                    <span>No messages yet</span>
                </div>
            {/if}
        </div>

        <footer class="chat-footer">
            <form id="message-form" class="message-form" on:submit={handleSendMessage}>
                <button type="button" class="icon-button"><i class="fas fa-paperclip"></i></button>
                <input 
                    type="text" 
                    class="message-input" 
                    placeholder="Message"
                    bind:value={messageInput}
                    on:keypress={handleKeyPress}
                >
                <button type="button" class="icon-button"><i class="far fa-grin"></i></button>
                <button type="submit" class="icon-button send-button">
                    <i class="fas fa-paper-plane"></i>
                </button>
            </form>
        </footer>
    </main>
</div>


<style>
    :root {
        --background-dark: #0e1621;
        --background-medium: #17212b;
        --background-light: #242f3d;
        --text-primary: #ffffff;
        --text-secondary: #a3a7ac;
        --text-tertiary: #6f7478;
        --accent-blue: #3390ec;
        --accent-blue-light: #529fde;
        --accent-green-sent: #2b5278;
        --active-chat-bg: #2b5278;
        --draft-color: #dd8853;
        --font-family: 'Inter', -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
    }

    * {
        box-sizing: border-box;
        margin: 0;
        padding: 0;
    }

    body {
        font-family: var(--font-family);
        background-color: var(--background-dark);
        color: var(--text-primary);
        overflow: hidden;
    }

    .app-container {
        display: flex;
        height: 100vh;
    }

    /* --- Sidebar (Chat List) --- */
    .sidebar {
        width: 350px;
        background-color: var(--background-medium);
        border-right: 1px solid var(--background-light);
        display: flex;
        flex-direction: column;
        flex-shrink: 0;
    }

    .sidebar-header {
        display: flex;
        align-items: center;
        padding: 10px 15px;
        background-color: var(--background-medium);
    }

    .icon-button {
        background: none;
        border: none;
        color: var(--text-secondary);
        font-size: 20px;
        cursor: pointer;
        padding: 5px;
    }

    .search-bar {
        flex-grow: 1;
        margin-left: 20px;
        background-color: var(--background-light);
        border-radius: 20px;
        padding: 8px 15px;
        display: flex;
        align-items: center;
    }

    .search-bar i {
        color: var(--text-tertiary);
        font-size: 14px;
    }

    .search-bar input {
        background: none;
        border: none;
        outline: none;
        color: var(--text-primary);
        margin-left: 15px;
        width: 100%;
        font-size: 15px;
    }

    .chat-list {
        overflow-y: auto;
        flex-grow: 1;
    }

    .chat-item {
        display: flex;
        align-items: center;
        padding: 10px 15px;
        cursor: pointer;
        border-bottom: 1px solid var(--background-light);
    }

    .chat-item:hover {
        background-color: var(--background-light);
    }

    .chat-item.active {
        background-color: var(--active-chat-bg);
    }

    .avatar {
        width: 50px;
        height: 50px;
        border-radius: 50%;
        display: flex;
        align-items: center;
        justify-content: center;
        font-size: 20px;
        font-weight: 600;
        margin-right: 15px;
        flex-shrink: 0;
    }

    .chat-details {
        flex-grow: 1;
        overflow: hidden;
    }

    .chat-name {
        font-weight: 600;
        font-size: 16px;
    }

    .chat-message {
        color: var(--text-secondary);
        font-size: 14px;
        white-space: nowrap;
        overflow: hidden;
        text-overflow: ellipsis;
    }

    .draft {
        color: var(--draft-color);
    }

    .chat-meta {
        display: flex;
        flex-direction: column;
        align-items: flex-end;
        font-size: 12px;
        color: var(--text-secondary);
    }

    .unread-badge {
        background-color: var(--accent-blue);
        color: white;
        border-radius: 50%;
        padding: 2px 7px;
        font-size: 12px;
        font-weight: 600;
        margin-top: 5px;
    }

    /* --- Chat View (Messages) --- */
    .chat-view {
        flex-grow: 1;
        display: flex;
        flex-direction: column;
        background-color: var(--background-dark);
    }

    .chat-header {
        display: flex;
        justify-content: space-between;
        align-items: center;
        padding: 10px 20px;
        background-color: var(--background-medium);
        border-bottom: 1px solid var(--background-light);
    }

    .chat-info {
        display: flex;
        align-items: center;
    }

    .chat-info .avatar {
        width: 40px;
        height: 40px;
        font-size: 16px;
    }

    .chat-identity {
        margin-left: 15px;
    }

    .chat-identity .chat-name {
        font-size: 16px;
    }

    .chat-identity .chat-status {
        font-size: 13px;
        color: var(--text-secondary);
    }

    .chat-actions {
        display: flex;
        gap: 15px;
    }

    .message-container {
        flex-grow: 1;
        overflow-y: auto;
        padding: 20px;
        display: flex;
        flex-direction: column;
    }

    .message-day-separator {
        text-align: center;
        margin: 20px 0;
    }

    .message-day-separator span {
        background-color: var(--background-light);
        color: var(--text-primary);
        padding: 5px 15px;
        border-radius: 20px;
        font-size: 13px;
    }

    .message-group {
        display: flex;
        margin-bottom: 10px;
        max-width: 60%;
    }

    .message-group.sent {
        align-self: flex-end;
        flex-direction: row-reverse;
    }

    .message-group.received {
        align-self: flex-start;
    }

    .message, .message-card {
        padding: 10px 15px;
        border-radius: 15px;
        position: relative;
    }

    .message-group.sent .message {
        background-color: var(--accent-green-sent);
        border-bottom-right-radius: 5px;
    }

    .message-group.received .message-card {
        background-color: var(--background-medium);
        border-bottom-left-radius: 5px;
    }

    .message-content {
        font-size: 15px;
        line-height: 1.4;
    }

    .message-subtext {
        font-size: 14px;
        color: var(--text-secondary);
        margin-top: 5px;
    }

    .message-time {
        font-size: 12px;
        color: var(--text-secondary);
        float: right;
        margin-left: 10px;
        margin-top: 5px;
    }

    .message-time .fa-check-double {
        color: var(--accent-blue-light);
    }

    .chat-footer {
        padding: 10px 20px;
        background-color: var(--background-medium);
        border-top: 1px solid var(--background-light);
    }

    .message-form {
        display: flex;
        align-items: center;
        background-color: var(--background-light);
        border-radius: 25px;
        padding: 5px;
    }

    .message-input {
        flex-grow: 1;
        background: none;
        border: none;
        outline: none;
        color: var(--text-primary);
        font-size: 16px;
        padding: 10px;
    }

    .message-form .icon-button {
        padding: 10px;
    }
</style>