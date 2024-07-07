<script setup lang="ts">
import { ref } from "vue";
import { Account } from "./types";
import AddAccount from "./components/AddAccount.vue";
import CreatePayment from "./components/CreatePayment.vue";
import { watchEffect } from "vue";

function getPersistedAccounts() {
  try {
    const json = localStorage.getItem("payment-barcode-accounts") ?? "[]";
    const accounts = JSON.parse(json);
    return new Map(accounts) as Map<Account["iban"], Account>;
  } catch {
    return new Map() as Map<Account["iban"], Account>;
  }
}

const accountsByIban = ref(
  new Map<Account["iban"], Account>(getPersistedAccounts())
);

watchEffect(() => {
  try {
    localStorage.setItem(
      "payment-barcode-accounts",
      JSON.stringify(Array.from(accountsByIban.value.entries()))
    );
  } finally {
  }
});
</script>

<template>
  <AddAccount
    @addAccount="(account) => accountsByIban.set(account.iban, account)"
  />
  <CreatePayment :accountsByIban />
</template>

<style scoped>
.logo {
  height: 6em;
  padding: 1.5em;
  will-change: filter;
  transition: filter 300ms;
}
.logo:hover {
  filter: drop-shadow(0 0 2em #646cffaa);
}
.logo.vue:hover {
  filter: drop-shadow(0 0 2em #42b883aa);
}
</style>
