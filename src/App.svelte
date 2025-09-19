<script>
  import TaskModal from './components/TaskModal.svelte';


  let tasks = [
    { id: 1, content: 'Configurar el proyecto Svelte', completed: true },
    { id: 2, content: 'Crear el componente de la lista de tareas', completed: true },
    { id: 3, content: 'Añadir la funcionalidad para editar y eliminar', completed: false },
    { id: 4, content: 'Tomar un café ☕', completed: false }
  ];


  let showCompleted = false;
  let isModalOpen = false;
  let taskToEdit = null;


  function openCreateModal() {
    taskToEdit = null;
    isModalOpen = true;
  }

  function openEditModal(task) {
    taskToEdit = task;
    isModalOpen = true;
  }

  function handleDelete(taskId) {
    if (confirm('¿Estás seguro de que querés eliminar esta tarea?')) {
      tasks = tasks.filter(t => t.id !== taskId);
    }
  }

  function handleToggleStatus(taskId, newStatus) {
    tasks = tasks.map(t => t.id === taskId ? { ...t, completed: newStatus } : t);
  }

 
  function handleSaveTask(event) {
    const savedTask = event.detail;

    if (savedTask.id) { 
      tasks = tasks.map(t => t.id === savedTask.id ? savedTask : t);
    } else { 
      const newTask = { ...savedTask, id: Date.now() }; 
      tasks = [...tasks, newTask];
    }
    
    isModalOpen = false; 
  }

  $: filteredTasks = tasks.filter(task => task.completed === showCompleted);

</script>

