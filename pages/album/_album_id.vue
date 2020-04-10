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
<v-toolbar-title class="headline">Create testing</v-toolbar-title>
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

<!-- v-for="(loop,index) in i" :key="index" -->
<v-container>
<v-row>
<v-col>
<strong>
<v-alert class="primary">
Product 

</v-alert>
</strong>
</v-col>

</v-row>
<v-row>
<v-col>


<v-text-field :rules="Rules" v-model="item.product_title" label="Product Title"></v-text-field>
</v-col>
<v-col>
<v-text-field :rules="Rules" v-model="item.product_code" label="Product CODE"></v-text-field>
</v-col>
</v-row>

<v-row>
<v-col>
<v-text-field type="number" :rules="Rules"  v-model="item.product_price" label="Product Price"></v-text-field>
</v-col>
<v-col>
<v-select
v-model="item.color_id" 
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
      <!-- <template v-slot:selection="{ text, inner_index, file }">
        <v-chip
          small
          text-color="black"
          color="primary" 
          >
          {{ text }}
        </v-chip>
      </template> -->
    </v-file-input>
</v-row>


<v-row>
<v-col>
<v-textarea  v-model="item.product_description" label="Production Description"></v-textarea>
</v-col>
</v-row>




</v-container>
<v-container>
<!-- <v-row>
<v-col>
<v-btn class="primary black--text" @click="i++">Add More <v-icon>mdi-plus</v-icon></v-btn>
</v-col>
</v-row> -->
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
this.collection_id = this.$route.params.album_id
//  const categories = await this.$axios.get('users');
//  this.categories = categories.data;

},
data:() => ({
collection_id : '',
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
arr:[],
colors:[
{id:'red',color_code:'#ff0000',color:'red'},
{id:'green',color_code:'#00ff00',color:'green'},
{id:'blue',color_code:'#0000FF',color:'blue'},

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
  if(!e.length){
    return false;
  }
    for( var i = 0; i < e.length; i++ ){
          this.item.product_image.push( e[i] ); 
    }


},

save(e){


const config = { headers: { 'Content-Type': 'multipart/form-data' } };


let index = this.i - 1;
let product = new FormData();  


product.append('collection_id',this.collection_id);
product.append('color_id',this.item.color_id);
product.append('product_code',this.item.product_code);
product.append('product_title',this.item.product_title); 
product.append('product_price',this.item.product_price);
product.append('product_description',this.item.product_description);

  for(var j = 0; j < this.item.product_image.length; j++){
     product.append('product_images['+j+']', this.item.product_image[j]);  
  }  

if(this.$refs.form.validate()){

  this.$axios.post('product',product,config).then((res) => {
    this.snackbar = res.data.response_status;

    if(res.data.response_status){

        this.msg = res.data.message;
        setTimeout(() => {
            this.$router.push('product/'+this.collection_id);
        },1000);
    }
    else{
        this.errors = res.data.errors;
    }


});
}

},

}

}
</script>