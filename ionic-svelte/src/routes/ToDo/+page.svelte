<script>
  import { onMount } from 'svelte';

  let tasks = [];
  let groupedTasks = {};
  let taskInput = '';
  let taskDueDateInput = '';
  let taskDueTimeInput = '';
  let taskDescriptionInput = '';

  let idCounter = 0;

  function uniqueId(prefix = 'id') {
    return `${prefix}${++idCounter}`;
  }

  onMount(() => {
    loadTasks();
    changeBackgroundImage('https://img.freepik.com/premium-vector/white-background-gradient-abstract-design-modern_343694-3049.jpg');
  });

  function changeBackgroundImage(imageUrl) {
    document.body.style.backgroundImage = `url('${imageUrl}')`;
    document.body.style.backgroundSize = 'cover';
    document.body.style.backgroundRepeat = 'no-repeat';
    document.body.style.backgroundPosition = 'center';
  }

  function formatTime(timeStr) {
    const time = timeStr.match(/^(\d{2}):(\d{2})$/);
    if (!time) return timeStr; // Return original if format is invalid

    let hours = parseInt(time[1], 10);
    const minutes = time[2];
    const ampm = hours >= 12 ? 'PM' : 'AM';
    hours = hours % 12;
    hours = hours ? hours : 12; // the hour '0' should be '12'
    return `${hours}:${minutes} ${ampm}`;
  }

  function addTask() {
    const date = new Date(taskDueDateInput);
    date.setMinutes(date.getMinutes() + date.getTimezoneOffset());
    const newTask = {
      id: uniqueId(),
      title: taskInput,
      dueDate: date.toISOString().substring(0, 10),
      dueTime: taskDueTimeInput,
      description: taskDescriptionInput,
      showDescription: false,
      subtaskInput: '', // Add subtaskInput to each task
      subTasks: [],
      completed: false
    };
    tasks = [...tasks, newTask];
    saveTasks();
    taskInput = '';
    taskDueDateInput = '';
    taskDueTimeInput = '';
    taskDescriptionInput = '';
  }

  function addSubtask(taskId, title) {
    const task = tasks.find(task => task.id === taskId);
    if (task && task.subtaskInput.trim()) {
      const newSubtask = {
        id: uniqueId('sub'),
        title: task.subtaskInput,
        completed: false
      };
      task.subTasks.push(newSubtask);
      saveTasks();
      task.subtaskInput = ''; // Clear the task's subtask input
    }
  }

  function toggleSubtask(taskId, subtaskId) {
    const task = tasks.find(task => task.id === taskId);
    if (task) {
      const subtask = task.subTasks.find(st => st.id === subtaskId);
      if (subtask) {
        subtask.completed = !subtask.completed;
        saveTasks();
      }
    }
  }

  function deleteSubtask(taskId, subtaskId) {
    const task = tasks.find(task => task.id === taskId);
    if (task) {
      task.subTasks = task.subTasks.filter(st => st.id !== subtaskId);
      saveTasks();
    }
  }

  function deleteTask(taskId) {
    tasks = tasks.filter(task => task.id !== taskId);
    saveTasks();
  }

  function toggleTaskCompleted(taskId) {
    const task = tasks.find(task => task.id === taskId);
    if (task) {
      task.completed = !task.completed;
      saveTasks();
    }
  }

  function toggleDescription(taskId) {
    tasks = tasks.map(task =>
      task.id === taskId ? { ...task, showDescription: !task.showDescription } : task
    );
    saveTasks();
  }

  function saveTasks() {
    // Sort tasks by date and time before saving
    tasks.sort((a, b) => new Date(a.dueDate + 'T' + a.dueTime).getTime() - new Date(b.dueDate + 'T' + b.dueTime).getTime());
    localStorage.setItem('toDoList', JSON.stringify(tasks));
    groupTasksByDate();
  }

  function loadTasks() {
    const loadedTasks = JSON.parse(localStorage.getItem('toDoList')) || [];
    // Sort tasks by date and time (ascending order)
    tasks = loadedTasks.sort((a, b) => new Date(a.dueDate + 'T' + a.dueTime).getTime() - new Date(b.dueDate + 'T' + b.dueTime).getTime());
    groupTasksByDate();
  }

  function groupTasksByDate() {
    groupedTasks = tasks.reduce((acc, task) => {
      const date = task.dueDate;
      if (!acc[date]) {
        acc[date] = [];
      }
      acc[date].push(task);
      return acc;
    }, {});
  }

  function formatDate(dateStr) {
    const date = new Date(dateStr);
    date.setMinutes(date.getMinutes() + date.getTimezoneOffset());
    return date.toLocaleDateString(undefined, { weekday: 'long', year: 'numeric', month: 'long', day: 'numeric' });
  }
