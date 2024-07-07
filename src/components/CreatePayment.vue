<template>
  <div>
    <h2>Create payment barcode</h2>
    <label>
      Account
      <select v-model="selectedAccount">
        <option
          v-for="account in props.accountsByIban.values()"
          :value="account.iban"
        >
          {{ account.name }} ({{ account.iban }})
        </option>
      </select>
    </label>
    <label>
      Reference
      <input type="text" v-model="reference" />
    </label>
    <label>
      Amount
      <input type="number" step="0.01" v-model="amount" placeholder="1.00" />
    </label>
    <label>
      Due date
      <input type="date" v-model="dueDate" />
    </label>
    <div v-show="!!barcode">
      <canvas ref="canvasRef" id="barcode" />
      <pre>{{ barcode }}</pre>
    </div>
  </div>
</template>
<script setup lang="ts">
import { ref } from "vue";
import { Account } from "../types";
import { watchEffect } from "vue";
import { computed } from "vue";
import finnishBankUtils from "finnish-bank-utils";
import JsBarcode from "jsbarcode";

const props = defineProps<{
  accountsByIban: Map<Account["iban"], Account>;
}>();
const amount = ref<number>(0);
const dueDate = ref<string>("");
const reference = ref<string>("1009");

const selectedAccount = ref<string | null>(null);

const barcode = computed(() => {
  const obj = {
    iban: selectedAccount.value,
    sum: Number(Number(amount.value).toFixed(2)),
    reference: reference.value,
    date: new Date(dueDate.value).toLocaleDateString("fi-FI"),
  };
  console.log(obj);
  return finnishBankUtils.formatFinnishVirtualBarCode(obj);
});

const canvasRef = ref<HTMLCanvasElement | null>(null);

watchEffect(() => {
  const iban = selectedAccount.value;
  if (iban) {
    const account = props.accountsByIban.get(iban);
    reference.value = account?.reference ?? "1009";
  }
});

watchEffect(() => {
  if (canvasRef.value && barcode.value) {
    JsBarcode("#barcode", barcode.value);
  }
  console.log(barcode.value);
});
</script>
