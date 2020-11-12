<template>
  <div>
    <div class="card table-container">
      <div class="px-3 py-3">
        <div class="card-header pb-2">
          <div class="search-and-select columns">
            <div class="has-select column is-8">
              <span class="pr-1">Show</span>
              <div class="select is-small">
                <select @change="onChange($event)">
                  <option value="10">10</option>
                  <option value="25">25</option>
                  <option value="50">50</option>
                </select>
              </div>
              <span class="pl-1">Entry</span>
            </div>
            <div class="has-search column is-4">
              <div class="control has-icons-left">
                <input
                  class="input is-small"
                  type="text"
                  v-model="tableData.params.searchedText"
                  @keyup="searchData()"
                  placeholder="Search"
                />
                <span class="icon is-small is-left">
                  <span
                    class="iconify"
                    data-icon="ant-design:search-outlined"
                    data-inline="false"
                  ></span>
                </span>
              </div>
            </div>
          </div>
        </div>
        <table class="table is-fullwidth">
          <thead>
            <tr>
              <th v-for="i in columns" :key="i.column" @click="sortBy(i.field)">
                <span>{{ i.column }}</span>
              </th>
              <th>Action</th>
            </tr>
          </thead>
          <tbody>
            <tr v-if="!tableData.data.totalData">
              <td :colspan="columns.length + 1" class="has-text-centered">
                No data available in table
              </td>
            </tr>
            <tr v-else v-for="item in tableData.data.data" :key="item._id">
              <td
                class="has-text-weight-medium"
                v-for="i in columns"
                :key="i.column"
                v-html="renderData(i, item)"
              ></td>

              <td class="actions">
                <a
                  v-for="el in actions"
                  :key="el.event"
                  :class="el.class"
                  @click="
                    editEvent(el.event, {
                      id: item._id,
                      params: tableData.params,
                    })
                  "
                >
                  <span v-html="el.value"></span>
                  <!-- <span class="iconify" data-icon="ant-design:edit-filled" data-inline="false"></span> -->
                </a>
                <slot v-bind="{ item, params: tableData.params }"></slot>
              </td>
            </tr>
          </tbody>
          <tfoot>
            <tr>
              <td colspan="1">
                <span
                  >Showing {{ startPoint }} to
                  {{ startPoint + tableData.data.currentPageData - 1 }} of
                  {{ tableData.data.totalData }}</span
                >
              </td>
              <td colspan="4">
                <div
                  class="pagination is-small"
                  role="navigation"
                  aria-label="pagination"
                >
                  <ul class="pagination-list">
                    <li @click="anotherPage(1)">
                      <a
                        class="pagination-link"
                        :class="{
                          'pagination-ellipsis':
                            tableData.data.currentPage == 1,
                        }"
                        >First</a
                      >
                    </li>
                    <li @click="anotherPage(tableData.data.prevPage)">
                      <a
                        class="pagination-link"
                        :class="{
                          'pagination-ellipsis':
                            tableData.data.currentPage == 1,
                        }"
                        >Prev</a
                      >
                    </li>
                    <li
                      @click="anotherPage(n)"
                      v-for="n in tableData.params.paginationLinks"
                      :key="n"
                    >
                      <a
                        class="pagination-link"
                        :class="{ 'is-current': tableData.params.page === n }"
                        aria-label="Goto page 1"
                        >{{ n }}</a
                      >
                    </li>
                    <li @click="anotherPage(tableData.data.nextPage)">
                      <a
                        class="pagination-next"
                        :class="{
                          'pagination-ellipsis':
                            tableData.data.currentPage ==
                            tableData.data.totalNumberOfPage,
                        }"
                        >next</a
                      >
                    </li>
                    <li @click="anotherPage(tableData.data.totalNumberOfPage)">
                      <a
                        class="pagination-next"
                        :class="{
                          'pagination-ellipsis':
                            tableData.data.currentPage ==
                            tableData.data.totalNumberOfPage,
                        }"
                        >Last</a
                      >
                    </li>
                  </ul>
                </div>
              </td>
              <td v-if="!columns.length >= 5"></td>
              <td v-else v-for="i in columns.length - 4" :key="i"></td>
            </tr>
          </tfoot>
        </table>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: ["columns", "endpoint", "actions", "parameters", "refresh"],
  data: () => ({
    data: "Hello World",
    tableData: {
      params: {
        page: 1,
        limit: 10,
        sortBy: "createdAt",
        order: "desc",
        searchedText: "",
        totalPage: "",
        paginationLinks: [],
        loading: true,
      },
      data: [],
      url: "",
    },
  }),
  methods: {
    editEvent: function (eventName, id) {
      this.$emit(eventName, id);
    },
    sortBy: function (fieldName) {
      this.tableData.params.sortBy = fieldName;
      this.tableData.params.order =
        this.tableData.params.order === "asc" ? "desc" : "asc";
      this.fetchPage();
    },
    onChange(event) {
      this.tableData.params.limit = event.target.value;
      this.fetchPage();
    },
    anotherPage: function (id) {
      if (!id) return;
      this.tableData.params.loading = true;
      this.tableData.params.page = id;
      this.fetchPage();
    },
    range: function (start, stop, step) {
      //like python range function
      var a = [start],
        b = start;
      while (b < stop) {
        a.push((b += step || 1));
      } //status
      return b > stop ? a.slice(0, -1) : a;
    },
    showPageNumber: function (currentPage, totalPage) {
      this.tableData.params.paginationLinks = [];
      var current = currentPage;
      var pages_to_show = 5;
      var end;
      var start;
      if (totalPage < pages_to_show) {
        start = 1;
        end = totalPage;
      } else {
        if (current > Math.ceil(pages_to_show / 2)) {
          start =
            current - Math.floor(pages_to_show / 2) == 0
              ? 1
              : current - Math.floor(pages_to_show / 2);
          end =
            current + Math.floor(pages_to_show / 2) >= totalPage
              ? totalPage
              : current + Math.floor(pages_to_show / 2);
        } else {
          start = 1;
          end = pages_to_show;
        }
        if (end - start < pages_to_show - 1) {
          start = end - (pages_to_show - 1);
        }
      }
      this.tableData.params.paginationLinks = this.range(start, end);
    },
    fetchPage: async function () {
      if (this.parameters) {
        this.tableData.params = {
          ...this.tableData.params,
          ...this.parameters,
        };
      }
      this.tableData.url =
        this.endpoint +
        `?limit=${this.tableData.params.limit}&page=${this.tableData.params.page}&sortBy=${this.tableData.params.sortBy}&order=${this.tableData.params.order}&searchText=${this.tableData.params.searchedText}`;
      var res = await this.$axios.get(this.tableData.url);
      this.showPageNumber(
        this.tableData.params.page,
        res.data.totalNumberOfPage
      );
      this.tableData.data = res.data;
      setTimeout(
        function () {
          this.tableData.params.loading = false;
        }.bind(this),
        900
      );
    },
    searchData() {
      this.tableData.params.page = 1;
      this.tableData.params.loading = true;
      this.fetchPage();
    },
    renderData: function (i, item) {
      return "render" in i ? i.render(item[i.field]) : item[i.field];
    },
  },
  watch: {
    refresh: function (newval, oldVal) {
      this.fetchPage();
    },
  },
  computed: {
    startPoint: function () {
      return (this.tableData.params.page - 1) * this.tableData.params.limit + 1;
    },
  },
  async created() {
    this.fetchPage();
  },
};
</script>

<style lang="scss">
.search-and-select {
  width: 100%;
}
.custom-table {
  thead {
    tr {
      cursor: pointer;
      th {
        padding: 12px !important;
        svg {
          text-align: left;
          cursor: pointer;
        }
      }
    }
  }
  tbody {
    tr {
      td.actions {
        font-size: 1.3rem;
        width: 15%;
      }
    }
  }
  tfoot {
    .pagination-list {
      justify-content: flex-end;
    }
  }
}
.pagination ul li > a {
  margin: 0.25rem 1px !important;
}
</style>