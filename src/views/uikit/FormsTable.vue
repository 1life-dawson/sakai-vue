<script setup>
import { faker } from '@faker-js/faker';
import { FilterMatchMode } from '@primevue/core/api';
import { onBeforeMount, reactive, ref } from 'vue';

function generateRandomInformationArray() {
    let set = new Set();
    const numTtems = Math.floor(Math.random() * 4);
    for (let index = 0; index <= numTtems; index++) {
        set.add(icons[Math.floor(Math.random() * 4)]);
    }
    return [...set];
}

const statuses = ['Assigned', 'Submitted', 'Reviewed'];
function generateRandomStatus() {
    return statuses[Math.floor(Math.random() * statuses.length)];
}

function createRandomForm() {
    return {
        formTitle: faker.lorem.words(2),
        formProject: faker.company.name(),
        formCreatedDate: faker.date.recent().toISOString().split('T')[0],
        formLastUpdate: faker.date.recent().toISOString().split('T')[0],
        formStatus: generateRandomStatus(),
        formInformation: generateRandomInformationArray()
    };
}

const forms = reactive([]);
const icons = ['pi pi-face-smile', 'pi pi-expand', 'pi pi-crown', 'pi pi-clipboard'];
const filters = ref({
    global: { value: null, matchMode: FilterMatchMode.CONTAINS },
    formTitle: { value: null, matchMode: FilterMatchMode.CONTAINS }
});

onBeforeMount(() => {
    for (let index = 0; index < 101; index++) {
        forms.push(createRandomForm());
    }
});
</script>

<template>
    <div class="card">
        <DataTable v-model:filters="filters" :rows="5" :rowsPerPageOptions="[5, 10, 20, 50]" :value="forms" filterDisplay="row" paginator>
            <template #header>
                <IconField>
                    <InputIcon>
                        <i class="pi pi-search" />
                    </InputIcon>
                    <InputText v-model="filters['formTitle'].value" placeholder="Title search" />
                </IconField>
            </template>
            <template #empty> No forms found. </template>
            <template #loading> Loading forms data. Please wait. </template>
            <Column header="Title" field="formTitle" sortable> </Column>
            <Column header="Project" field="formProject" sortable></Column>
            <Column header="Created Date" field="formCreatedDate" sortable></Column>
            <Column header="Updated Date" field="formLastUpdate" sortable></Column>
            <Column header="Status" field="formStatus" sortable></Column>
            <Column header="Information" field="formInformation" sortable>
                <template #body="slotProps">
                    <span v-for="(icon, index) in slotProps.data.formInformation" :key="index" :class="icon"></span>
                </template>
            </Column>
        </DataTable>
    </div>
</template>

<style scoped lang="scss">
:deep(.p-datatable-frozen-tbody) {
    font-weight: bold;
}

:deep(.p-datatable-scrollable .p-frozen-column) {
    font-weight: bold;
}
</style>
