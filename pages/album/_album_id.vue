<template>
<v-app>

<v-snackbar
v-model="snackbar"
:top="'top'"
>
{{msg}}
<v-btn      
text
@click="snackbar = false"
>
Close
</v-btn>
</v-snackbar>


<v-card>


<v-toolbar flat dark>
<v-toolbar-title class="headline">Create</v-toolbar-title>
<v-spacer></v-spacer>
<v-btn
color="primary"
class=" black--black"
@click="save"
>
Submit
</v-btn>
</v-toolbar>

<v-card-text>
<v-form ref="form" lazy-validation>

<v-container v-for="(loop,index) in i" :key="index">
<v-row>
<v-col>
<strong>
<v-alert class="primary">
Product {{loop}}
</v-alert>
</strong>
</v-col>

</v-row>
<v-row>
<v-col>


<v-text-field :rules="Rules" v-model="item.product_title[index]" label="Product Title"></v-text-field>
</v-col>
<v-col>
<v-text-field :rules="Rules" v-model="item.product_code[index]" label="Product CODE"></v-text-field>
</v-col>
</v-row>

<v-row>
<v-col>
<v-text-field :rules="Rules"  v-model="item.product_price[index]" label="Product Price"></v-text-field>
</v-col>
<v-col>
<v-select
v-model="item.color_id[index]" 
:items="colors"
item-value="id"
item-text="color" 
label="Color"
></v-select>
</v-col>

</v-row>

<v-row>

      <v-file-input

      small-chips
      show-size
      multiple
      clearable
      @change="check_attachment"
    >
      <template v-slot:selection="{ text, inner_index, file }">
        <v-chip
          small
          text-color="black"
          color="primary" 
          >
          {{ text }}
        </v-chip>
      </template>
    </v-file-input>
</v-row>


<v-row>
<v-col>
<v-textarea  v-model="item.product_description[index]" label="Production Description"></v-textarea>
</v-col>
</v-row>




</v-container>
<v-container><v-row>
<v-col>
<v-btn class="primary black--text" @click="i++">Add More <v-icon>mdi-plus</v-icon></v-btn>
</v-col>

</v-row>
</v-container>

</v-form>

</v-card-text>



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
</v-card>
</v-app>
</template>


<script>

export default {
async created () {
//console.log(this.$route.params.album_id);
//  const categories = await this.$axios.get('users');
//  this.categories = categories.data;

},
data:() => ({
i:1,  
categories:[],
item : {
files:[],   
color_id:[],
product_code:[],
product_title:[],
product_price:[],
product_image:[],
product_description:[],
},
product_image:{
  name:''
},
colors:[
{id:1,color_code:'#ff0000',color:'red'},
{id:2,color_code:'#00ff00',color:'green'},
{id:3,color_code:'#0000FF',color:'blue'},

],



msg:"",

snackbar:false,
Rules : [
v => !!v || 'This field is required',
],

}),
computed:{


},
watch: {

},
methods:{

check_attachment(e) { 

this.item.product_image[this.i - 1] = e || ''; 

},

save(e){

let product = new FormData();
product.append('color_id',this.item.color_id);
product.append('product_code',this.item.product_code);
product.append('product_title',this.item.product_title); 
product.append('product_price',this.item.product_price);
product.append('product_image',this.item.product_image);
product.append('product_description',this.item.product_description);
// if(this.$refs.form.validate()){

// this.$axios.post('product',product).then((res) => {

//     this.snackbar = res.data.response_status;

//     if(res.data.response_status){

//         this.msg = res.data.message;
//         setTimeout(() => {
//             this.$router.push('/product');
//         },1000);
//     }
//     else{
//         this.errors = res.data.errors;
//     }


// });

// }

},

}

}
</script>