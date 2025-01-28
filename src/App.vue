<template>
  <v-container>

    <v-card class="d-flex justify-space-between" gap-2>
      <v-text-field 
        v-model="searchQuery" 
        :loading="loading" 
        append-inner-icon="mdi-magnify" 
        density="compact"
        label="Search templates" 
        variant="solo" 
        hide-details 
        single-line
      ></v-text-field>
      <v-btn @click="openDialog">ADD</v-btn>
    </v-card>
    <br>


    <v-data-table 
  :headers="headers" 
  :items="filteredPayload" 
  item-value="id" 
  class="elevation-1" 
  items-per-page="5"
>
  <template #[`item.image`]="{ item }">
    <v-img :src="item.image" max-height="50" max-width="50" alt="Product Image"></v-img>
  </template>

 
  <template #[`item.count`]="{ item }">
    <v-chip :style="{ backgroundColor: item.count < 300 ? '#678515' : '#156a85' }" class="text-white">
        {{ item.count }}
    </v-chip>
</template>



 
  <template #[`item.actions`]="{ item }">
    <v-icon class="me-2" size="small" @click="editItem(item)">
      mdi-pencil
    </v-icon>
    <v-icon size="small" @click="deleteItem(item)">
      mdi-delete
    </v-icon>
  </template>


  <template v-slot:no-data>
    <v-btn color="primary" @click="fetchData">Reload Data</v-btn>
  </template>
</v-data-table>


    <v-dialog v-model="dialog" max-width="500">
      <v-card>
        <v-card-title>{{ formTitle }}</v-card-title>
        <v-card-text>
          <v-container>
            <v-row>
              <v-col cols="12" sm="6" md="4" v-for="(value, key) in editedItem" :key="key">
                <v-text-field 
                  v-model="editedItem[key]" 
                  :label="capitalize(key)" 
                  :type="key === 'year' || key === 'price' ? 'number' : 'text'"
                ></v-text-field>
              </v-col>
            </v-row>
          </v-container>
        </v-card-text>
        <v-card-actions>
          <v-btn @click="save">Save</v-btn>
          <v-btn text @click="closeDialog">Cancel</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>


    <v-dialog v-model="dialogDelete" max-width="500">
      <v-card>
        <v-card-title class="text-h5">Are you sure you want to delete this item?</v-card-title>
        <v-card-actions>
          <v-btn text @click="deleteItemConfirm">Yes</v-btn>
          <v-btn text @click="closeDeleteDialog">No</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-container>
</template>

<script setup>
import { ref, reactive, computed, onMounted } from "vue";
import axios from "axios";


const dialog = ref(false);
const dialogDelete = ref(false);
const loading = ref(false);

const headers = [
  { title: "ID", key: "id" },
  { title: "Title", key: "title" },
  { title: "Price", key: "price" },
  { title: "Description", key: "description" },
  { title: "Category", key: "category" },
  { title: "Image", key: "image" },
  { title: "Rate", key: "rate" },
  { title: "Count", key: "count" },
  { title: "Actions", key: "actions", sortable: false },
];



const payload = ref([]);
const defaultItem = {
  name: "",
  address: "",
  zip: 0,
  country: "",
  employeeCount: 0,
  industry: "",
  marketCap: 0,
  domain: "",
  ceoName: "",
};


const editedItem = reactive({ ...defaultItem });
const editedIndex = ref(-1);

const formTitle = computed(() =>
  editedIndex.value === -1 ? "New Item" : "Edit Item"
);

const searchQuery = ref ("");
const filteredPayload = computed(() => {
  if (!searchQuery.value) return payload.value;

  const query = searchQuery.value.toLowerCase();
  return payload.value.filter((item) =>
    Object.values(item).some(
      (value) =>
        value &&
        value.toString().toLowerCase().includes(query)
    )
  );
});



const fetchData = async () => {
  loading.value = true;
  try {
    const response = await axios.get("https://fakestoreapi.com/products");
    payload.value = response.data.map((item) => ({
      id: item.id,
      title: item.title,
      price: item.price,
      description: item.description,
      category: item.category,
      image: item.image,
      rate: item.rating?.rate || 0, 
      count: item.rating?.count || 0,
    }));
  } catch (error) {
    console.error("Error fetching data:", error);
  } finally {
    loading.value = false;
  }
};


const save = () => {
  if (editedIndex.value > -1) {
    payload.value[editedIndex.value] = { ...editedItem };
  } else {
    payload.value.push({ ...editedItem });
  }
  closeDialog();
};

const deleteItem = (item) => {
  editedIndex.value = payload.value.indexOf(item);
  dialogDelete.value = true;
};

const deleteItemConfirm = () => {
  payload.value.splice(editedIndex.value, 1);
  closeDeleteDialog();
};


const openDialog = () => {
  Object.assign(editedItem, defaultItem);
  editedIndex.value = -1;
  dialog.value = true;
};

const editItem = (item) => {
  editedIndex.value = payload.value.indexOf(item);
  Object.assign(editedItem, item);
  dialog.value = true;
};

const closeDialog = () => {
  dialog.value = false;
};

const closeDeleteDialog = () => {
  dialogDelete.value = false;
};


const capitalize = (str) => str.charAt(0).toUpperCase() + str.slice(1);


onMounted(fetchData);
</script>
