<template>
<div class="editbook">
  <div class="err_info">{{errEdit}}</div>
  <div class="add_info">{{isEdit}}</div>
  <div class="add">
    <div class="lbl_add">Book(id:{{book_id}}) add form</div>
      <div class="form-group">
        <label for="inputName">Name</label>
        <input type="text" class="form-control" id="inputName" placeholder="Enter name" v-model="book.name">
      </div>

      <div class="form-group">
        <label for="inputDescr">Description</label>
        <textarea class="form-control" id="inputDescr" placeholder="Enter description" v-model="book.description" rows="5"></textarea>
      </div>

      <div class="row">
        <div  class="col-sm-10 col-md-5">
          <div class="form-group">
            <label for="inputPrice">Price</label>
            <input type="number" min='0' step='0.1' class="form-control" id="inputPrice" placeholder="Enter price" v-model="book.price">
          </div>
        </div>
        <div  class="col-sm-2 col-md-2"></div>
        <div  class="col-sm-10 col-md-5">
          <div class="form-group">
            <label for="inputDiscount">Discount</label>
            <input type="number" min='0' step='0.1' class="form-control" id="inputDiscount" placeholder="Enter discount" v-model="book.discount">
          </div>
        </div>
      </div>    

      <div class="row">
        <div  class="col-sm-10 col-md-5">
          <div class="form-group">
            <label for="inputGanre">Ganre(s)</label>
            <select multiple class="form-control" v-model="book_ganres">
              <option v-for="ganre in ganres" :key="ganre.id" :value="ganre.id">{{ganre.name}}</option>
            </select>
          </div>
        </div>
        <div  class="col-sm-2 col-md-2"></div>
        <div  class="col-sm-10 col-md-5">
          <div class="form-group">
            <label for="inputAuthor">Author(s)</label>
            <select multiple class="form-control"  v-model="book_authors">
              <option v-for="author in authors" :key="author.id" :value="author.id">{{author.name}} {{author.surname}}</option>
            </select>
          </div>
        </div>
      </div>
      
      <button  class="btn btn-primary" @click="edit_book">Edit book</button>
      <input type="reset" class="btn btn-def" value="Cancel">
    </div>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  name: 'editBook',
  data () {
    return {
      book: {},
      errEdit: "",
      isEdit: "",
      book_show: true,
      book_authors: [],
      book_ganres: [],
      ganres: [],
      authors: [],
    }
  },
  created(){
    this. getGanres()
    this. getAuthors()
  },
  methods:{
    edit_book(){
      var self = this
      if(this.book.name != ""){
         //axios.put('http://192.168.0.15/~user15/BOOK_SHOP/client/api/book/', 
        axios.put('http://localhost:88/BOOK_SHOP/client/api/book/', 
        {
          name: this.book.name,
          description: this.book.description,
          price: this.book.price,
          discount: this.book.discount,
          book_ganres: this.book_ganres,
          book_authors: this.book_authors,
          id: this.book.id
        }, this.config)
        .then(function (response) {
           if(response.data.data == '1'){
              self.isEdit = "record was updeted."
              setTimeout(function () {
                self.isEdit = ""
              },2500);
              self.$emit('updateGanres')
              self.$emit('setSelect', 'ganre', self.ganre.id)  
           }
        })
        .catch(function (error) {
            self.errEdit = "somthing wrong, no update was made"
            setTimeout(function () {
              self.errEdit = ""
            },2500);
        });
      }else{
        this.errEdit = "fill the name field"
        setTimeout(function () {
            this.errEdit = ""
        },2500);   
      }
   },

    getGanres(){
      var self = this
      axios.get('http://localhost:88/BOOK_SHOP/client/api/ganre/', this.config)
      //axios.get('http://192.168.0.15/~user15/BOOK_SHOP/client/api/ganre/', this.config)
            .then(function (response) {
              console.log(response.data);
                self.ganres = response.data.data
      })
      .catch(function (error) {
        console.log(error);
      });
    },

    getAuthors(){
      var self = this
      axios.get('http://localhost:88/BOOK_SHOP/client/api/author/', this.config)
      //axios.get('http://192.168.0.15/~user15/BOOK_SHOP/client/api/author/', this.config)
            .then(function (response) {
              //console.log(response.data);
                self.authors = response.data.data
      })
      .catch(function (error) {
        console.log(error);
      });
    },
  },
  computed:{
    book_id: function(){
      var id = this.$route.params.id
      //alert(this.$route.params.id)
      var self = this
      axios.get('http://localhost:88/BOOK_SHOP/client/api/book/'+ this.$route.params.book_id, this.config)
    //axios.get('http://192.168.0.15/~user15/BOOK_SHOP/client/api/book/'+ this.$route.params.book_id, this.config)
            .then(function (response) {
              self.book = response.data.data
              if( self.book.length == 0){
                self.errEdit = "Information about this gare is absent"
                self.book_show = false
              }
              self.book_ganres_id = self.book.ganres_id.split(',')
              self.book_authors_id = self.book.authors_id.split(',')
              self.book_ganres = self.book_ganres_id.map(function(el) {
                return parseInt(el);
              });
              self.book_authors = self.book_authors_id.map(function(el) {
                return parseInt(el);
              });
              //console.log(self.book_ganres)
              //console.log(self.book_authors)
            })
            .catch(function (error) {
              self.errEdit = "Information about this ganre is absent"
              self.book_show = false

          }); 
      return id;
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
/*--error--*/
.err_info, .add_info{
  margin-top: 20px;
  font-weight: bold;
  font-size: 17px;
}

.err_info{
  color: #F00;
}
</style>
