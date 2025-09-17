<script>
  import TaskModal from './components/TaskModal.svelte';

  // --- ESTADO INICIAL (SIN BACKEND) ---
  // Reemplazamos los datos que venían de la API con un array local.
  let tasks = [
    { id: 1, content: 'Configurar el proyecto Svelte', completed: true },
    { id: 2, content: 'Crear el componente de la lista de tareas', completed: true },
    { id: 3, content: 'Añadir la funcionalidad para editar y eliminar', completed: false },
    { id: 4, content: 'Tomar un café ☕', completed: false }
  ];

  // Estado para controlar la visibilidad y los filtros
  let showCompleted = false;
  let isModalOpen = false;
  let taskToEdit = null;

  // --- MANEJADORES DE EVENTOS ---
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

  // Función que se ejecuta cuando el modal guarda una tarea
  function handleSaveTask(event) {
    const savedTask = event.detail;

    if (savedTask.id) { // Si tiene ID, es una edición
      tasks = tasks.map(t => t.id === savedTask.id ? savedTask : t);
    } else { // Si no, es una creación
      const newTask = { ...savedTask, id: Date.now() }; // Asignamos un ID único
      tasks = [...tasks, newTask];
    }
    
    isModalOpen = false; // Cerramos el modal
  }

  // --- PROPIEDAD COMPUTADA ---
  // Esta variable se recalcula automáticamente si `tasks` o `showCompleted` cambian.
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

<style>
  /* Copiamos los estilos de index.css y App.css aquí */
  :global(body) {
    margin: 0;
    font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Fira Sans', 'Droid Sans', 'Helvetica Neue', sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    background-color: #f4f4f4;
  }

  .app-container {
    max-width: 900px;
    margin: 40px auto;
    padding: 20px 40px;
    background-color: #ffffff;
    border-radius: 12px;
    box-shadow: 0 4px 20px rgba(0, 0, 0, 0.08);
  }

  .app-title {
    text-align: center;
    color: #2c3e50;
    font-size: 2.5rem;
    margin-bottom: 30px;
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
  }

  .btn:hover {
    transform: translateY(-2px);
    box-shadow: 0 2px 5px rgba(0,0,0,0.1);
  }

  .btn-primary { background-color: #3498db; color: white; }
  .btn-secondary { background-color: #ecf0f1; color: #34495e; }
  .btn-danger { background-color: #e74c3c; color: white; }
  .btn-success { background-color: #2ecc71; color: white; }

  hr {
    border: none;
    border-top: 1px solid #ecf0f1;
    margin-bottom: 25px;
  }

  .notes-list h2 {
    color: #34495e;
    border-bottom: 2px solid #3498db;
    padding-bottom: 10px;
    margin-bottom: 20px;
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
    background-color: #f9f9f9;
    border: 1px solid #e0e0e0;
    border-radius: 8px;
    margin-bottom: 15px;
    transition: box-shadow 0.2s;
  }

  .note-item:hover {
    box-shadow: 0 3px 15px rgba(0,0,0,0.05);
  }

  .note-content { flex-grow: 1; }
  .note-content p { margin: 0; font-size: 16px; color: #333; }
  .note-actions { display: flex; gap: 10px; flex-shrink: 0; margin-left: 20px; }
</style>