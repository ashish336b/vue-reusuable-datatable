<template>
  <datatable
    :endpoint="tableData.endpoint"
    :columns="tableData.columns"
    :parameters="tableData.params"
    :refresh="tableData.refresh"
  >
    <template slot-scope="item">
      <a class="button is-primary is-small" @click="edit(item)">Edit</a>
      <a class="button is-danger is-small" @click="del(item)">Delete</a>
    </template>
  </datatable>
</template>

<script>
import datatable from "../components/datatable";
export default {
  components: {
    datatable,
  },
  data: () => ({
    tableData: {
      params: null,
      refresh: true,
      endpoint: "https://app.medmandu.life/api/medicine",
      //no get params needed to pass here
      columns: [
        {
          field: "name",
          column: "name",
          render: function (field) {
            return !field ? "ok" : field;
          },
        },
        {
          field: "group_name",
          column: "Group Name",
          render: function (field) {
            return !field ? "..." : field;
          },
        },
        {
          field: "composition",
          column: "Composition",
          render: function (field) {
            return !field ? "..." : field;
          },
        },
        {
          field: "type",
          column: "type",
          render: function (field) {
            return !field ? "<p class='has-text-primary'>..</p>" : field;
          },
        },
      ],
    },
  }),
  methods: {
    edit: function (event) {
      alert(`edit action here`);
      this.tableData.refresh = !this.tableData.refresh;
    },
    del: function (event) {
      alert(`delete action here`);
      this.tableData.refresh = !this.tableData.refresh;
    },
  },
};
</script>

<style>
</style>