<template>
  <datatable
    :endpoint="tableData.endpoint"
    :columns="tableData.columns"
    :actions="tableData.actions"
    @editEvent="edit($event)"
    @deleteEvent="del($event)"
    :parameters="tableData.params"
    :refresh="tableData.refresh"
  ></datatable>
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
      actions: [
        {
          event: "editEvent",
          class: "is-white has-text-primary px-2 py-0 mx-0 my-0",
          value: `<span class="iconify" data-icon="ant-design:edit-filled" data-inline="false"></span>`,
        },
        {
          event: "deleteEvent",
          class: "is-white has-text-danger px-2 py-0 mx-0 my-0",
          value: `<span class="iconify" data-icon="ant-design:delete-filled" data-inline="false"></span>`,
        },
      ],
    },
  }),
  methods: {
    edit: function (event) {
      alert(`edit event on id=> ${event.id}`);
    },
    del: function (event) {
      alert(`delete request on id=> ${event.id}`);
    },
  },
};
</script>

<style>
</style>