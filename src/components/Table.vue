<template>
  <div>
    <v-data-table :headers="headers" :items="employeeItem" class="elevation-1">
      <template v-slot:top>
        <v-toolbar flat>
          <v-toolbar-title>จัดการข้อมูล</v-toolbar-title>
          <v-divider class="mx-4" inset vertical></v-divider>
          <v-spacer></v-spacer>
          <v-btn
            color="primary"
            dark
            class="mb-2"
            @click="openDialog('add', defaultItem)"
          >
            เพิ่มข้อมูล
          </v-btn>
        </v-toolbar>
      </template>
      <template v-slot:[`item.roles`]="{ item }">
        {{ item.role.name }}
      </template>

      <template v-slot:[`item.actions`]="{ item }">
        <v-icon small class="mr-2" @click="openDialog('edit', item)">
          mdi-pencil
        </v-icon>
        <v-icon small @click="deleteItem(item)"> mdi-delete </v-icon>
      </template>

      <template v-slot:no-data>
        <v-btn color="primary" @click="initialize"> Reset </v-btn>
      </template>
    </v-data-table>
    <v-dialog v-model="dialog" max-width="500px">
      <v-card>
        <v-card-title>
          <span class="text-h5">{{ formTitle }}</span>
        </v-card-title>

        <v-card-text>
          <v-container>
            <v-row>
              <v-col cols="12" sm="6" md="4">
                <v-text-field
                  v-model="editedItem.name"
                  label="ชื่อ"
                ></v-text-field>
              </v-col>
              <v-col cols="12" sm="6" md="4">
                <v-text-field
                  v-model="editedItem.lastName"
                  label="นามสกุล"
                ></v-text-field>
              </v-col>
              <v-col cols="12" sm="6" md="4">
                <v-text-field
                  v-model="editedItem.Salary"
                  label="เงินเดือน"
                ></v-text-field>
              </v-col>
              <v-col cols="12" sm="6" md="4">
                <v-text-field
                  v-model="editedItem.role"
                  label="ตำแหน่ง"
                ></v-text-field>
              </v-col>
            </v-row>
          </v-container>
        </v-card-text>

        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" text @click="close"> Cancel </v-btn>
          <v-btn color="blue darken-1" text @click="save(formTitle)">
            บันทึก
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
    <v-dialog v-model="dialogDelete" max-width="500px">
      <v-card>
        <v-card-title class="text-h5"
          >Are you sure you want to delete this item?</v-card-title
        >
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" text @click="closeDelete()">Cancel</v-btn>
          <v-btn color="blue darken-1" text @click="deleteItemConfirm()"
            >OK</v-btn
          >
          <v-spacer></v-spacer>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </div>
</template>
  
<script>
export default {

    data: () => ({
        firstName:'',
        lastName:'',
        salary:'',
        role:'',
        dialog: false,
        dialogDelete: false,
        headers: [
            {
                text: 'ไอดี',
                align: 'start',
                sortable: false,
                value: 'id',
            },
            { text: 'ชื่อ', value: 'firstName' },
            { text: 'นามสกุล ', value: 'lastName' },
            { text: 'เงินเดือน ', value: 'salary' },
            { text: 'ตำแหน่ง ', value: 'role' },
            { text: 'Actions', value: 'actions', sortable: false },
        ],
        employeeItem:[],

        editedIndex: -1,
        editedItem: {
            name: '',
            calories: 0,
            fat: 0,
            carbs: 0,
            protein: 0,
        },
        defaultItem: {
            name: '',
            calories: 0,
            fat: 0,
            carbs: 0,
            protein: 0,
        },
        formTitle: '',
        employeeItem: []
    }),

    watch: {
        dialog(val) {
            val || this.close()
        },
        dialogDelete(val) {
            val || this.closeDelete()
        },
    },

    created () {
        console.log('data employ ====>')
        this.initialize()
    },

    methods: {
        async initialize() {
            this.employeeItem = []
            try {
                var data = await this.axios.get('http://localhost:9000/employee')
                console.log('data employ ====>', data)
                this.employeeItem = data.data
                // this.employeeItem = 'ooo'
            console.log('data employ ====>', this.employeeItem)
            } catch (error){

            }
        },
        close() {
            this.dialog = false
            this.editItem = []
            this.defaultItem = {
                name: '',
                calories: 0,
                fat: 0,
                carbs: 0,
                protein: 0,
            }
        },
        async save(action) {
            if (action === 'เพิ่มข้อมูล') {
            //     this.desserts.push(this.editedItem)
            // }else{
            //     Object.assign(this.desserts[this.editItemIndex], this.editedItem)
            // }
            //     this.close()
            var data = {
                firstName:this.firstName,
                lastName:this.lastName,
                salary:this.salary,
                role:this.roles
            }
            console.log('data after send ====>',data)
            try{
                var dataResponse = await this.axiospost('http://localhost:9000/employee',data)
                console.log('dataResponse ===>', dataResponse)
            }catch(error){(error.message)}

            }else{
                Object.assign(this.desserts[this.editedIndex],this.editItem)
            }
            this.close()
         },
           openDialog(Action, item) {
            // console.log(Action, item);
            this.formTitle = ''
            if (Action === 'add') {

                this.formTitle = "เพิ่มข้อมูล"
                this.editedItem = item

            } else {

                this.dialog = true
                this.formTitle = "แก้ไขข้อมูล"
                // this.editItemIndex = this.desserts.indexOf(item)
                // this.editedItem = item
                this.firstName = item.firstName
                this.lastName = item.lastName
                this.salary = item.salary
                this.roles = item.roles.name
                this.idEmployee = item.id
            }
        },
        editItem(item) {
            console.log('ittem select', item)
            console.log('index item',this.desserts.indexOf(item))
            // this.editedIndex = this.desserts.indexOf(item)
            // this.editedItem = Object.assign({}, item)
            // this.dialog = true
        },

        deleteItem(item) {
            this.idForDelete = item.id
            this.dialogDelete = true
        },

        async deleteItemConfirm() {
            try {
                var response = await this.axios.delete('http://localhost:9000/employee'+ this.idForDelete)
                this.initialize()
            }catch(error){
            }
            this.closeDelete
        },

        closeDelete() {
            this.dialogDelete = false
            this.editedItem = []
            this.editedIndex = -1

        },
    },
};
</script>
  
<style></style>