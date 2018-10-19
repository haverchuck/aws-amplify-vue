<template>
  <div class="home">
      <amplify-connect 
        :query="graphqlQuery"
        :subscription="createTodoSubscription"
        :onSubscriptionMsg="onCreateTodo"
      >
        <template slot-scope="{loading, data, errors}">
          <div v-if="loading">Loading...</div>
           <div v-else-if="errors.length > 0">{{ errors }}</div>
           <div v-else-if="data">
              <a-notes :notes="data.listTodos.items" />
          </div>
        </template>
      </amplify-connect>
  </div>
</template>

<script>
  import Vue from 'vue';
  import  { CreateTodo, ListTodos, UpdateTodo, DeleteTodo, SubscribeTodosCreate }  from './persist/graphqlActions';
  import  Notes from './Notes';


Vue.component('a-notes', Notes)

  export default {
    name: 'NotesConnect',
    data() {
      return {
        graph: null,
        actions: {
          list: ListTodos,
          subscribe: SubscribeTodosCreate
        }
      }
    },
    created() {
      this.graph = this.$Amplify.graphqlOperation;
    },
    computed: {
      graphqlQuery() {
        return this.graph(this.actions.list);
      },
      createTodoSubscription() {
        return this.graph(this.actions.subscribe);
      },
    },
    methods: {
      onCreateTodo(prevData, newData) {
        console.log('New todo from subscription...');
        const newTodo = newData.onCreateTodo;
        prevData.data.listTodos.items.push(newTodo);
        return prevData.data;
      }
    }
  };
</script>