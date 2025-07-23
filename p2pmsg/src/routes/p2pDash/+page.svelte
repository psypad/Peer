<script>
    import P2pHeader from "../../components/p2p-header.svelte";
</script>

<div class="app-container">
    <P2pHeader />

    <main class="main-content">
        <aside class="sidebar">
            <div class="sidebar-section">
                <h3>STATUS</h3>
                <ul>
                    <li>All (95)</li>
                    <li>Downloading (0)</li>
                    <li>Seeding (1)</li>
                    <li>Completed (95)</li>
                    <li>Resumed (0)</li>
                    <li>Paused (0)</li>
                    <li>Active (1)</li>
                    <li>Inactive (94)</li>
                    <li>Stalled (0)</li>
                    <li>Stalled Uploading (0)</li>
                    <li>Stalled Downloading (0)</li>
                    <li>Checking (0)</li>
                    <li>Errored (0)</li>
                </ul>
            </div>
            <div class="sidebar-section">
                <h3>CATEGORIES</h3>
                <ul>
                    <li>No category</li>
                </ul>
            </div>
            <div class="sidebar-section">
                <h3>TAGS</h3>
                    <ul>
                        <li>No tags</li>
                </ul>
            </div>
            <div class="sidebar-section">
                <h3>TRACKERS</h3>
                <ul>
                    <li>(No specific tracker)</li>
                </ul>
            </div>
        </aside>

        <section class="content-area">
            <div class="torrent-list">
                <table>
                    <thead>
                        <tr>
                            <th><input type="checkbox" checked></th>
                            <th>Name</th>
                            <th>Size</th>
                            <th>Progress</th>
                            <th>Status</th>
                            <th>Seeds</th>
                            <th>Peers</th>
                            <th>Down Speed</th>
                            <th>Up Speed</th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr class="selected">
                            <td><input type="checkbox" checked></td>
                            <td>kali-linux-2025.2-installer-amd64.iso</td>
                            <td>4.17 GiB</td>
                            <td>
                                <div class="progress-bar-container">
                                    <div class="progress-bar" style="width: 100%;"></div>
                                    <span>100%</span>
                                </div>
                            </td>
                            <td>Completed</td>
                            <td>0 (0)</td>
                            <td>0 (95)</td>
                            <td>0 B/s</td>
                            <td>0 B/s</td>
                        </tr>
                            </tbody>
                </table>
            </div>

            <div class="details-panel">
                <div class="details-header">
                    <span>Progress:</span>
                    <div class="main-progress-bar-container">
                        <div class="main-progress-bar" style="width: 100%;"></div>
                        <span>100.0%</span>
                    </div>
                </div>
                <div class="details-tabs">
                    <button class="tab-button active">General</button>
                    <button class="tab-button">Trackers</button>
                    <button class="tab-button">Peers</button>
                    <button class="tab-button">HTTP Sources</button>
                    <button class="tab-button">Content</button>
                </div>
                <div class="details-content">
                    <div class="transfer-info">
                        <h4>Transfer</h4>
                        <div class="info-grid">
                            <div><span class="label">Time Active:</span> 1h 28m (seeded for 14m)</div>
                            <div><span class="label">ETA:</span> ∞</div>
                            <div><span class="label">Downloaded:</span> 4.23 GiB (0 B this session)</div>
                            <div><span class="label">Uploaded:</span> 513.4 KiB (0 B this session)</div>
                            <div><span class="label">Download Speed:</span> 0 B/s (996.3 KiB/s avg.)</div>
                            <div><span class="label">Upload Speed:</span> 0 B/s (98 B/s avg.)</div>
                            <div><span class="label">Connections:</span> 0 (100 max)</div>
                            <div><span class="label">Seeds:</span> 0 (0 total)</div>
                            <div><span class="label">Download Limit:</span> ∞</div>
                            <div><span class="label">Upload Limit:</span> ∞</div>
                            <div><span class="label">Peers:</span> 0 (95 total)</div>
                            <div><span class="label">Wasted:</span> 0 B</div>
                            <div><span class="label">Share Ratio:</span> 0.00</div>
                            <div><span class="label">Reannounce In:</span> 0</div>
                            <div><span class="label">Last Seen Complete:</span> 12-07-2025 18:58</div>
                            <div><span class="label">Popularity:</span> 0.05</div>
                        </div>
                    </div>
                </div>
            </div>
        </section>
    </main>

    <footer class="status-bar">
        <div class="status-left">
            <span>DHT: 0 nodes</span>
        </div>
        <div class="status-right">
            <i class="fas fa-globe"></i>
            <i class="fas fa-arrow-down"></i> 0 B/s (0 B)
            <i class="fas fa-arrow-up"></i> 0 B/s (0 B)
        </div>
    </footer>
</div>

