<script>
  import client from "./graphql-client";
  import TodoRow from "./Components/_TodoRow.svelte";
  import TodoAdd from "./Components/_TodoAdd.svelte";
  import gql from 'graphql-tag';

  // export let todo = "";

  const query = gql`
    subscription TodosQuery {
      todo {
        id
        name
        completed
      }
    }
  `
  
  const todos = client.subscribe({
    query
  })
  console.log(todos.data);
  // console.log(todos.data.todos);
  // console.log(todos.data);

</script>

<style>
  :global(button:not(:disabled)),
  :global(label) {
		cursor: pointer;
	}
</style>

<main>
  <table style="text-align: left;">
    <thead>
      <th>Name</th>
      <th>Status</th>
      <th>Delete</th>
    </thead>
    <tbody>
      <td colspan="3">
          <hr style="margin-bottom: .75rem; padding-bottom: .25rem; border: none; border-top: 1px solid lightgray"/>
      </td>
      {#if $todos && $todos.data}
        {#each $todos.data.todo as todo, index (todo)}
          <tr  style="height: 3.5rem">
            <TodoRow todo={todo}/>
          </tr>
        {/each}
      {/if}
      <tr>
        <td colspan="3">
          <hr style="margin-top: .75rem; padding-top: .25rem; border: none; border-top: 1px solid lightgray"/>
          <TodoAdd />
        </td>
      </tr>
    </tbody>
  </table>
</main>