<template>
  <form @submit.prevent="createStudent">
    <v-text-field type="text" label="Name" v-model="name"></v-text-field>
    <v-text-field type="text" label="Age" v-model="age"></v-text-field>
    <v-text-field type="text" label="Gender" v-model="gender"></v-text-field>
    <v-btn type="submit"> submit </v-btn>
  </form>
</template>

<script lang="ts">
import gql from "graphql-tag";
import { Component, Prop, Vue } from "vue-property-decorator";
import "vue-apollo";

@Component
export default class addStudent extends Vue {
  @Prop()
  name:string = ''
  gender!: string;
  age!: string;

  createStudent(event: { target: { reset: () => void } }) {
    this.$apollo
      .mutate({
        mutation: gql`
          mutation ($name: String, $age: Int, $gender: String) {
            createStudent(name: $name, age: $age, gender: $gender)
          }
        `,
        variables: {
          name: this.name,
          age: parseInt(this.age),
          gender: this.gender,
        },
      })
      .then((data: any) => {
        event.target.reset();
      });
  }
}

// export default {
//   name: 'addStudent',
//   data() {
//     return {
//       name: '',
//       age: 0,
//       gender: ''
//     }
//   },
//   methods: {
//     createStudent(event) {
//       this.$apollo.mutate({
//         mutation: gql`
//         mutation(
//           $name: String
//           $age: Int
//           $gender: String
//         ){
//           createStudent (
//             name: $name
//             age: $age
//             gender: $gender
//           )
//         }
//         `,
//         variables: {
//           name: this.name,
//           age: parseInt(this.age),
//           gender: this.gender
//         }
//       }).then (data => {
//         event.target.reset()
//       })
//     }
//   },
// };
</script>