<style>
        /* General Styles */
    :root {
        --bg-color: #2c2f33;
        --primary-bg: #36393f;
        --secondary-bg: #23272a;
        --header-bg: #202225;
        --text-color: #dcddde;
        --text-muted: #72767d;
        --accent-color: #4a90e2;
        --border-color: #40444b;
        --selected-color: #4c515a;
    }



    .app-container {
        display: flex;
        flex-direction: column;
        height: 100vh;
    }

    /* Toolbar */
    .toolbar {
        display: flex;
        justify-content: space-between;
        align-items: center;
        background-color: var(--primary-bg);
        padding: 6px 12px;
        border-bottom: 1px solid var(--border-color);
    }

    .toolbar-left, .toolbar-right {
        display: flex;
        align-items: center;
        gap: 8px;
    }

    .icon-button {
        background: none;
        border: none;
        color: var(--text-color);
        font-size: 18px;
        cursor: pointer;
        padding: 6px;
        border-radius: 4px;
    }

    .icon-button:hover {
        background-color: var(--selected-color);
    }

    .separator {
        width: 1px;
        height: 24px;
        background-color: var(--border-color);
        margin: 0 4px;
    }

    .search-container {
        display: flex;
        align-items: center;
        background-color: var(--secondary-bg);
        border: 1px solid var(--border-color);
        border-radius: 4px;
        padding: 4px 8px;
    }

    .search-container i {
        color: var(--text-muted);
    }

    .search-container input {
        background: none;
        border: none;
        color: var(--text-color);
        margin-left: 8px;
        outline: none;
    }

    .filter-container {
        display: flex;
        align-items: center;
    }

    .filter-container select {
        background-color: var(--secondary-bg);
        color: var(--text-color);
        border: 1px solid var(--border-color);
        border-radius: 4px;
        padding: 4px 8px;
    }

    /* Main Content */
    .main-content {
        display: flex;
        flex-grow: 1;
        overflow: hidden;
    }

    /* Sidebar */
    .sidebar {
        width: 200px;
        background-color: var(--primary-bg);
        padding: 10px;
        overflow-y: auto;
        border-right: 1px solid var(--border-color);
    }

    .sidebar-section h3 {
        font-size: 12px;
        color: var(--text-muted);
        text-transform: uppercase;
        margin: 15px 0 5px 0;
    }

    .sidebar-section ul {
        list-style: none;
        padding: 0;
        margin: 0;
    }

    .sidebar-section li {
        padding: 6px 10px;
        cursor: pointer;
        border-radius: 4px;
    }

    .sidebar-section li:hover {
        background-color: var(--selected-color);
    }

    /* Content Area */
    .content-area {
        flex-grow: 1;
        display: flex;
        flex-direction: column;
        background-color: var(--bg-color);
    }

    .torrent-list {
        flex-grow: 1;
        overflow: auto;
    }

    table {
        width: 100%;
        border-collapse: collapse;
    }

    th, td {
        padding: 10px 12px;
        text-align: left;
        border-bottom: 1px solid var(--border-color);
    }

    th {
        background-color: var(--primary-bg);
        font-size: 13px;
        font-weight: normal;
    }

    tr.selected {
        background-color: var(--accent-color);
        color: white;
    }

    .progress-bar-container {
        display: flex;
        align-items: center;
        gap: 8px;
        width: 150px;
    }

    .progress-bar {
        background-color: #2a6a3d;
        height: 16px;
        border-radius: 3px;
    }

    /* Details Panel */
    .details-panel {
        background-color: var(--primary-bg);
        border-top: 1px solid var(--border-color);
        padding: 10px;
    }

    .details-header {
        display: flex;
        align-items: center;
        gap: 10px;
        margin-bottom: 10px;
    }

    .main-progress-bar-container {
        flex-grow: 1;
        background-color: var(--secondary-bg);
        border-radius: 4px;
        height: 20px;
        position: relative;
        overflow: hidden;
        display: flex;
        align-items: center;
    }

    .main-progress-bar {
        background-color: var(--accent-color);
        height: 100%;
    }

    .main-progress-bar-container span {
        position: absolute;
        left: 50%;
        transform: translateX(-50%);
        color: white;
        font-weight: bold;
    }


    .details-tabs {
        display: flex;
        border-bottom: 1px solid var(--border-color);
        margin-bottom: 10px;
    }

    .tab-button {
        background: none;
        border: none;
        color: var(--text-muted);
        padding: 8px 16px;
        cursor: pointer;
        border-bottom: 2px solid transparent;
    }

    .tab-button.active {
        color: var(--text-color);
        border-bottom: 2px solid var(--accent-color);
    }

    .details-content {
        color: var(--text-color);
    }

    .transfer-info .label {
        font-weight: bold;
        color: var(--text-muted);
    }

    .info-grid {
        display: grid;
        grid-template-columns: repeat(2, 1fr);
        gap: 8px 20px;
        font-size: 13px;
    }

    /* Status Bar */
    .status-bar {
        display: flex;
        justify-content: space-between;
        background-color: var(--primary-bg);
        padding: 6px 12px;
        border-top: 1px solid var(--border-color);
        font-size: 12px;
    }

    .status-left, .status-right {
        display: flex;
        align-items: center;
        gap: 15px;
    }

    .status-right i {
        margin-right: 5px;
    }
</style>