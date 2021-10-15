<template>
  <div>
    <h3>Assign Student To Classroom</h3>
    <!-- <form @submit.prevent="assignStudentToClassroom">
      <v-row align="center">
        <v-col class="d-flex" cols="12" sm="6">
          <v-select v-model="selectedStudent" label="Select Student" dense solo>
            <option v-for="student in getStudent" :key="student.id" v-bind:value="student.id"> {{student.name}} </option>
          </v-select>
        </v-col>
        <v-col class="d-flex" cols="12" sm="6">
          <v-select v-model="selectedCLass" label="Select Classroom" dense solo>
            <option v-for="classroom in getAllClassroom" :key="classroom.id" v-bind:value="classroom.id"> {{classroom.name}} </option>
          </v-select>
        </v-col>
      </v-row>
      <v-btn type="submit"> submit </v-btn>
    </form> -->
    <form @submit.prevent="assignStudentToClassroom">
      <label for="student">Choose Student:</label>
      <select v-model="selectedStudent" id="student">
        <option
          v-for="student in getStudent"
          :key="student.id"
          v-bind:value="student.id"
        >
          {{ student.name }}
        </option>
      </select>

      <label for="classroom">Choose Classroom:</label>
      <select v-model="selectedClass" id="classroom">
        <option
          v-for="classroom in getAllClassroom"
          :key="classroom.id"
          v-bind:value="classroom.id"
        >
          {{ classroom.name }}
        </option>
      </select>
      <v-btn type="submit"> submit </v-btn>
    </form>
  </div>
</template>

<script lang="ts">
import gql from "graphql-tag";
import { Component, Prop, Vue, Provide } from "vue-property-decorator";
import "vue-apollo";

@Component
export default class assign extends Vue {
  @Prop()
  selectedStudent = "";
  selectedClass = "";
  getStudent = [];
  getAllClassroom = [];

  assignStudentToClassroom(event: { target: { reset: () => void } }) {
    this.$apollo
      .mutate({
        mutation: gql`
          mutation ($student_id: ID!, $classroom_id: ID!) {
            assignStudentToClassroom(
              student_id: $student_id
              classroom_id: $classroom_id
            )
          }
        `,
        variables: {
          student_id: parseInt(this.selectedStudent),
          classroom_id: parseInt(this.selectedClass),
        },
      })
      .then((data: any) => {
        event.target.reset();
      });
  }

  handleGet() {
    this.$apollo.addSmartQuery("getStudent", {
      query: gql`
        query student {
          getStudent {
            id
            name
            age
            gender
          }
        }
      `,
    });
      this.$apollo.addSmartQuery("getAllClassroom", {
        query: gql`
          query classroom {
            getAllClassroom {
              id
              name
            }
          }
        `,
      });
  }

  created() {
    this.handleGet();
  }
}

// export default {
//   name: "assign",
//   data() {
//     return {
//       selectedStudent: 0,
//       selectedClass: 0
//     }
//   },
//   apollo: {
//     getStudent: gql`
//       query student {
//         getStudent {
//           id
//           name
//           age
//           gender
//         }
//       }
//     `,
//     getAllClassroom: gql`
//       query classroom {
//         getAllClassroom {
//           id
//           name
//         }
//       }
//     `,
//   },
//   methods: {
//     assignStudentToClassroom(event) {
//       this.$apollo.mutate({
//         mutation: gql`
//         mutation(
//           $student_id: ID!
//           $classroom_id: ID!
//         ){
//           assignStudentToClassroom (
//             student_id: $student_id
//             classroom_id: $classroom_id
//           )
//         }
//         `,
//         variables: {
//           student_id: parseInt(this.selectedStudent),
//           classroom_id: parseInt(this.selectedClass),
//         }
//       }).then (data => {
//         event.target.reset()
//       })
//     }
//   },
// };
</script>

<style scoped>
select {
  color: white;
}
</style>