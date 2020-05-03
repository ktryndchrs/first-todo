<script>
  import {clickOutside} from '../Extensions/_ClickOutside.js';
  import client from "../graphql-client";
  import gql from 'graphql-tag';

  export let todo = "";
  
  let edit = false;

  const toggleEdit = (t) => {
    if (!edit) {
      edit = !edit      
    }
  }

  const handleKeydown = (e) => {
		if (e.key === "Enter"){
      editTodo();
    }
  }
  
  async function updateTodo(todo){
    const mutation = gql`
      mutation update_todo($id: Int!, $completed: Boolean!) {
        update_todo(where: {id: {_eq: $id}}, _set: {completed: $completed}) {
          returning {
            name
            completed
          }
        }
      }
    `
    client.mutate({
      mutation,
      variables:{
        id: todo.id,
        completed: todo.completed,
      }
    })
  };

  async function deleteTodo(todo){
    const mutation = gql`
      mutation deleteTodo($id: Int!) {
        delete_todo_by_pk(id: $id) {
          name
        }
      }
    `
    client.mutate({
      mutation,
      variables:{
        id: todo.id
      }
    })
  };
  
  async function editTodo(){
    const mutation = gql`
      mutation editTodo($id: Int!, $name: String!) {
        update_todo(where: {id: {_eq: $id}}, _set: {name: $name}) {
          returning {
            name
          }
        }
      }
    `
    client.mutate({
      mutation,
      variables:{
        id: todo.id,
        name: todo.name,
      }
    })
    edit = !edit
  };
</script>


<td on:click={(t) => toggleEdit(t)}>
  {#if edit}
    <form on:submit|preventDefault={editTodo}>
      <!-- svelte-ignore a11y-autofocus -->
      <input type="text" placeholder="Todo name" bind:value={todo.name}  use:clickOutside on:clickOutside={editTodo} autofocus/>
    </form>
  {:else}
    <div>{todo.name}</div>
  {/if}
</td>
<td>
  <label>
    <input 
      type="checkbox" 
      bind:checked={todo.completed} 
      on:change={() => updateTodo(todo)}/>
        {todo.completed ? "done" : "pending"}
  </label>
</td>
<td>
  <button style="padding: .1rem .4rem;" on:click={() => deleteTodo(todo) }>
    Delete
  </button>
</td>

<!-- <span on:click={() => editTodo(todo) }>Edit</span> -->