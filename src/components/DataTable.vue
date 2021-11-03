<template>
  <div fluid="md" class="container veggieTable">
    <h3>Vegetable Inventory Management</h3>

    <b-container>
      <b-row class="rows">
        <b-col sm="11">
          <b-form-input
            id="filter-input"
            v-model="filter"
            type="search"
            placeholder="Type to Search"
          >
          </b-form-input>
        </b-col>
        <b-col sm="1"
          ><b-button
            @click="clear()"
            v-b-modal.addModal
            variant="outline-success"
            >Add</b-button
          ></b-col
        >
      </b-row>

      <b-row class="rows">
        <b-col>
          <b-table
            dark
            fixed
            bordered
            responsive
            :filter-included-fields="['SKU', 'Name']"
            :filter="filter"
            striped
            hover
            :items="rows"
            :fields="fields"
          >
            <template v-slot:cell(EditAndDelete)="row">
              <b-button-group>
                <b-button
                  v-b-modal.editModal
                  variant="outline-warning"
                  @click="Object.assign(updateForm, row.item)"
                  ><b-icon icon="pencil-square"></b-icon>
                </b-button>
                <b-button
                  variant="outline-danger"
                  @click="deleteItem(row.item.SKU)"
                  ><b-icon icon="trash"></b-icon>
                </b-button>
              </b-button-group>
            </template>
            <template #table-colgroup="scope">
              <col
                v-for="field in scope.fields"
                :key="field.key"
                :style="{width: field.key === 'EditAndDelete' ? '40px' : '100px'}"
              />
            </template>
          </b-table>
        </b-col>
      </b-row>
    </b-container>

    <b-modal
      hide-header-close
      @ok="handleOkUpdate"
      id="editModal"
      :title="'Edit ' + updateForm.Name"
    >
      <form ref="updateForm">
        <label for="input-date">Best Before Date:</label>
        <b-form-datepicker
          id="input-date"
          :locale="'en-UK'"
          :value-as-date="true"
          v-model="updateForm.BestBefore"
          required
        ></b-form-datepicker
        ><br />
        <label for="input-inv">Inventory Level:</label>
        <b-form-input
          :state="isANumberUp"
          aria-describedby="input-inv-feedback"
          placeholder="Enter a new amount"
          id="input-inv"
          v-model.number="updateForm.InvLvl"
          required
          @keyup.enter="handleOkUpdate"
        ></b-form-input>
        <b-form-invalid-feedback id="input-inv-feedback">
          Enter a number.
        </b-form-invalid-feedback>
      </form>
    </b-modal>

    <b-modal @ok="handleOkAdd" hide-header-close id="addModal" title="Add Item">
      <form ref="addForm">
        <div>
          <label for="input-name-add">Name:</label>
          <b-form-input
            :state="isAString"
            required
            @keyup.enter="handleOkAdd"
            aria-describedby="input-name-feedback-add"
            placeholder="New item entry..."
            id="input-name-add"
            v-model="addItem.Name"
          >
          </b-form-input>
          <b-form-invalid-feedback
            id="input-name-feedback-add"
            :state="isAString"
          >
            Enter a Vegetable.
          </b-form-invalid-feedback>
        </div>
        <br />
        <div>
          <label for="input-date-add">Best Before Date:</label>
          <b-form-datepicker
            required
            aria-describedby="input-date-feedback-add"
            id="input-date-add"
            :locale="'en-UK'"
            :value-as-date="true"
            v-model="addItem.BestBefore"
          ></b-form-datepicker>
          <b-form-invalid-feedback id="input-date-feedback-add">
            Enter a date.
          </b-form-invalid-feedback>
        </div>
        <br />
        <div>
          <label for="input-inv-add">Inventory Level:</label>
          <b-form-input
            :state="isANumberAdd"
            required
            @keyup.enter="handleOkAdd"
            aria-describedby="input-inv-feedback-add"
            placeholder="Enter amount"
            id="input-inv-add"
            v-model.number="addItem.InvLvl"
          ></b-form-input>
          <b-form-invalid-feedback
            id="input-inv-feedback-add"
            :state="isANumberAdd"
          >
            Enter a number.
          </b-form-invalid-feedback>
        </div>
      </form>
    </b-modal>
  </div>
</template>

<script>
export default {
  name: "DataTable",
  props: {},
  computed: {
    isAString() {
      return this.addItem.Name !== "";
    },
    isANumberAdd() {
      return this.addItem.InvLvl !== "" && !isNaN(this.addItem.InvLvl);
    },
    isANumberUp() {
      return this.updateForm.InvLvl !== "" && !isNaN(this.updateForm.InvLvl);
    },
  },
  data() {
    return {
      SKU_seq: 0,
      filter: null,
      addItem: {
        SKU: 0,
        Name: "",
        BestBefore: new Date(),
        InvLvl: "",
      },
      updateForm: {
        SKU: 0,
        Name: "",
        BestBefore: new Date(),
        InvLvl: 0,
      },
      fields: [
        {
          key: "SKU",
         
        },
        {
          key: "Name",
      
        },
        {
          key: "BestBefore",
          label: "Best Before Date",
          formatter: "formatDate",
          
        },
        {
          key: "InvLvl",
          label: "Inventory Level",
          
        },
        {
          key: "EditAndDelete",
          label: "Edit/Delete",
          tdClass: "tdElements"
        },
      ],
      rows: [
        {
          SKU: 1234,
          Name: "Carrots",
          BestBefore: new Date(2021, 11, 20),
          InvLvl: 240,
        },
        {
          SKU: 4567,
          Name: "Potatoes",
          BestBefore: new Date(2021, 9, 20),
          InvLvl: 140,
        },
        {
          SKU: 4321,
          Name: "Corn",
          BestBefore: new Date(2021, 7, 20),
          InvLvl: 40,
        },
        {
          SKU: 2416,
          Name: "Cucumber",
          BestBefore: new Date(2021, 6, 16),
          InvLvl: 80,
        },
      ],
    };
  },
  methods: {
    handleOkUpdate(event) {
      if (this.$refs.updateForm.checkValidity() == false) {
        event.preventDefault();
      } else {
        this.updateItem();
      }
    },
    handleOkAdd(event) {
      if (this.$refs.addForm.checkValidity() == false) {
        event.preventDefault();
      } else {
        this.$nextTick(() => {
          this.$bvModal.hide("addModal");
        });
        this.addNewItem();
      }
    },
    formatDate(date) {
      return date.toLocaleDateString("en-UK");
    },
    deleteItem(SKU) {
      console.log(SKU);
      this.rows = this.rows.filter((x) => x.SKU != SKU);
    },
    addNewItem() {
      let copy = {};
      Object.assign(copy, this.addItem);
      copy.SKU = this.SKU_seq;
      this.rows.push(copy);
      this.SKU_seq++;
    },
    clear() {
      this.addItem = {
        SKU: 0,
        Name: "",
        BestBefore: new Date(),
        InvLvl: 0,
      };
    },
    updateItem() {
      let copy = {};
      Object.assign(copy, this.updateForm);
      this.rows = this.rows.map((x) => {
        return x.SKU == this.updateForm.SKU ? copy : x;
      });
    },
  },
  created: function () {
    this.SKU_seq = Math.max(...this.rows.map((x) => x.SKU)) + 1;
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
h3 {
  margin: auto;
  padding: 20px;
  color: white;
}
body {
  height: 100px !important;
  background-color: #212529 !important;
  
}
.tdElements{
  text-align: center;
}
.rows {
  margin-bottom: 20px;
}
</style>
