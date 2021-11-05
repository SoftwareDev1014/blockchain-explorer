<template>
  <div class="home">
    <!-- <img alt="Vue logo" src="../assets/logo.png"> -->
    <!-- <HelloWorld msg="Welcome to Your Vue.js App"/> -->
    <h1>Etherscan</h1>
    <div style="display: flex; width: 800px; margin: auto; column-gap: 10px">
      <v-text-field
        outlined
        v-model="address"
        placeholder="Address of ehteriumn"
        style="width: 400px"
        dense
      />
      <v-btn color="success" @click="searchData" :loading="isLoading"
        >search</v-btn
      >
    </div>
    <div style="margin-top: 30px; padding:10px">
      <!-- <table>
        <thead>
          <tr>
            <th>No</th>
            <th>Txn Hash</th>
            <th>Method</th>
            <th>Block</th>
            <th>Age</th>
            <th>From</th>
            <th>To</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(item, index) in dataList" :key="item.hash">
            <td>{{ index + 1 }}</td>
            <td>{{ item.hash }}</td>
            <td>Transfer</td>
            <td>{{ item.blockNumber }}</td>
            <td>{{ getDifTime(new Date(parseInt(item.timeStamp * 1000))) }}</td>

            <td>{{ item.from }}</td>
            <td>{{ item.to }}</td>
          </tr>
        </tbody>
      </table> -->
      <v-text-field
        v-model="search"
        append-icon="mdi-magnify"
        label="Search"
        single-line
        hide-details
      ></v-text-field>
      <v-data-table
        :headers="dessertHeaders"
        :items="dataList"
        :single-expand="singleExpand"
        :expanded.sync="expanded"
        :loading="isLoading"
        item-key="name"
        :show-expand="false"
        class="elevation-1"
              :search="search"

      >
        <template v-slot:item.method>
          Transfer
        </template>
        <template v-slot:item.timeStamp="{ item }">
          {{ getDifTime(new Date(parseInt(item.timeStamp * 1000))) }}
        </template>
        <template v-slot:expanded-item="{ headers, item }">
          <td :colspan="headers.length">More info about {{ item.name }}</td>
        </template>
      </v-data-table>
    </div>
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: "Home",
  components: {},
  data: () => ({
    address: "0xaa7a9ca87d3694b5755f213b5d04094b8d0f0a6f",
    apiToken: "FEBJTKZM84BVS2ZE45RVKI8NXZDC761GMN",
    dataList: [],
    isLoading: false,
    search:"",
    expanded: [],
    singleExpand: false,
    dessertHeaders: [
      {
        text: "Txn Hash",
        align: "start",
        sortable: false,
        value: "hash",
      },
      { sortable: false, text: "Method", value: "method", default: "transfer" },
      { sortable: false, text: "Block", value: "blockNumber" },
      { sortable: false, text: "Age", value: "timeStamp" },
      { sortable: false, text: "From", value: "from" },
      { sortable: false, text: "To", value: "to" },
      { sortable: false, text: "Fee", value: "gasUsed" },
      // { sortable: false,text: '', value: 'data-table-expand' },
    ],
  }),
  methods: {
    async searchData() {
      this.isLoading = true;
      axios
        .get(
          `https://api.etherscan.io/api?module=account&action=txlist&address=${this.address}&startblock=0&endblock=99999999&sort=desc&apikey=${this.apiToken}`
        )
        .then(({ data }) => {
          console.log("data", data);
          this.dataList = data.result;
          this.isLoading = false;
        })
        .catch(() => {
          this.isLoading = false;
        });
    },
    getDifTime(time) {
      const endDate = new Date();
      const days = parseInt((endDate - time) / (1000 * 60 * 60 * 24));
      const hours = parseInt(
        (Math.abs(endDate - time) / (1000 * 60 * 60)) % 24
      );
      const minutes = parseInt(
        (Math.abs(endDate.getTime() - time.getTime()) / (1000 * 60)) % 60
      );
      // const seconds = parseInt(
      //   (Math.abs(endDate.getTime() - time.getTime()) / 1000) % 60
      // );

      return days != 0
        ? `${days} days `
        : "" + hours != 0
        ? `${hours} hours `
        : "" + `${minutes}minutes ago`;
    },
  },
};
</script>
<style>
table {
  font-family: arial, sans-serif;
  border-collapse: collapse;
  width: 100%;
}

td,
th {
  border: 1px solid #dddddd;
  text-align: left;
  padding: 8px;
}

tr:nth-child(even) {
  background-color: #dddddd;
}
</style>