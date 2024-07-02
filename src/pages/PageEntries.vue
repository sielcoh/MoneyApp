<template>
  <q-page>
    <div class="q-pa-md">
      <q-list bordered separator>
        <q-slide-item
          v-for="entry in entries"
          :key="entry.id"
          @right="onEntrySlideRight($event, entry)"
          left-color="positive"
          right-color="negative"
        >
          <template v-slot:right>
            <q-icon name="delete" />
          </template>

          <q-item>
            <q-item-section
              class="text-weight-bold"
              :class="useAmountColorClass(entry.amount)"
            >
              {{ entry.name }}
            </q-item-section>

            <q-item-section
              class="text-weight-bold"
              :class="useAmountColorClass(entry.amount)"
              side
            >
              {{ useCurrencify(entry.amount) }}
            </q-item-section>
          </q-item>
        </q-slide-item>
      </q-list>
    </div>

    <q-footer class="bg-transparent">
      <div class="row q-mb-sm q-px-md q-py-sm shadow-up-3">
        <div class="col text-grey-7 text-h6">Balance:</div>
        <div
          class="col text-h6 text-right"
          :class="useAmountColorClass(balance)"
        >
          {{ useCurrencify(balance) }}
        </div>
      </div>

      <q-form
        @submit="addEntry"
        class="row q-px-sm q-pb-sm q-col-gutter-sm bg-primary"
      >
        <div class="col">
          <q-input
            v-model="addEntryForm.name"
            outlined
            bg-color="white"
            dense
            placeholder="Name"
            ref="nameRef"
          />
        </div>
        <div class="col">
          <q-input
            v-model.number="addEntryForm.amount"
            type="number"
            step="0.01"
            outlined
            bg-color="white"
            dense
            placeholder="Amount"
            input-class="text-right"
          />
        </div>
        <div class="col col-auto">
          <q-btn round color="primary" icon="add" type="submit" />
        </div>
      </q-form>
    </q-footer>
  </q-page>
</template>

<script setup>
import { ref, computed, reactive } from "vue";
import { uid, useQuasar } from "quasar";
import { useCurrencify } from "src/use/useCurrencify.js";
import { useAmountColorClass } from "src/use/useAmountColorClass.js";

const $q = useQuasar();

const entries = ref([
  {
    id: 1,
    name: "Salary",
    amount: 4999.99,
  },
  {
    id: 2,
    name: "Rent",
    amount: -999,
  },
  {
    id: 3,
    name: "Phone",
    amount: -14.99,
  },
  {
    id: 4,
    name: "Unknown",
    amount: 0,
  },
]);

const balance = computed(() => {
  return entries.value.reduce((acc, { amount }) => {
    return acc + amount;
  }, 0);
});

const nameRef = ref(null);

const addEntryFormDefault = {
  name: "",
  amount: null,
};

const addEntryForm = reactive({
  ...addEntryFormDefault,
});

const addEntryFormReset = () => {
  Object.assign(addEntryForm, addEntryFormDefault);
  nameRef.value.focus();
};

const addEntry = () => {
  const newEntry = Object.assign({}, addEntryForm, { id: uid() });
  entries.value.push(newEntry);
  addEntryFormReset();
};

const deleteEntry = (entryId) => {
  const index = entries.value.findIndex((entry) => entry.id === entryId);
  entries.value.splice(index, 1);
  $q.notify({
    message: 'Entry Deleted',
    position: 'top'
  })
};

const onEntrySlideRight = ({ reset }, entry) => {
  $q.dialog({
    title: "Delete Entry",
    message: `
    Delete this entry?
    <div class= 'q-mt-md text-weight-bold ${useAmountColorClass(entry.amount)}'>
      ${entry.name} : ${useCurrencify(entry.amount)}
      </div>
    `,
    cancel: true,
    persistent: true,
    html: true,
    ok: {
      label: "Delete",
      color: "negative",
      noCaps: true,
    },
    cancel: {
      color: "primary",
      noCaps: true,
    },
  })
    .onOk(() => {
      deleteEntry(entry.id);
    })
    .onCancel(() => {
      reset();
    });
};
</script>
