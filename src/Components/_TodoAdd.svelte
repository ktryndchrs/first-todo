<script>
  import {clickOutside} from '../Extensions/_ClickOutside.js';
  import client from "../graphql-client";
  import gql from 'graphql-tag';

  // export let todo = "";
  
  let NewTodo = {
    name: ""
  };

  async function insertTodo(e){
    if (NewTodo.name != ""){
      const mutation = gql`
        mutation insert_todo($name: String!) {
          insert_todo(
            objects: {
              name: $name
            }
          ){
            returning {
              name
            }
          }
        }
      `
      client.mutate({
        mutation,
        variables:{
          name: NewTodo.name
        }
      })
      NewTodo.name = ""
    };
  };
  
</script>

<form on:submit|preventDefault={insertTodo}>
  <input type="text" placeholder="New Todo Name" bind:value={NewTodo.name}/>
  <button disabled={NewTodo.name == "" ? "disabled" : ""} type="submit">Add Todo</button>
</form>