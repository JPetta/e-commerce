<template>
  <div>
    <div class="card " style="width:350px;">
        <img class="card-img-top" :src="product.imgUrl" alt="">
        <div class="card-body">
          <h4 class="card-title">{{product.name}}</h4>
          <p class="card-text">Rp.{{product.price}},-</p>
          <p v-if="where==='home'" class="card-text">Stock : {{product.quantity}}</p>
          <p v-if="where==='cart'" class="card-text">Qty : {{product.quantity}}</p>
          <p class="card-text">Kondisi : {{product.condition}}</p>
          <p class="card-text"><i class="fa fa-store"></i> {{product.seller}}</p>
          <div class="row d-flex justify-content-center">
            <button v-if="where==='home'" @click="addToCart(product)" class="btn btn-dark">Add to cart</button>
            <button v-if="where==='cart'" @click="deleteFromCart(product)" class="btn btn-dark">Delete</button>
            <button v-if="product.seller === seller" @click="deleteProduct(product)" class="btn btn-dark ml-2">Delete</button>
          </div>
        </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  props : {
    product : Object,
    where : String,
  },
  data(){
    return{
      seller : localStorage.user
    }
  },
  methods : {
    addToCart(product){
      this.$emit('buyOne')
      let productData = {
        name : product.name,
        imgUrl : product.imgUrl,
        price : product.price,
        quantity : 1,
        condition : product.condition,
        seller : product.seller,
      }
      axios({
        url : `http://54.179.171.81/user/addtocart/${localStorage.user}`,
        method : 'post',
        headers : {
          token : localStorage.token
        },
        data : productData
      })
      .then(({data})=>{
        swal.fire('Added to your cart!')
      })
      .catch(err=>{
        console.log(err)
      })
    },
    deleteFromCart(product){
      console.log('masuk delete cart')
      let productName = product.name
      axios({
        url : `http://54.179.171.81/user/deletecart/${localStorage.user}`,
        method : 'post',
        headers : {
          token : localStorage.token
        },
        data : {
          name : productName
        }
      })
      .then(({data})=>{
        console.log(data)
        swal.fire('Sorry you had to cancel :( Perhaps another item?')
        this.$emit('deleteCart')
      })
      .catch(err => {
        swal.fire('Oops, there seems to be an error')
        console.log(err)
      })
    },
    deleteProduct(product){
      axios({
        url : `http://54.179.171.81/product/${product._id}`,
        method : 'delete',
        headers : {
          token : localStorage.token
        }
      })
      .then(({data})=>{
        console.log(data)
        swal.fire('Item deleted, anything else you wanna sell?')
        this.$emit('productDeleted')
      })
      .catch(err => {
        swal.fire('Oops, there seems to be an error')
        console.log(err)
      })
    }
  }
}
</script>

<style>

</style>