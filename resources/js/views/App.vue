<template>
    <div>
        <cart @add-to-cart="addToCart" @remove-from-cart="removeFromCart"></cart>
        <div class="product">
            <div class="product-image">
                <img v-bind:src="image">
            </div>   
            <div class="product-info">
                <h1>{{ title }}</h1>
                <p v-if="inStock">In Stock</p>
                <p v-else-if="inStock > 0 && inStock < 10">Almost Sold Out</p>
                <p v-else :class="{soldOut:!inStock}">Out of Stock</p>
                <p>Shipping: {{ shipping }}</p>

                <ul>
                    <li v-for="detail in details" >{{ detail }}</li>
                </ul>

                <div v-for="(variant, index) in variants" 
                     :key="variant.variantId"
                     class="color-box"
                     :style="{ backgroundColor: variant.variantColor }"
                     @mouseover="updateProduct(index)">
                </div>

                <button 
                    v-on:click="addToCart"
                    :disabled="!inStock"
                    :class="{ disabledButton: !inStock }"
                >Add to Cart</button>
                <button 
                    v-on:click="removeFromCart"
                    :disabled="!inStock"
                    :class="{ disabledButton: !inStock }"
                    >Erase Cart</button>

            </div>

            <product-tabs :reviews="reviews"></product-tabs>


            
        </div>
    </div>
</template>




<script>

import { bus } from '../app.js'

    export default {

        name: 'app',
        props: {
            premium:{
                type: Boolean,
            required: true
            }
        },

        data: function() {
            return {
                brand: 'Adidas',
                product: 'Socks',
                slectedVariant: 0,
                details: [
                    '80% cotton',
                    '20% polyester',
                    'Kids'
                ],
                variants: [
                    {
                        variantId: 2234,
                        variantColor: "green",
                        variantImage: './image/vmSocks-green-onWhite.jpg',
                        variantQuantity: 0
                    },
                    {
                        variantId: 2233,
                        variantColor: "blue",
                        variantImage: './image/vmSocks-blue-onWhite.jpg',
                        variantQuantity: 10
                    }
                ],
                reviews: []
            }
        },
        methods: {
            addToCart(){
                bus.$emit('add-to-cart')
            },
            removeFromCart(){
                bus.$emit('remove-from-cart')
            },
            updateProduct(index){
                this.slectedVariant = index
            },
            
        },
        computed:{
            title() {
                return this.brand + ' ' + this.product
            },
            image(){
                return this.variants[this.slectedVariant].variantImage
            },
            inStock(){
                return this.variants[this.slectedVariant].variantQuantity
            },
            shipping(){
                if (this.premium) {
                    return "Free"
                }
                    return 2.99
            }
        },
        mounted(){
            bus.$on('review-submitted', productReview =>{
                this.reviews.push(productReview)
            })
        }
    }
</script>







<style>

  .product {
    display: flex;
    flex-flow: wrap;
    padding: 1rem;
  }

  img {
    border: 1px solid #d8d8d8;
    width: 70%;
    margin: 40px;
    box-shadow: 0px .5px 1px #d8d8d8;
  }
  
  .product-image {
    width: 80%;
  }
  
  .product-image,
  .product-info {
    margin-top: 10px;
    width: 50%;
  }
  
  .color-box {
    width: 40px;
    height: 40px;
    margin-top: 5px;
  }
  
  button {
    margin-top: 30px;
    border: none;
    background-color: #1E95EA;
    color: white;
    height: 40px;
    width: 100px;
    font-size: 14px;
  } 
  
  .disabledButton {
    background-color: #d8d8d8;
  }

  .soldOut{
    color: #d8d8d8;
    text-decoration: line-through;
  }
</style>