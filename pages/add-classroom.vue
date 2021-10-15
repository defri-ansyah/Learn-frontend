<template>
  <form @submit.prevent="createClassroom">
    <v-text-field type="text" label="Name" v-model="name"></v-text-field>
    <v-btn type="submit"> submit </v-btn>
  </form>
</template>

<script lang="ts">
import gql from "graphql-tag";
import { Component, Prop, Vue } from "vue-property-decorator";
import "vue-apollo";

@Component
export default class addClassroom extends Vue {
  @Prop() 
  name:string = '';

  createClassroom(event: { target: { reset: () => void }; }) {
    this.$apollo
      .mutate({
        mutation: gql`
          mutation ($name: String) {
            createClassroom(name: $name)
          }
        `,
        variables: {
          name: this.name,
        },
      })
      .then((data:any) => {
        event.target.reset();
      });
  }
}
// name: 'addClassroom',
// data() {
//   return {
//     name: '',
//   }
// },
// methods: {
//   createClassroom(event) {
//     this.$apollo.mutate({
//       mutation: gql`
//       mutation(
//         $name: String
//       ){
//         createClassroom (
//           name: $name
//         )
//       }
//       `,
//       variables: {
//         name: this.name,
//       }
//     }).then (data => {
//       event.target.reset()
//     })
//   }
// }
</script>
