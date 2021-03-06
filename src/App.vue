<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png" class="logo">
    <div class="table-container">
      <div v-if="!tableResponse" class="lds-circle">
        <div></div>
      </div>
      <div v-else>
        <Table :tableData="dataOnPage" @to-sort-table="sort"></Table>
        <Pagination
          :tableItemsLength="tableItemsLength"
          :currentPage="currentPage"
          :itemsPerPage="itemsPerPage"
          @change-page="changePage"
        ></Pagination>
      </div>
    </div>
  </div>
</template>

<script>
import store from "./store";
import Table from "./components/Table.vue";
import Pagination from "./components/Pagination.vue";

import { mapState } from "vuex";

export default {
  name: "app",
  store,
  components: {
    Table,
    Pagination
  },
  data() {
    return {
      currentPage: 0,
      itemsPerPage: 10,
      sortTitle: "id",
      sortDir: "asc"
    };
  },
  created() {
    console.log("here we dispatch action");
    this.$store.dispatch("table/getTableData");
    // mapActions("table", ["getTableData"]);
  },
  computed: {
    ...mapState({
      table: state => state.table.data,
      tableResponse: state => state.table.rendered
    }),
    dataOnPage() {
      const start = this.currentPage * this.itemsPerPage,
        end = start + this.itemsPerPage,
        paginatedData = this.sortedTable.slice(start, end);
      return paginatedData;
    },
    tableItemsLength() {
      return this.table.length;
    },
    sortedTable() {
      // .slice() for avoid side effects in computed properties
      return this.table.slice().sort((a, b) => {
        let modifier = 1;
        if (this.sortDir === "desc") modifier = -1;
        if (a[this.sortTitle] < b[this.sortTitle]) return -1 * modifier;
        if (a[this.sortTitle] > b[this.sortTitle]) return 1 * modifier;
        return 0;
      });
    }
  },
  methods: {
    changePage(page) {
      this.currentPage = page;
    },
    sort(s) {
      if (s === this.sortTitle) {
        this.sortDir = this.sortDir === "asc" ? "desc" : "asc";
      }
      this.sortTitle = s;
    }
  }
};
</script>

<style>
.logo {
  display: block;
  margin: 0 auto 10px;
  width: 100px;
}
.table-container {
  display: flex;
  align-items: center;
  justify-content: center;
  min-height: 300px;
}
/* preloader */
.lds-circle {
  display: inline-block;
  transform: translateZ(1px);
}
.lds-circle > div {
  display: inline-block;
  width: 51px;
  height: 51px;
  margin: 6px;
  border-radius: 50%;
  background: #cef;
  animation: lds-circle 2.4s cubic-bezier(0, 0.2, 0.8, 1) infinite;
}
@keyframes lds-circle {
  0%,
  100% {
    animation-timing-function: cubic-bezier(0.5, 0, 1, 0.5);
  }
  0% {
    transform: rotateY(0deg);
  }
  50% {
    transform: rotateY(1800deg);
    animation-timing-function: cubic-bezier(0, 0.5, 0.5, 1);
  }
  100% {
    transform: rotateY(3600deg);
  }
}
</style>
