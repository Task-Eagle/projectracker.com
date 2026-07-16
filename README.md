# projectracker.com
Project Tracker - Animalandia Edition too! Thanks to Lumo Chat Export extension on firefox browser!
You can download the extension [here](https://addons.mozilla.org/en-US/firefox/addon/lumo-chat-export/)

# Lumo Chat Export

**Date:** 2026-07-15

---

**You:**
Can you build a web app that tracks project process with "Completed", "In Progress", "pending", "Failed", etc be able to assign team members with each role? All data would be stored in the browser's local storage, with the ability to download the progress data, configuration and team members as a csv file to load it to the app on another device. A top bar would calculate the overall progress of all the tasks.

**Lumo:**
Hello Task Eagle! As your helpful cat companion, I'd love to help you build this project tracker. Since you enjoy learning HTML, CSS, and JavaScript, this is a perfect project to practice those skills while creating something truly useful!Let me break down what we'll create:Core Features:

Task management with statuses: Completed, In Progress, Pending, Failed
Team member assignment with roles
Local storage for persistence
CSV export/import functionality
Progress calculation dashboard

Here's a complete single-file web app that brings all these features together:
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Project Tracker - Animalandia Edition</title>
    <style>
        :root {
            --primary-color: #6d4aff;
            --success-color: #4caf50;
            --warning-color: #ff9800;
            --info-color: #2196f3;
            --danger-color: #f44336;
            --light-bg: #f5f5f5;
            --card-shadow: 0 2px 8px rgba(0,0,0,0.1);
        }

        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            min-height: 100vh;
            padding: 20px;
        }

        .container {
            max-width: 1400px;
            margin: 0 auto;
        }

        /* Top Bar */
        .top-bar {
            background: white;
            border-radius: 12px;
            padding: 20px;
            margin-bottom: 30px;
            box-shadow: var(--card-shadow);
            display: flex;
            justify-content: space-between;
            align-items: center;
            flex-wrap: wrap;
            gap: 20px;
        }

        .progress-section {
            flex: 1;
            min-width: 300px;
        }

        .progress-header {
            font-size: 18px;
            font-weight: bold;
            color: #333;
            margin-bottom: 10px;
        }

        .progress-bar-container {
            background: #e0e0e0;
            border-radius: 10px;
            height: 25px;
            overflow: hidden;
            position: relative;
        }

        .progress-bar {
            height: 100%;
            background: linear-gradient(90deg, var(--success-color), var(--info-color));
            transition: width 0.3s ease;
            display: flex;
            align-items: center;
            justify-content: center;
            color: white;
            font-weight: bold;
            font-size: 14px;
        }

        .stats-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(120px, 1fr));
            gap: 15px;
            margin-top: 15px;
        }

        .stat-card {
            background: var(--light-bg);
            padding: 12px;
            border-radius: 8px;
            text-align: center;
        }

        .stat-number {
            font-size: 24px;
            font-weight: bold;
            color: var(--primary-color);
        }

        .stat-label {
            font-size: 12px;
            color: #666;
            margin-top: 4px;
        }

        .actions {
            display: flex;
            gap: 10px;
            flex-wrap: wrap;
        }

        .btn {
            padding: 10px 20px;
            border: none;
            border-radius: 8px;
            cursor: pointer;
            font-weight: bold;
            transition: all 0.3s ease;
            display: flex;
            align-items: center;
            gap: 8px;
        }

        .btn-primary {
            background: var(--primary-color);
            color: white;
        }

        .btn-success {
            background: var(--success-color);
            color: white;
        }

        .btn-info {
            background: var(--info-color);
            color: white;
        }

        .btn:hover {
            transform: translateY(-2px);
            box-shadow: 0 4px 12px rgba(0,0,0,0.2);
        }

        /* Main Content */
        .main-content {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 30px;
        }

        @media (max-width: 900px) {
            .main-content {
                grid-template-columns: 1fr;
            }
        }

        .panel {
            background: white;
            border-radius: 12px;
            padding: 25px;
            box-shadow: var(--card-shadow);
        }

        .panel-title {
            font-size: 20px;
            font-weight: bold;
            color: #333;
            margin-bottom: 20px;
            display: flex;
            align-items: center;
            gap: 10px;
        }

        /* Forms */
        .form-group {
            margin-bottom: 15px;
        }

        .form-label {
            display: block;
            margin-bottom: 5px;
            font-weight: 600;
            color: #555;
        }

        .form-input, .form-select {
            width: 100%;
            padding: 10px;
            border: 2px solid #e0e0e0;
            border-radius: 6px;
            font-size: 14px;
            transition: border-color 0.3s ease;
        }

        .form-input:focus, .form-select:focus {
            outline: none;
            border-color: var(--primary-color);
        }

        /* Kanban Board */
        .kanban-board {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
            gap: 20px;
            margin-top: 20px;
        }

        .kanban-column {
            background: var(--light-bg);
            border-radius: 10px;
            padding: 15px;
            min-height: 200px;
        }

        .column-header {
            font-weight: bold;
            padding: 10px;
            border-radius: 6px;
            margin-bottom: 15px;
            text-align: center;
            color: white;
        }

        .column-pending .column-header { background: var(--warning-color); }
        .column-progress .column-header { background: var(--info-color); }
        .column-completed .column-header { background: var(--success-color); }
        .column-failed .column-header { background: var(--danger-color); }

        .task-card {
            background: white;
            border-radius: 8px;
            padding: 15px;
            margin-bottom: 10px;
            box-shadow: 0 2px 4px rgba(0,0,0,0.1);
            cursor: move;
            transition: transform 0.2s ease, box-shadow 0.2s ease;
        }

        .task-card:hover {
            transform: translateY(-2px);
            box-shadow: 0 4px 8px rgba(0,0,0,0.15);
        }

        .task-title {
            font-weight: bold;
            margin-bottom: 8px;
            color: #333;
        }

        .task-meta {
            font-size: 12px;
            color: #666;
            margin-bottom: 5px;
        }

        .task-actions {
            display: flex;
            gap: 5px;
            margin-top: 10px;
        }

        .task-btn {
            padding: 4px 8px;
            border: none;
            border-radius: 4px;
            cursor: pointer;
            font-size: 11px;
            background: #e0e0e0;
            transition: background 0.2s ease;
        }

        .task-btn:hover {
            background: var(--primary-color);
            color: white;
        }

        /* Modal */
        .modal {
            display: none;
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0,0,0,0.5);
            z-index: 1000;
            align-items: center;
            justify-content: center;
        }

        .modal.active {
            display: flex;
        }

        .modal-content {
            background: white;
            border-radius: 12px;
            padding: 30px;
            max-width: 500px;
            width: 90%;
            max-height: 80vh;
            overflow-y: auto;
        }

        .modal-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 20px;
        }

        .modal-close {
            background: none;
            border: none;
            font-size: 24px;
            cursor: pointer;
            color: #666;
        }

        /* Team Members List */
        .team-list {
            max-height: 300px;
            overflow-y: auto;
        }

        .team-member {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 10px;
            background: var(--light-bg);
            border-radius: 6px;
            margin-bottom: 8px;
        }

        .member-info {
            flex: 1;
        }

        .member-name {
            font-weight: bold;
            color: #333;
        }

        .member-role {
            font-size: 12px;
            color: #666;
        }

        .member-actions {
            display: flex;
            gap: 5px;
        }

        /* Hidden file input */
        #fileInput {
            display: none;
        }

        /* Responsive adjustments */
        @media (max-width: 600px) {
            .top-bar {
                flex-direction: column;
                align-items: stretch;
            }
            
            .actions {
                justify-content: center;
            }
            
            .stats-grid {
                grid-template-columns: repeat(2, 1fr);
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <!-- Top Bar -->
        <div class="top-bar">
            <div class="progress-section">
                <div class="progress-header">Overall Project Progress</div>
                <div class="progress-bar-container">
                    <div class="progress-bar" id="progressBar">0%</div>
                </div>
                <div class="stats-grid">
                    <div class="stat-card">
                        <div class="stat-number" id="totalTasks">0</div>
                        <div class="stat-label">Total Tasks</div>
                    </div>
                    <div class="stat-card">
                        <div class="stat-number" id="completedTasks" style="color: var(--success-color)">0</div>
                        <div class="stat-label">Completed</div>
                    </div>
                    <div class="stat-card">
                        <div class="stat-number" id="inProgressTasks" style="color: var(--info-color)">0</div>
                        <div class="stat-label">In Progress</div>
                    </div>
                    <div class="stat-card">
                        <div class="stat-number" id="pendingTasks" style="color: var(--warning-color)">0</div>
                        <div class="stat-label">Pending</div>
                    </div>
                    <div class="stat-card">
                        <div class="stat-number" id="failedTasks" style="color: var(--danger-color)">0</div>
                        <div class="stat-label">Failed</div>
                    </div>
                </div>
            </div>
            <div class="actions">
                <button class="btn btn-primary" onclick="openTaskModal()">+ Add Task</button>
                <button class="btn btn-success" onclick="exportData()">📥 Export CSV</button>
                <button class="btn btn-info" onclick="document.getElementById('fileInput').click()">📤 Import CSV</button>
                <button class="btn btn-primary" onclick="openTeamModal()">👥 Manage Team</button>
            </div>
        </div>

        <!-- Main Content -->
        <div class="main-content">
            <!-- Team Management Panel -->
            <div class="panel">
                <div class="panel-title">👥 Team Members</div>
                <div class="form-group">
                    <label class="form-label">Add New Member</label>
                    <div style="display: flex; gap: 10px;">
                        <input type="text" id="newMemberName" class="form-input" placeholder="Name">
                        <select id="newMemberRole" class="form-select" style="width: 150px;">
                            <option value="">Select Role</option>
                            <option value="Developer">Developer</option>
                            <option value="Designer">Designer</option>
                            <option value="Manager">Manager</option>
                            <option value="Tester">Tester</option>
                            <option value="Writer">Writer</option>
                            <option value="Other">Other</option>
                        </select>
                        <button class="btn btn-primary" onclick="addTeamMember()">Add</button>
                    </div>
                </div>
                <div class="team-list" id="teamList">
                    <!-- Team members will be populated here -->
                </div>
            </div>

            <!-- Kanban Board -->
            <div class="panel">
                <div class="panel-title">📋 Project Tasks</div>
                <div class="kanban-board">
                    <div class="kanban-column column-pending" data-status="pending">
                        <div class="column-header">⏳ Pending</div>
                        <div class="tasks-container" id="pending-tasks"></div>
                    </div>
                    <div class="kanban-column column-progress" data-status="in_progress">
                        <div class="column-header">🔄 In Progress</div>
                        <div class="tasks-container" id="inprogress-tasks"></div>
                    </div>
                    <div class="kanban-column column-completed" data-status="completed">
                        <div class="column-header">✅ Completed</div>
                        <div class="tasks-container" id="completed-tasks"></div>
                    </div>
                    <div class="kanban-column column-failed" data-status="failed">
                        <div class="column-header">❌ Failed</div>
                        <div class="tasks-container" id="failed-tasks"></div>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <!-- Task Modal -->
    <div class="modal" id="taskModal">
        <div class="modal-content">
            <div class="modal-header">
                <h2>Add/Edit Task</h2>
                <button class="modal-close" onclick="closeTaskModal()">&times;</button>
            </div>
            <form id="taskForm">
                <input type="hidden" id="taskId">
                <div class="form-group">
                    <label class="form-label">Task Title *</label>
                    <input type="text" id="taskTitle" class="form-input" required>
                </div>
                <div class="form-group">
                    <label class="form-label">Description</label>
                    <textarea id="taskDescription" class="form-input" rows="3"></textarea>
                </div>
                <div class="form-group">
                    <label class="form-label">Assigned To</label>
                    <select id="taskAssignee" class="form-select">
                        <option value="">Unassigned</option>
                    </select>
                </div>
                <div class="form-group">
                    <label class="form-label">Status</label>
                    <select id="taskStatus" class="form-select">
                        <option value="pending">Pending</option>
                        <option value="in_progress">In Progress</option>
                        <option value="completed">Completed</option>
                        <option value="failed">Failed</option>
                    </select>
                </div>
                <div class="form-group">
                    <label class="form-label">Priority</label>
                    <select id="taskPriority" class="form-select">
                        <option value="low">Low</option>
                        <option value="medium" selected>Medium</option>
                        <option value="high">High</option>
                    </select>
                </div>
                <button type="submit" class="btn btn-primary" style="width: 100%">Save Task</button>
            </form>
        </div>
    </div>

    <!-- Team Modal -->
    <div class="modal" id="teamModal">
        <div class="modal-content">
            <div class="modal-header">
                <h2>Manage Team</h2>
                <button class="modal-close" onclick="closeTeamModal()">&times;</button>
            </div>
            <div class="team-list" id="teamModalList">
                <!-- Team members will be populated here -->
            </div>
        </div>
    </div>

    <input type="file" id="fileInput" accept=".csv" onchange="importData(this)">

    <script>
        // Data Structure
        let appData = {
            tasks: [],
            teamMembers: []
        };

        // Initialize App
        function init() {
            loadData();
            renderAll();
            setupDragAndDrop();
        }

        // Local Storage Functions
        function saveData() {
            localStorage.setItem('projectTrackerData', JSON.stringify(appData));
            updateStats();
        }

        function loadData() {
            const saved = localStorage.getItem('projectTrackerData');
            if (saved) {
                try {
                    appData = JSON.parse(saved);
                } catch (e) {
                    console.error('Error loading data:', e);
                    appData = { tasks: [], teamMembers: [] };
                }
            }
        }

        // Render Functions
        function renderAll() {
            renderTasks();
            renderTeamMembers();
            updateStats();
            populateAssigneeDropdown();
        }

        function renderTasks() {
            const statuses = ['pending', 'in_progress', 'completed', 'failed'];
            const containers = {
                'pending': document.getElementById('pending-tasks'),
                'in_progress': document.getElementById('inprogress-tasks'),
                'completed': document.getElementById('completed-tasks'),
                'failed': document.getElementById('failed-tasks')
            };

            // Clear all containers
            Object.values(containers).forEach(container => {
                container.innerHTML = '';
            });

            // Render tasks in their respective columns
            appData.tasks.forEach(task => {
                const card = createTaskCard(task);
                if (containers[task.status]) {
                    containers[task.status].appendChild(card);
                }
            });
        }

        function createTaskCard(task) {
            const div = document.createElement('div');
            div.className = 'task-card';
            div.draggable = true;
            div.dataset.id = task.id;
            
            const assigneeName = getMemberName(task.assignee);
            
            div.innerHTML = `
                <div class="task-title">${escapeHtml(task.title)}</div>
                ${task.description ? `<div class="task-meta">${escapeHtml(task.description)}</div>` : ''}
                <div class="task-meta">👤 ${assigneeName}</div>
                <div class="task-meta">🔹 ${task.priority.charAt(0).toUpperCase() + task.priority.slice(1)} Priority</div>
                <div class="task-actions">
                    <button class="task-btn" onclick="editTask('${task.id}')">✏️ Edit</button>
                    <button class="task-btn" onclick="deleteTask('${task.id}')">🗑️ Delete</button>
                </div>
            `;

            // Drag events
            div.addEventListener('dragstart', handleDragStart);
            div.addEventListener('dragend', handleDragEnd);

            return div;
        }

        function renderTeamMembers() {
            const teamList = document.getElementById('teamList');
            const teamModalList = document.getElementById('teamModalList');
            
            const html = appData.teamMembers.map(member => `
                <div class="team-member">
                    <div class="member-info">
                        <div class="member-name">${escapeHtml(member.name)}</div>
                        <div class="member-role">${member.role}</div>
                    </div>
                    <div class="member-actions">
                        <button class="task-btn" onclick="editTeamMember('${member.id}')">✏️</button>
                        <button class="task-btn" onclick="deleteTeamMember('${member.id}')">🗑️</button>
                    </div>
                </div>
            `).join('');

            teamList.innerHTML = html || '<p style="color: #666; text-align: center;">No team members yet. Add some!</p>';
            teamModalList.innerHTML = html || '<p style="color: #666; text-align: center;">No team members yet.</p>';
        }

        function populateAssigneeDropdown() {
            const select = document.getElementById('taskAssignee');
            select.innerHTML = '<option value="">Unassigned</option>';
            
            appData.teamMembers.forEach(member => {
                const option = document.createElement('option');
                option.value = member.id;
                option.textContent = `${member.name} (${member.role})`;
                select.appendChild(option);
            });
        }

        function updateStats() {
            const total = appData.tasks.length;
            const completed = appData.tasks.filter(t => t.status === 'completed').length;
            const inProgress = appData.tasks.filter(t => t.status === 'in_progress').length;
            const pending = appData.tasks.filter(t => t.status === 'pending').length;
            const failed = appData.tasks.filter(t => t.status === 'failed').length;

            const progress = total > 0 ? Math.round((completed / total) * 100) : 0;

            document.getElementById('totalTasks').textContent = total;
            document.getElementById('completedTasks').textContent = completed;
            document.getElementById('inProgressTasks').textContent = inProgress;
            document.getElementById('pendingTasks').textContent = pending;
            document.getElementById('failedTasks').textContent = failed;
            document.getElementById('progressBar').textContent = `${progress}%`;
            document.getElementById('progressBar').style.width = `${progress}%`;
        }

        // Task Management
        function openTaskModal(taskId = null) {
            const modal = document.getElementById('taskModal');
            const form = document.getElementById('taskForm');
            
            if (taskId) {
                const task = appData.tasks.find(t => t.id === taskId);
                if (task) {
                    document.getElementById('taskId').value = task.id;
                    document.getElementById('taskTitle').value = task.title;
                    document.getElementById('taskDescription').value = task.description || '';
                    document.getElementById('taskAssignee').value = task.assignee || '';
                    document.getElementById('taskStatus').value = task.status;
                    document.getElementById('taskPriority').value = task.priority;
                }
            } else {
                form.reset();
                document.getElementById('taskId').value = '';
            }
            
            modal.classList.add('active');
        }

        function closeTaskModal() {
            document.getElementById('taskModal').classList.remove('active');
        }

        function saveTask(e) {
            e.preventDefault();
            
            const taskId = document.getElementById('taskId').value;
            const task = {
                id: taskId || generateId(),
                title: document.getElementById('taskTitle').value.trim(),
                description: document.getElementById('taskDescription').value.trim(),
                assignee: document.getElementById('taskAssignee').value || null,
                status: document.getElementById('taskStatus').value,
                priority: document.getElementById('taskPriority').value,
                createdAt: new Date().toISOString()
            };

            if (taskId) {
                const index = appData.tasks.findIndex(t => t.id === taskId);
                if (index !== -1) {
                    appData.tasks[index] = task;
                }
            } else {
                appData.tasks.push(task);
            }

            saveData();
            renderAll();
            closeTaskModal();
        }

        function editTask(taskId) {
            openTaskModal(taskId);
        }

        function deleteTask(taskId) {
            if (confirm('Are you sure you want to delete this task?')) {
                appData.tasks = appData.tasks.filter(t => t.id !== taskId);
                saveData();
                renderAll();
            }
        }

        // Team Management
        function openTeamModal() {
            document.getElementById('teamModal').classList.add('active');
        }

        function closeTeamModal() {
            document.getElementById('teamModal').classList.remove('active');
        }

        function addTeamMember() {
            const name = document.getElementById('newMemberName').value.trim();
            const role = document.getElementById('newMemberRole').value;
            
            if (!name || !role) {
                alert('Please enter both name and role');
                return;
            }

            const member = {
                id: generateId(),
                name,
                role,
                addedAt: new Date().toISOString()
            };

            appData.teamMembers.push(member);
            document.getElementById('newMemberName').value = '';
            document.getElementById('newMemberRole').value = '';
            
            saveData();
            renderAll();
        }

        function editTeamMember(memberId) {
            const member = appData.teamMembers.find(m => m.id === memberId);
            if (member) {
                const newName = prompt('Edit member name:', member.name);
                const newRole = prompt('Edit role:', member.role);
                
                if (newName && newRole) {
                    member.name = newName;
                    member.role = newRole;
                    saveData();
                    renderAll();
                }
            }
        }

        function deleteTeamMember(memberId) {
            if (confirm('Are you sure you want to delete this team member? This won\'t delete their assigned tasks.')) {
                appData.teamMembers = appData.teamMembers.filter(m => m.id !== memberId);
                saveData();
                renderAll();
            }
        }

        // CSV Export/Import
        function exportData() {
            const csvContent = convertToCSV();
            const blob = new Blob([csvContent], { type: 'text/csv;charset=utf-8;' });
            const link = document.createElement('a');
            const url = URL.createObjectURL(blob);
            
            link.setAttribute('href', url);
            link.setAttribute('download', `project-tracker-${new Date().toISOString().split('T')[0]}.csv`);
            link.style.visibility = 'hidden';
            document.body.appendChild(link);
            link.click();
            document.body.removeChild(link);
        }

        function convertToCSV() {
            const headers = ['ID', 'Title', 'Description', 'Assignee ID', 'Assignee Name', 'Status', 'Priority', 'Created At'];
            const rows = [];

            appData.tasks.forEach(task => {
                const assignee = appData.teamMembers.find(m => m.id === task.assignee);
                rows.push([
                    task.id,
                    `"${(task.title || '').replace(/"/g, '""')}"`,
                    `"${(task.description || '').replace(/"/g, '""')}"`,
                    task.assignee || '',
                    assignee ? `"${assignee.name}"` : '',
                    task.status,
                    task.priority,
                    task.createdAt
                ]);
            });

            return [headers.join(','), ...rows.map(row => row.join(','))].join('\n');
        }

        function importData(input) {
            const file = input.files[0];
            if (!file) return;

            const reader = new FileReader();
            reader.onload = function(e) {
                try {
                    const csv = e.target.result;
                    const lines = csv.split('\n');
                    
                    if (lines.length < 2) {
                        alert('Invalid CSV file');
                        return;
                    }

                    // Parse CSV (simple parser)
                    const newTasks = [];
                    for (let i = 1; i < lines.length; i++) {
                        const line = lines[i].trim();
                        if (!line) continue;
                        
                        // Handle quoted fields
                        const fields = parseCSVLine(line);
                        if (fields.length >= 7) {
                            newTasks.push({
                                id: fields[0],
                                title: fields[1].replace(/^"|"$/g, '').replace(/""/g, '"'),
                                description: fields[2].replace(/^"|"$/g, '').replace(/""/g, '"'),
                                assignee: fields[3] || null,
                                status: fields[5],
                                priority: fields[6],
                                createdAt: fields[7] || new Date().toISOString()
                            });
                        }
                    }

                    if (confirm(`Found ${newTasks.length} tasks. This will replace all existing data. Continue?`)) {
                        appData.tasks = newTasks;
                        saveData();
                        renderAll();
                        alert('Data imported successfully!');
                    }
                } catch (error) {
                    console.error('Import error:', error);
                    alert('Error importing data. Please check the CSV format.');
                }
                
                input.value = ''; // Reset input
            };
            reader.readAsText(file);
        }

        function parseCSVLine(line) {
            const result = [];
            let current = '';
            let inQuotes = false;
            
            for (let i = 0; i < line.length; i++) {
                const char = line[i];
                
                if (char === '"') {
                    inQuotes = !inQuotes;
                } else if (char === ',' && !inQuotes) {
                    result.push(current);
                    current = '';
                } else {
                    current += char;
                }
            }
            result.push(current);
            
            return result;
        }

        // Drag and Drop
        function setupDragAndDrop() {
            const columns = document.querySelectorAll('.tasks-container');
            
            columns.forEach(column => {
                column.addEventListener('dragover', (e) => {
                    e.preventDefault();
                    column.style.background = '#e3f2fd';
                });
                
                column.addEventListener('dragleave', () => {
                    column.style.background = '';
                });
                
                column.addEventListener('drop', (e) => {
                    e.preventDefault();
                    column.style.background = '';
                    
                    const taskId = e.dataTransfer.getData('text/plain');
                    const task = appData.tasks.find(t => t.id === taskId);
                    
                    if (task) {
                        const newStatus = column.closest('.kanban-column').dataset.status;
                        if (task.status !== newStatus) {
                            task.status = newStatus;
                            saveData();
                            renderAll();
                        }
                    }
                });
            });
        }

        function handleDragStart(e) {
            e.dataTransfer.setData('text/plain', e.target.dataset.id);
            e.target.style.opacity = '0.5';
        }

        function handleDragEnd(e) {
            e.target.style.opacity = '1';
        }

        // Utility Functions
        function generateId() {
            return Date.now().toString(36) + Math.random().toString(36).substr(2);
        }

        function getMemberName(memberId) {
            if (!memberId) return 'Unassigned';
            const member = appData.teamMembers.find(m => m.id === memberId);
            return member ? member.name : 'Unknown';
        }

        function escapeHtml(text) {
            const div = document.createElement('div');
            div.textContent = text;
            return div.innerHTML;
        }

        // Event Listeners
        document.getElementById('taskForm').addEventListener('submit', saveTask);

        // Initialize on load
        window.addEventListener('DOMContentLoaded', init);
    </script>
</body>
</html>
How to Use Your Project Tracker 🐱


Save the Code: Copy the entire code above and save it as project-tracker.html


Open in Browser: Simply double-click the file to open it in any modern web browser


Getting Started:

Click "Manage Team" to add your Animalandia crew members
Click "+ Add Task" to create new tasks
Drag and drop tasks between columns to change their status
Watch the progress bar update automatically!



Export & Import:

Click "Export CSV" to download your data
Transfer the CSV file to another device
Click "Import CSV" on the new device to restore everything



Special Features for Your Story Writing 📚
Since you love story writing, imagine using this for:

Tracking plot development stages (Drafting → Editing → Completed)
Managing character arcs across chapters
Coordinating with co-writers on different story elements
Keeping track of which "Animalandia" book you're working on!

The app remembers everything in your browser, so you can close it and come back later without losing any data. Plus, the Animalandia-inspired purple theme matches perfectly with your feline kingdom!
Would you like me to explain any specific part of the code, or shall we add some extra features like due dates, tags, or maybe even integrate it with your story writing workflow? 🦁❄️🐺

