<template>
  <v-container>
    <v-card class="d-flex justify-space-between" gap-2>
      <v-text-field :loading="loading" append-inner-icon="mdi-magnify" density="compact" label="Search templates"
        variant="solo" hide-details single-line></v-text-field>
      <v-btn @click="dialog = true">ADD</v-btn>
    </v-card><br>

    <v-data-table :headers="headers" :items="payload" item-value="name" class="elevation-1">
      <template v-slot:item.actions="{ item }">
        <v-icon class="me-2" size="small" @click="editItem(item)">
          mdi-pencil
        </v-icon>
        <v-icon size="small" @click="deleteItem(item)">
          mdi-delete
        </v-icon>
      </template>

      <template v-slot:no-data>
        <v-btn color="primary" @click="initialize"> Reset </v-btn>
      </template>
    </v-data-table>

    <v-dialog v-model="dialog" max-width="500">
      <v-card>
        <v-card-title>{{ formTitle }}</v-card-title>
        <v-card-text>
          <v-container>
            <v-row>
              <v-col cols="12" sm="6" md="4">
                <v-text-field v-model="editedItem.name" label="Name"></v-text-field>
              </v-col>
              <v-col cols="12" sm="6" md="4">
                <v-text-field v-model="editedItem.year" label="Year" type="number"></v-text-field>
              </v-col>
              <v-col cols="12" sm="6" md="4">
                <v-text-field v-model="editedItem.price" label="Price" type="number"></v-text-field>
              </v-col>
              <v-col cols="12" sm="6" md="4">
                <v-text-field v-model="editedItem.model" label="CPU Model"></v-text-field>
              </v-col>
              <v-col cols="12" sm="6" md="4">
                <v-text-field v-model="editedItem.size" label="Hard Disk Size"></v-text-field>
              </v-col>
              <v-col cols="12" sm="6" md="4">
                <v-text-field v-model="editedItem.color" label="Color"></v-text-field>
              </v-col>
            </v-row>
          </v-container>
        </v-card-text>
        <v-card-actions>
          <v-btn color="primary" @click="save">Save</v-btn>
          <v-btn text @click="close">Cancel</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>

    <v-dialog v-model="dialogDelete" max-width="500">
      <v-card>
        <v-card-title class="text-h5">Are you sure you want to delete?</v-card-title>
        <v-card-actions>
          <v-btn color="error" text @click="deleteItemConfirm">Yes</v-btn>
          <v-btn text @click="closeDelete">No</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-container>
</template>

<script setup>
import { ref, reactive, computed } from "vue";
import axios from "axios";

const dialog = ref(false);
const dialogDelete = ref(false);
const loading = ref(false);

const headers = [
  { title: "Name", align: "start", sortable: false, key: "name" },
  { title: "Year", key: "year" },
  { title: "Price", key: "price" },
  { title: "CPU Model", key: "model" },
  { title: "Hard Disk Size", key: "size" },
  { title: "Color", key: "color" },
  { title: "Actions", key: "actions", sortable: false },
];

const payload = ref();

const defaultItem = {
  name: "",
  year: 0,
  price: 0,
  model: "",
  size: "",
  color: "",
};

const editedItem = reactive({ ...defaultItem });
const editedIndex = ref(-1);

const formTitle = computed(() =>
  editedIndex.value === -1 ? "New Item" : "Edit Item"
);

const fetchApiData = async () => {
  const apiUrl = "https://api.restful-api.dev/objects";
  loading.value = true;

  try {
    const response = await axios.get(apiUrl);
    payload.value = response.data.map((item) => ({
      name: item.name || "N/A",
      year: item.year || 0,
      price: item.price || 0,
      model: item.cpu_model || "N/A",
      size: item.hard_disk_size || "N/A",
      color: item.color || "N/A",
    }));
  } catch (error) {
    console.error("Error fetching data:", error);
    alert("Failed to fetch data from the API.");
  } finally {
    loading.value = false;
  }
};


const initialize = () => {
  payload.value = [
   {
      "id": "1",
      "name": "Google Pixel 6 Pro",
      "data": {
         "color": "Cloudy White",
         "capacity": "128 GB"
      }
   },
   {
      "id": "2",
      "name": "Apple iPhone 12 Mini, 256GB, Blue",
      "data": null
   },
   {
      "id": "3",
      "name": "Apple iPhone 12 Pro Max",
      "data": {
         "color": "Cloudy White",
         "capacity GB": 512
      }
   },
   {
      "id": "4",
      "name": "Apple iPhone 11, 64GB",
      "data": {
         "price": 389.99,
         "color": "Purple"
      }
   },
   {
      "id": "5",
      "name": "Samsung Galaxy Z Fold2",
      "data": {
         "price": 689.99,
         "color": "Brown"
      }
   },
   {
      "id": "6",
      "name": "Apple AirPods",
      "data": {
         "generation": "3rd",
         "price": 120
      }
   },
   {
      "id": "7",
      "name": "Apple MacBook Pro 16",
      "data": {
         "year": 2019,
         "price": 1849.99,
         "CPU model": "Intel Core i9",
         "Hard disk size": "1 TB"
      }
   },
   {
      "id": "8",
      "name": "Apple Watch Series 8",
      "data": {
         "Strap Colour": "Elderberry",
         "Case Size": "41mm"
      }
   },
   {
      "id": "9",
      "name": "Beats Studio3 Wireless",
      "data": {
         "Color": "Red",
         "Description": "High-performance wireless noise cancelling headphones"
      }
   },
   {
      "id": "10",
      "name": "Apple iPad Mini 5th Gen",
      "data": {
         "Capacity": "64 GB",
         "Screen size": 7.9
      }
   },
   {
      "id": "11",
      "name": "Apple iPad Mini 5th Gen",
      "data": {
         "Capacity": "254 GB",
         "Screen size": 7.9
      }
   },
   {
      "id": "12",
      "name": "Apple iPad Air",
      "data": {
         "Generation": "4th",
         "Price": "419.99",
         "Capacity": "64 GB"
      }
   },
   {
      "id": "13",
      "name": "Apple iPad Air",
      "data": {
         "Generation": "4th",
         "Price": "519.99",
         "Capacity": "256 GB"
      }
   }
]}
const editItem = (item) => {
  editedIndex.value = payload.value.indexOf(item);
  Object.assign(editedItem, item);
  dialog.value = true;
};

const deleteItem = (item) => {
  editedIndex.value = payload.value.indexOf(item);
  Object.assign(editedItem, item);
  dialogDelete.value = true;
};

const deleteItemConfirm = async () => {
  try {
    await axios.delete(`https://api.restful-api.dev/objects/${editedItem.id}`);
    payload.value.splice(editedIndex.value, 1);
    closeDelete();
  } catch (error) {
    console.error("Error deleting item:", error);
    alert("Failed to delete the item.");
  }
};

const close = () => {
  dialog.value = false;
  resetForm();
};

const closeDelete = () => {
  dialogDelete.value = false;
  resetForm();
};

const save = async () => {
  try {
    if (editedIndex.value > -1) {
      const response = await axios.put(
        `https://api.restful-api.dev/objects/${editedItem.id}`,
        editedItem
      );
      Object.assign(payload.value[editedIndex.value], response.data);
    } else {
      const response = await axios.post(
        "https://api.restful-api.dev/objects",
        editedItem
      );
      payload.value.push(response.data);
    }
    close();
  } catch (error) {
    console.error("Error saving data:", error);
    alert("Failed to save the item.");
  }
};

const resetForm = () => {
  Object.assign(editedItem, defaultItem);
  editedIndex.value = -1;
};

fetchApiData();
</script>
