# vue-serveside-datatable

ServerSide resuable vue resuable components

You can use this component by simply copy `datatable.vue` and add inside to your components folder in your project.

For using this components you need api endpoint that response should exactly look like this.

```json
{
  "currentPage": 1,
  "nextPage": 2,
  "totalNumberOfPage": 651,
  "currentPageData": 20,
  "totalData": 13010,
  "data": [
    {
      "name": "Ashish",
      "address": "Kathmandu",
      "email": "hello@gmail.com"
    },
    {
      "name": "Atul",
      "address": "kathmandu",
      "email": "helloa21@gmail.com"
    },
    {
      "name": "Kritish",
      "address": "kathmandu",
      "email": "hellokritish@gmail.com"
    }
  ]
}
```

> ### Props to Define
```html
<datatable
  :endpoint="tableData.endpoint"
  :columns="tableData.columns"
  :actions="tableData.actions"
  @deleteEvent="deleteEvent($event)"
  :parameters="tableData.params"
  :refresh="tableData.refresh"
></datatable>
```
> Props
> | props | Description | |
> |------------|-----------------------------------------------------------------------------------------------------------------------------------------------|---|
> | endpoint | This is endpoint for you api. Response Should look like above mentioned | |
> | columns | columns to show in table. It should be passed as array of object. object contains field , column as compulsory key and render as optional key | |
> | actions | This is also array of object. This is used for displaying delete and edit buttons | |
> | parameters | This is parameters to pass and this is done automatically in emit event. You just have to pass it in props as null | |
> | refresh | This is true and false toggle props to refresh datatable for reactivity | |
> | eventName | Same name that is passed action[index].event. This has method this is defined in methods in vue instance which is for certain click event like delete on click | |

### the above defined props is defined in data

```js
tableData: {
      params: null, //always define this as null
      refresh: true, //always define as either true or false
      endpoint: "http://localhost:3000/api/enquiry",
      columns: [
        {
          field: "name",
          /* field from api response. this should be same as in
          api response*/
          column: "name", //String to Display in datatable columns
          render: function(field) { //for rendering as per condition in datatable. Just like jquery render. This is optional
            return !field ? "ok" : field;
          }
        },
        {
          field: "address",
          column: "address",
          render: function(field) {
            return !field ? "..." : field;
          }
        },
        {
          field: "email",
          column: "Email",
          render: function(field) {
            return !field ? "<p class='has-text-primary'>No Email</p>" : field;
          }
        }
      ],
      actions: [
         /* This is for displaying buttons eg. delete/edit in datatable */
        {
          event: "editEvent",//<datatable @editEvent="methodToTrigger()">
          /* name of event to emit when this button is clicked.
          This emitted event contains _id and params(this is passed in
          parameters props when action button is clicked)  */
          /* After That you can call @editEvent="methodName($event)"
          for your click action in buttons. See below for deleteEvent code
          */
          class: "is-white has-text-primary px-2 py-0 mx-0 my-0",
          /* css class for button styling */
          value: `<span class="iconify" data-icon="ant-design:edit-filled" data-inline="false"></span>`
          /* For button content. */
        },
        {
          event: "deleteEvent", //<datatable @deleteEvent="methodToTrigger()">
          /* This event is implemented below methodToTrigger
          is named as deleteEvent */
          class: "is-white has-text-danger px-2 py-0 mx-0 my-0",
          value: `<span class="iconify" data-icon="ant-design:delete-filled" data-inline="false"></span>`
        }
      ]
    }
  }
```

#### After you have delete and edit buttons or your prefered actions buttons you can send axios request as mentioned below

```js
  methods: {
    deleteEvent: function(event) {
      axios
        .post("http://localhost:3000/api/enquiry/delete", { id: event.id })
        .then(res => {
          this.tableData.params = event.params;  //this params is from emitted event. Pass this to props named parameters

          this.tableData.refresh = !this.tableData.refresh; //just change value every time request is sent to refresh datatable
          // This refresh is passed as props name refresh.
        });
    }
  }
```

## Full code Implementation

```html
<template>
  <div>
    <sidebar>
      <datatable
        :endpoint="tableData.endpoint"
        :columns="tableData.columns"
        :actions="tableData.actions"
        @deleteEvent="deleteEvent($event)"
        :parameters="tableData.params"
        :refresh="tableData.refresh"
      ></datatable>
    </sidebar>
  </div>
</template>
```

```js
import datatable from "../../components/datatable";
import axios from "axios";
export default {
  components: {
    datatable,
  },
  data: () => ({
    tableData: {
      params: null,
      refresh: true,
      endpoint: "http://localhost:3000/api/enquiry",
      columns: [
        {
          field: "name",
          column: "name",
          render: function (field) {
            return !field ? "ok" : field;
          },
        },
        {
          field: "address",
          column: "address",
          render: function (field) {
            return !field ? "..." : field;
          },
        },
        {
          field: "email",
          column: "Email",
          render: function (field) {
            return !field ? "<p class='has-text-primary'>No Email</p>" : field;
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
        {
          event: "deleteEvent",
          class: "is-white has-text-danger px-2 py-0 mx-0 my-0",
          value: `<span class="iconify" data-icon="ant-design:delete-filled" data-inline="false"></span>`,
        },
      ],
    },
  }),
  methods: {
    deleteEvent: function (event) {
      axios
        .post("http://localhost:3000/api/enquiry/edit", { id: event.id })
        .then((res) => {
          this.tableData.show = true;
          // this.tableData.endpoint = "http://localhost:3000/api/enquiry";
          /* include these below two line of code everytime for reactivity */
          this.tableData.params = event.params;
          this.tableData.refresh = !this.tableData.refresh; //just change value every time request is sent to refresh datatable
        });
    },
  },
};
```
