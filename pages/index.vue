<template>
  <div>
    <!-- <h1>Data Students</h1>
    <ul>
      <li v-for="student in getStudent" :key="student.id">
        {{ student.name }}
        {{ student.age }}
        {{ student.gender }}
        Classroom: {{ student.classroom ? student.classroom.name : ''}}
      </li>
    </ul> -->
    <div>
      <v-card-title>
        <v-text-field
          v-model="search"
          append-icon="mdi-magnify"
          label="Search"
          single-line
          hide-details
        ></v-text-field>
        <v-label class="m-100" for="classroom">Choose Classroom:</v-label>
        <select v-model="selectedClass" id="classroom">
          <option value="">No filter</option>
          <option
            v-for="classroom in getAllClassroom"
            :key="classroom.id"
            v-bind:value="classroom.id"
          >
            {{ classroom.name }}
          </option>
        </select>
      </v-card-title>
      <v-data-table
        :headers="headers"
        :items="getStudent"
        :search="search"
        :options.sync="options"
        :server-items-length="totalData"
        :loading="loading"
        :footer-props="{
          'items-per-page-options': [1, 2, 5, 10, -1],
        }"
        class="elevation-1"
      ></v-data-table>
    </div>
  </div>
</template>

<script>
import gql from "graphql-tag";

export default {
  data() {
    return {
      student: [],
      search: "",
      totalData: 10,
      loading: false,
      options: {},
      selectedClass: 0,
      headers: [
        {
          text: "Name",
          align: "start",
          value: "name",
        },
        { text: "Age", value: "age" },
        { text: "Gender", value: "gender" },
        { text: "Classroom", value: "classroom.name", sortable: false },
      ],
    };
  },
  // watch: {
  //   options: {
  //     handler() {
  //       this.getStudent()
  //       console.log(this.options)
  //     },
  //     deep: true,
  //   },
  // },
  // created() {
  //   this.getStudent()
  // },
  // methods: {
  //   getStudent(event) {
  //     this.$apollo.query({
  //       query: gql`
  //         query getStudent($where: Filter, $sort: sortBy) {
  //           getStudent(where: $where, sort: $sort) {
  //             id
  //             name
  //             age
  //             gender
  //             classroom {
  //               name
  //             }
  //           }
  //         }
  //       `,
  //       variables() {
  //         const { sortBy, sortDesc, page, itemsPerPage } = this.options;
  //         let sort = null;
  //         let sortValues = sortDesc[0] ? "DESC":"ASC"
  //         if (sortBy && sortDesc) {
  //           sort = { [sortBy[0]]: sortValues };
  //         }
  //         return {
  //           where: this.where,
  //           sort: sort,
  //         };
  //       },
  //     }).then (data => {
  //       // console.log(data);
  //       this.student = data.data.getStudent
  //     })
  //   },
  // },
  apollo: {
    getStudent: {
      query: gql`
        query getStudent(
          $where: Filter
          $sort: sortBy
          $offset: Int
          $limit: Int
          $search: String
        ) {
          getStudent(
            where: $where
            sort: $sort
            offset: $offset
            limit: $limit
            search: $search
          ) {
            id
            name
            age
            gender
            classroom {
              name
            }
          }
        }
      `,
      variables() {
        let { sortBy, sortDesc, page, itemsPerPage } = this.options;
        const options = {
          limit: itemsPerPage > 0 ? itemsPerPage : this.totalData,
          offset: page * itemsPerPage - itemsPerPage,
        };
        if ((sortBy?.length ?? 0) && (sortDesc?.length ?? 0)) {
          let sortValues = sortDesc[0] ? "ASC" : "DESC";
          const sort = { [sortBy]: sortValues };
          options.sort = sort;
        }
        if (this.search !== "") {
          options.search = this.search;
        }
        if (this.selectedClass != 0 && this.selectedClass != null) {
          options.where = { classroom_id: parseInt(this.selectedClass) };
        }
        return options;
      },
    },
    getAllClassroom: gql`
      query classroom {
        getAllClassroom {
          id
          name
        }
      }
    `,
  },
};
</script>

<style lang="scss" scoped>
select {
  color: white;
}
</style>