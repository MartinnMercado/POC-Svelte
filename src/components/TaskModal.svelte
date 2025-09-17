<script>
  import { createEventDispatcher } from 'svelte';
  import { fly, fade } from 'svelte/transition';

  // Props
  export let taskToEdit = null;

  // El despachador de eventos para comunicarnos con App.svelte
  const dispatch = createEventDispatcher();

  // Estado local del formulario
  let title = '';
  let description = '';
  
  // Usamos una declaración reactiva ($:) en lugar de onMount.
  // Es más "Svelte" y reacciona si la prop `taskToEdit` cambiara en el futuro.
  $: {
    if (taskToEdit) {
      title = taskToEdit.title;
      description = taskToEdit.description;
    } else {
      title = '';
      description = '';
    }
  }

  function handleSubmit() {
    if (title.trim() === '') {
      alert('El título no puede estar vacío.');
      return;
    }

    // Aseguramos que el objeto que enviamos coincida con la estructura en App.svelte
    const taskData = {
      id: taskToEdit ? taskToEdit.id : null,
      title: title,
      description: description,
      completed: taskToEdit ? taskToEdit.completed : false
    };

    dispatch('save', taskData);
  }
</script>

<div class="modal-backdrop" transition:fade={{ duration: 200 }} on:click={() => dispatch('close')}>
  <div class="modal-content" transition:fly={{ y: -30, duration: 300 }} on:click|stopPropagation>
    <form on:submit|preventDefault={handleSubmit}>
      <h2>{taskToEdit ? 'Editar Tarea' : 'Crear Nueva Tarea'}</h2>
      
      <div class="form-group">
        <label for="title">Título</label>
        <input 
          id="title"
          type="text"
          bind:value={title}
          placeholder="Ej: Comprar leche"
          required
        />
      </div>

      <div class="form-group">
        <label for="description">Descripción</label>
        <textarea
          id="description"
          bind:value={description}
          placeholder="Detalles adicionales (opcional)..."
          rows="4"
        ></textarea>
      </div>
      
      <div class="modal-actions">
        <button type="submit" class="btn primary">Guardar Cambios</button>
        <button type="button" class="btn" on:click={() => dispatch('close')}>Cancelar</button>
      </div>
    </form>
  </div>
</div>

<style>
  /* Paleta de colores y variables para un diseño consistente */
  :root {
    --primary-color: #007bff;
    --primary-hover: #0056b3;
    --secondary-bg: #f0f0f0;
    --secondary-hover: #dcdcdc;
    --border-color: #ccc;
    --text-color: #333;
    --modal-shadow: 0 5px 15px rgba(0,0,0,0.3);
    --focus-shadow: 0 0 0 3px rgba(0, 123, 255, 0.25);
  }

  .modal-backdrop {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-color: rgba(0, 0, 0, 0.6);
    display: flex;
    justify-content: center;
    align-items: center;
    z-index: 1000;
  }

  .modal-content {
    background-color: white;
    padding: 2rem;
    border-radius: 8px;
    width: 90%;
    max-width: 500px;
    box-shadow: var(--modal-shadow);
  }

  h2 {
    margin-top: 0;
    margin-bottom: 1.5rem;
    color: var(--text-color);
    font-weight: 600;
  }

  .form-group {
    margin-bottom: 1rem;
  }

  .form-group label {
    display: block;
    margin-bottom: 0.5rem;
    font-weight: 500;
    font-size: 0.9em;
  }

  input, textarea {
    width: 100%;
    box-sizing: border-box;
    padding: 0.75rem;
    border: 1px solid var(--border-color);
    border-radius: 4px;
    font-family: inherit;
    font-size: 1rem;
    transition: border-color 0.2s, box-shadow 0.2s;
  }

  /* Efecto de foco para mejorar la accesibilidad */
  input:focus, textarea:focus {
    outline: none;
    border-color: var(--primary-color);
    box-shadow: var(--focus-shadow);
  }

  textarea {
    resize: vertical;
  }

  .modal-actions {
    display: flex;
    justify-content: flex-end;
    gap: 0.75rem;
    margin-top: 1.5rem;
  }

  .btn {
    padding: 0.6rem 1.2rem;
    border: none;
    border-radius: 4px;
    font-weight: 500;
    cursor: pointer;
    background-color: var(--secondary-bg);
    transition: background-color 0.2s, transform 0.1s;
  }
  .btn:hover { background-color: var(--secondary-hover); }

  /* Feedback visual al hacer clic */
  .btn:active {
    transform: scale(0.98);
  }
  
  .btn.primary {
    background-color: var(--primary-color);
    color: white;
  }
  .btn.primary:hover { background-color: var(--primary-hover); }
</style>