</script>




<style>
  #toDoApp {
    width: 100%;
    max-width: 800px;
    margin: 40px auto;
    padding: 20px;
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    background: linear-gradient(to right, #e3f2fd, #bccae7); /* Gentle blue gradient */
    border-radius: 12px;
    box-shadow: 0 4px 8px rgba(0,0,0,0.1);
    color: #333;
    transition: transform 0.2s ease;
  }

  .input-group {
    display: flex;
    flex-direction: column;
    gap: 10px;
    background-color: #ffffff;
    padding: 15px;
    border-radius: 12px;
    margin-bottom: 30px;
    box-shadow: 0 2px 4px rgba(0,0,0,0.05);
  }

  @media (min-width: 768px) {
    .input-group {
      flex-direction: row;
      align-items: center;
    }
    
    .input-group > * {
      flex: 1;
    }
    
    .input-group > button {
      margin-left: 10px;
      flex: none; 
    }
  }

  input, textarea, button {
    padding: 10px;
    border: 1px solid #ddd;
    border-radius: 8px;
    outline: none;
  }

  button {
    background-color: #0084ff;
    color: white;
    cursor: pointer;
    border: none;
    transition: background-color 0.2s, transform 0.2s;
  }

  button:hover {
    background-color: #0066cc;
    transform: translateY(-2px);
  }

  input[type="date"], input[type="time"] {
    cursor: pointer;
  }

  .tasks-container {
    max-height: 600px;
    overflow-y: auto;
  }

  ul.tasks {
    list-style: none;
    padding: 0;
    margin: 0;
  }

  li {
    background-color: #fff;
    border-radius: 8px;
    padding: 15px;
    margin: 10px 0;
    box-shadow: 0 2px 4px rgba(0,0,0,0.05);
    display: flex;
    flex-direction: column;
    gap: 10px;
  }

  h2, h3 {
    margin: 0 0 10px 0;
  }

  .task-item {
    display: flex;
    justify-content: space-between;
    align-items: center;
  }

  .subtasks {
    display: flex;
    flex-direction: column;
    gap: 10px;
  }

  .subtask-input-group {
    display: flex;
    gap: 10px;
  }

  .completed-task {
    text-decoration: line-through;
    opacity: 0.6;
  }

  .task-detail, .task-buttons {
    display: flex;
    align-items: center;
    gap: 10px;
  }

  .task-detail.show {
    animation: fadeIn 0.3s ease-in-out;
  }

  @keyframes fadeIn {
    0% { opacity: 0; }
    100% { opacity: 1; }
  }
</style>

<div id="toDoApp">
  <h2>To-Do List</h2>
  <div class="input-group">
    <input type="text" bind:value={taskInput} placeholder="Enter a new task" />
    <input type="date" bind:value={taskDueDateInput} />
    <input type="time" bind:value={taskDueTimeInput} />
    <textarea bind:value={taskDescriptionInput} placeholder="Description (optional)"></textarea>
    <button on:click={addTask}>Add Task ➕</button>
  </div>
  <div class="tasks-container">
    {#each Object.keys(groupedTasks) as date}
      <div class="date-group">
        <h3>{formatDate(date)}</h3>
        <ul class="tasks">
          {#each groupedTasks[date] as task (task.id)}
            <li class={task.completed ? 'completed-task' : ''}>
              <div class="task-item">
                <div>
                  <input type="checkbox" bind:checked={task.completed} on:click={() => toggleTaskCompleted(task.id)}>
                  <strong>{task.title}</strong>
                  <p>Due: {formatTime(task.dueTime)}</p>
                </div>
                <div class="task-buttons">
                  <button on:click={() => deleteTask(task.id)}>🗑️</button>
                  <button on:click={() => toggleDescription(task.id)}>⬇️</button>
                </div>
              </div>
              {#if task.showDescription}
                <p class="task-detail show">{task.description}</p>
              {/if}
              <div class="subtasks">
                <input type="text" bind:value={task.subtaskInput} placeholder="Add a subtask" />
                <button on:click={() => addSubtask(task.id)}>Add Subtask ➕</button>
                <ul>
                  {#each task.subTasks as subtask (subtask.id)}
                    <li class={subtask.completed ? 'completed-task' : ''}>
                      <div class="task-item">
                        <input type="checkbox" bind:checked={subtask.completed} on:click={() => toggleSubtask(task.id, subtask.id)}>
                        <strong>{subtask.title}</strong>
                        <div class="task-buttons">
                          <button on:click={() => deleteSubtask(task.id, subtask.id)}>🗑️</button>
                        </div>
                      </div>
                    </li>
                  {/each}
                </ul>
              </div>
            </li>
          {/each}
        </ul>
      </div>
    {/each}
  </div>
</div>