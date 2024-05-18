<template>
    <div class="cart-container" @mouseover="showCartItems" @mouseleave="hideCartItems">
      <h2></h2>
      <ul v-show="showItems">
        <li v-for="item in cartItems" :key="item.id">
          {{ item.title }} - ${{ item.price.toFixed(2) }}
        </li>
      </ul>
    </div>
  </template>
  
  <script>
  export default {
    data() {
      return {
        cartItems: [],
        showItems: false,
      };
    },
    methods: {
      addToCart(product) {
        this.cartItems.push(product);
      },
      getTotalItems() {
        return this.cartItems.length;
      },
      showCartItems() {
        this.showItems = true;
      },
      hideCartItems() {
        this.showItems = false;
      },
    },
  };
  </script>
  
  <style scoped>
  .cart-container {
    position: relative;
  }
  
  .cart-container ul {
    position: absolute;
    top: 100%;
    right: 0;
    background-color: #fff;
    border: 1px solid #ccc;
    padding: 10px;
    display: none;
    z-index: 1000;
  }
  
  .cart-container ul li {
    margin-bottom: 5px;
  }
  
  .cart-container:hover ul {
    display: block;
  }
  </style>
  