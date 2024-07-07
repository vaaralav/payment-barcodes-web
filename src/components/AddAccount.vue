<template>
  <form @submit.prevent="addAccount">
    <h2>Add account</h2>
    <label>
      name
      <input type="text" v-model="fields.name" />
    </label>
    <label>
      iban
      <input type="text" v-model="fields.iban" />
    </label>
    <label>
      reference
      <input type="text" v-model="fields.reference" />
    </label>

    <button type="submit">Add new account</button>
  </form>
</template>
<script setup lang="ts">
import { ref } from "vue";
import { Account } from "../types";
import finnishBankUtils from "finnish-bank-utils";

const fields = ref<Account>({
  name: "",
  iban: "",
  reference: null,
});

const emit = defineEmits<{
  (e: "addAccount", account: Account): void;
}>();

function addAccount() {
  if (!finnishBankUtils.isValidFinnishIBAN(fields.value.iban)) {
    return alert("Invalid IBAN");
  }

  if (
    !!fields.value.reference &&
    !finnishBankUtils.isValidFinnishRefNumber(fields.value.reference)
  ) {
    return alert("Invalid reference");
  }

  emit("addAccount", {
    ...fields.value,
    reference: !!fields.value.reference ? fields.value.reference : null,
  });
}
</script>
