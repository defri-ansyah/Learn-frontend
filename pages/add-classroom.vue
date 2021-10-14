<template>
  <form @submit.prevent="createClassroom">
    <v-text-field type="text" label="Name" v-model="name"></v-text-field>
    <v-btn type="submit"> submit </v-btn>
  </form>
</template>

<script>
import gql from "graphql-tag";

export default {
  name: 'addClassroom',
  data() {
    return {
      name: '',
    }
  },
  methods: {
    createClassroom(event) {
      this.$apollo.mutate({
        mutation: gql`
        mutation(
          $name: String
        ){
          createClassroom (
            name: $name
          )
        }
        `,
        variables: {
          name: this.name,
        }
      }).then (data => {
        event.target.reset()
      })
    }
  },
};
</script>
