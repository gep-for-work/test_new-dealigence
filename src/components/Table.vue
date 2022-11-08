<script setup lang="ts">
import { reactive } from "vue";

interface ITableItem {
  symbol: String;
  ts: String;
  bid: Number;
  ask: Number;
  mid: Number;
}

const table: any[] = reactive([]);

const socket = new WebSocket(`${import.meta.env.VITE_API_URL}/feedadv`);

socket.onopen = () => {
  socket.send(
    JSON.stringify({
      userKey: import.meta.env.VITE_API_KEY,
      symbol: import.meta.env.VITE_SYMBOLS,
    })
  );
};

socket.onmessage = (event) => {
  const response: ITableItem = JSON.parse(event.data);

  const existingSymbolIndex = table.findIndex((item: ITableItem) => {
    return item.symbol === response.symbol;
  });

  ~existingSymbolIndex
    ? (table[existingSymbolIndex] = response)
    : table.push(response);
};
</script>

<template>
  <table v-if="table.length">
    <thead>
      <tr>
        <th
          v-for="tableItemKey in Object.keys(table[0])"
          :key="`head-${tableItemKey}`"
        >
          {{ tableItemKey }}
        </th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="(tableItem, index) in table" :key="index">
        <td
          v-for="tableItemKey in Object.keys(tableItem)"
          :key="`${index}-${tableItemKey}`"
        >
          {{ tableItem[tableItemKey] }}
        </td>
      </tr>
    </tbody>
  </table>
</template>
