<template>
<v-app>
  <div class="text-center ma-2">

    <v-snackbar
      v-model="snackbar"
      :top="'top'"
    >
      {{response.msg}}
      <v-btn      
        text
        @click="snackbar = false"
      >
        Close
      </v-btn>
    </v-snackbar>
  </div>
  <v-data-table
    :headers="headers"
    :items="brands"
    :search="search"
    class="elevation-1"
  >
  <template  v-slot:item.categories="{ item }">
  <v-col style="margin-left:-15px !important;" sm="6">
  <v-chip
  v-for="(category,index) in item.categories" :key="index"
  small  class="ma-1 pa-2 black--text primary">
  {{category.category_name}}
  </v-chip>
  </v-col>
  </template>
    <template v-slot:top>
      <v-toolbar flat color="">
        <v-toolbar-title>Brands</v-toolbar-title>
        <v-divider
          class="mx-4"
          inset
          vertical
        ></v-divider>
      
      <v-text-field
        v-model="search"
        label="Search"
        single-line
        hide-details
      ></v-text-field>
        <v-divider
          class="mx-4"
          inset
          vertical
        ></v-divider>
       <v-dialog v-model="dialog" max-width="500px">
            <template v-slot:activator="{ on }">
            <v-btn color="primary"  class="mb-2" v-on="on">New Item</v-btn>
          </template>
          <v-form ref="form">
          
          <v-card>
            <v-card-title>
              <span class="headline">{{ formTitle }}</span>
            </v-card-title>

            <v-card-text>
              <v-container>
                <v-row>
                  <v-col>
                    <v-text-field 
                    :rules="Rules"
                     v-model="editedItem.brand_name" 
                     label="Brand Name"></v-text-field>

                  </v-col>
                  </v-row>
                  <v-row>
                  <v-col>
                  <v-select

                  :rules="Rules"
                  v-model="editedItem.category_ids" 
                  :items="cat_to_upload"
                  attach
                  chips
                  multiple
                  item-value="id"
                  item-text="category_name" 
                  label="Category"
                  ></v-select>
                  
                  </v-col>
                  </v-row>
                  
              </v-container>
            </v-card-text>

            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="blue darken-1" text @click="close">Cancel</v-btn>
              <v-btn color="blue darken-1" text @click="save">Save</v-btn>
            </v-card-actions>
          </v-card>
          </v-form>
        </v-dialog>
      </v-toolbar>
    </template>
    <template v-slot:item.action="{ item }">
      <v-icon
        small
        class="mr-2"
        @click="editItem(item)"
      >
        mdi-pencil
      </v-icon>
      <v-icon
        small
        @click="deleteItem(item)"
      >
        mdi-delete
      </v-icon>
    </template>
    <template v-slot:no-data>
      <!-- <v-btn color="primary" @click="initialize">Reset</v-btn> -->
    </template>
  </v-data-table>
  </v-app>
</template>
<script>
  export default {
   
    data: () => ({
      search:'',
      snackbar:false,
      dialog: false,
      headers: [
        {
          text: '#',
          align: 'left',
          sortable: false,
          value: 'id',
        },
         {
          text: 'Brand',
          align: 'left',
          sortable: false,
          value: 'brand_name',
        },
        {
          text: 'Category',
          align: 'left',
          sortable: false,
          value: 'categories',
        },
        { text: 'Actions', value: 'action', sortable: false },
      ],
      editedIndex: -1,
      editedItem: {
        id:'',
        category_ids:[],
        brand_name: '',
       
      },
      defaultItem: {
        id:'',
        category_ids:[],
        brand_name: '',
      
      },
      response : {
        msg:''
      },
      brands:[],
      categories:[],
      cat_to_upload:[],
      Rules : [
        v => !!v || 'This field is required',
      ],
    }),

    computed: {
      formTitle () {
        return this.editedIndex === -1 ? 'New Item' : 'Edit Item'
      },
      // categories(){
        
      //   return this.$store.state.categories

      // }
    },

    watch: {
      dialog (val) {
        val || this.close()
      },
    },

    async created () {
     const data = await this.$axios.get('brands');

     this.brands = data.data.brands

     this.cat_to_upload = data.data.categories;
            
    },

    methods: {

      editItem (item) {
          this.editedIndex = this.brands.indexOf(item)
          this.editedItem.id = item.id
          this.editedItem.brand_name = item.brand_name 
         // this.editedItem = Object.assign({}, item)
          this.dialog = true
          
      },

      deleteItem (item) {
        confirm('Are you sure you want to delete this item?') && 
         this.$axios.delete('brands/'+item.id)
            .then((res) => {

              if(res.data.response_status){ 

              const index = this.brands.indexOf(item)
              this.brands.splice(index, 1)
              this.snackbar = res.data.response_status;             
              this.response.msg = res.data.message;

              }
            });
      },

      close () {
        this.dialog = false
        setTimeout(() => {
          this.editedItem = Object.assign({}, this.defaultItem)
          this.editedIndex = -1
        }, 300)
      },

      save () {
        if(this.$refs.form.validate()){
          const payload = {
            brand_name: this.editedItem.brand_name,
            category_ids: this.editedItem.category_ids
            }

           if (this.editedIndex > -1) {

            this.$axios.put('brands/' + this.editedItem.id, payload)
            .then(res => {
            if(res.data.response_status){  
              const index = this.brands.findIndex(item => item.id == this.editedItem.id)
              Object.assign(this.brands[index],res.data.updated_record);
              this.snackbar = res.data.response_status;
              this.response.msg = res.data.message;
              this.close()
              }
            })
            .catch(error => console.log(err));
           }
           else{


              
            this.$axios.post('brands',payload)
              .then((res) => {

              if(res.data.response_status){ 

              this.brands.push(res.data.new_record)
              this.snackbar = res.data.response_status;
              this.response.msg = res.data.message;

              this.close()
              
              }

            });
         
           }

        }
      },
    },
  }
</script>