<div class="app-container">
  <h1 class="app-title">Mi To-Do List</h1>
  
  <div class="top-controls">
    <button class="btn btn-primary" on:click={openCreateModal}>
      Crear Nueva Tarea
    </button>
    <div class="filters">
      <button class="btn" class:btn-secondary={!showCompleted} on:click={() => showCompleted = false}>
        Ver Pendientes
      </button>
      <button class="btn" class:btn-secondary={showCompleted} on:click={() => showCompleted = true}>
        Ver Completadas
      </button>
    </div>
  </div>
  
  <hr />
  
  <div class="notes-list">
    <h2>{showCompleted ? 'Tareas Completadas' : 'Tareas Pendientes'}</h2>
    <ul>
      {#each filteredTasks as task (task.id)}
        <li class="note-item">
          <div class="note-content">
            <p>{task.content}</p>
          </div>
          <div class="note-actions">
            <button class="btn btn-secondary" on:click={() => openEditModal(task)}>Editar</button>
            {#if showCompleted}
              <button class="btn btn-success" on:click={() => handleToggleStatus(task.id, false)}>Reactivar</button>
            {:else}
              <button class="btn btn-success" on:click={() => handleToggleStatus(task.id, true)}>Completar</button>
            {/if}
            <button class="btn btn-danger" on:click={() => handleDelete(task.id)}>Eliminar</button>
          </div>
        </li>
      {/each}
    </ul>
  </div>

  {#if isModalOpen}
    <TaskModal 
      taskToEdit={taskToEdit} 
      on:save={handleSaveTask}
      on:close={() => isModalOpen = false} 
    />
  {/if}
</div>

<footer class="footer">
  <p>Página creada por Ortiz Daiana, Higonet Juan Ignacio, Mercado Martin, Sola Amparo</p>
</footer>

<style>
  
  :root {
    --bg-primary: #1e1f22;
    --bg-secondary: #2b2d31;
    --bg-tertiary: #383a40;
    --text-primary: #f2f3f5;
    --text-secondary: #b5bac1;
    --accent-blue: #4dabf7;
    --accent-blue-hover: #339af0;
    --accent-green: #51cf66;
    --accent-green-hover: #40c057;
    --accent-red: #ff6b6b;
    --accent-red-hover: #fa5252;
    --border-color: #404249;
    --shadow: 0 4px 20px rgba(0, 0, 0, 0.3);
    --shadow-hover: 0 6px 25px rgba(0, 0, 0, 0.4);
  }


  :global(body) {
    margin: 0;
    font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Fira Sans', 'Droid Sans', 'Helvetica Neue', sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    background-color: var(--bg-primary);
    color: var(--text-primary);
    min-height: 100vh;
  }

  .app-container {
    max-width: 900px;
    margin: 40px auto;
    padding: 20px 40px;
    background-color: var(--bg-secondary);
    border-radius: 12px;
    box-shadow: var(--shadow);
    border: 1px solid var(--border-color);
  }

  .app-title {
    text-align: center;
    color: var(--text-primary);
    font-size: 2.5rem;
    margin-bottom: 30px;
    text-shadow: 0 2px 4px rgba(0, 0, 0, 0.3);
  }

  .top-controls {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 25px;
    flex-wrap: wrap;
    gap: 10px;
  }

  .filters {
    display: flex;
    gap: 10px;
  }

  .btn {
    padding: 10px 15px;
    border: none;
    border-radius: 6px;
    font-size: 14px;
    font-weight: 500;
    cursor: pointer;
    transition: all 0.2s ease-in-out;
    border: 1px solid transparent;
  }

  .btn:hover {
    transform: translateY(-2px);
    box-shadow: var(--shadow-hover);
  }

  .btn-primary { 
    background-color: var(--accent-blue); 
    color: white;
    border-color: var(--accent-blue);
  }
  .btn-primary:hover { 
    background-color: var(--accent-blue-hover);
    border-color: var(--accent-blue-hover);
  }

  .btn-secondary { 
    background-color: var(--bg-tertiary); 
    color: var(--text-primary);
    border-color: var(--border-color);
  }
  .btn-secondary:hover { 
    background-color: #4a4d55;
    border-color: #5a5d65;
  }

  .btn-danger { 
    background-color: var(--accent-red); 
    color: white;
    border-color: var(--accent-red);
  }
  .btn-danger:hover { 
    background-color: var(--accent-red-hover);
    border-color: var(--accent-red-hover);
  }

  .btn-success { 
    background-color: var(--accent-green); 
    color: white;
    border-color: var(--accent-green);
  }
  .btn-success:hover { 
    background-color: var(--accent-green-hover);
    border-color: var(--accent-green-hover);
  }

  hr {
    border: none;
    border-top: 1px solid var(--border-color);
    margin-bottom: 25px;
    opacity: 0.6;
  }

  .notes-list h2 {
    color: var(--text-primary);
    border-bottom: 2px solid var(--accent-blue);
    padding-bottom: 10px;
    margin-bottom: 20px;
    font-weight: 600;
  }

  .notes-list ul {
    list-style: none;
    padding: 0;
  }

  .note-item {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 20px;
    background-color: var(--bg-tertiary);
    border: 1px solid var(--border-color);
    border-radius: 8px;
    margin-bottom: 15px;
    transition: all 0.2s;
  }

  .note-item:hover {
    box-shadow: var(--shadow-hover);
    border-color: #5a5d65;
    transform: translateY(-1px);
  }

  .note-content { 
    flex-grow: 1; 
  }
  
  .note-content p { 
    margin: 0; 
    font-size: 16px; 
    color: var(--text-primary);
    line-height: 1.5;
  }
  
  .note-actions { 
    display: flex; 
    gap: 10px; 
    flex-shrink: 0; 
    margin-left: 20px; 
  }


  :global(::-webkit-scrollbar) {
    width: 8px;
  }

  :global(::-webkit-scrollbar-track) {
    background: var(--bg-primary);
  }

  :global(::-webkit-scrollbar-thumb) {
    background: var(--border-color);
    border-radius: 4px;
  }

  :global(::-webkit-scrollbar-thumb:hover) {
    background: #5a5d65;
  }

 
  .footer {
    background-color: var(--bg-secondary);
    border-top: 1px solid var(--border-color);
    border-bottom: 0px;
    padding: 20px 0;
    margin-top: 40px;
    text-align: center;
  }

  .footer p {
    margin: 0;
    color: var(--text-secondary);
    font-size: 14px;
    font-weight: 400;
    letter-spacing: 0.5px;
  }
</style>