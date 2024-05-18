<template>
  <v-container>
    <v-row>
      <v-col cols="12">
        <h1 class="text-center">Product Listing</h1>
        <v-row align="center" class="cart-container" @mouseenter="showCart = true" @mouseleave="showCart = false">
          <v-col cols="auto">
            <v-btn icon>
              <v-badge :content="cartItemCount" color="primary">
                <v-icon>mdi-cart</v-icon>
              </v-badge>
            </v-btn>
          </v-col>
          <v-col v-if="showCart" class="cart-dropdown-container">
            <v-slide-y-transition>
              <v-card class="cart-dropdown" :elevation="12">
                <v-card-title>Cart</v-card-title>
                <v-list>
                  <v-list-item v-for="(item, index) in cart" :key="index">
                    <v-list-item-content>{{ item.title }}</v-list-item-content>
                    <v-list-item-action>
                      <v-icon @click="removeFromCart(index)">mdi-delete</v-icon>
                    </v-list-item-action>
                  </v-list-item>
                </v-list>
              </v-card>
            </v-slide-y-transition>
          </v-col>
        </v-row>
      </v-col>
    </v-row>
    
    <v-row>
      <v-col cols="12" md="6">
        <v-select
          v-model="selectedCategory"
          :items="categories"
          label="Category"
          @change="filterProducts"
          :menu-props="{ closeOnContentClick: false }"
          clearable
        />
      </v-col>
      <v-col cols="12" md="6">
        <v-slider
          v-model="priceRange"
          :max="maxPrice"
          label="Price Range"
          @input="filterProducts"
          thumb-label="always"
        />
        <div class="text-center">${{ priceRange.toFixed(2) }}</div>
      </v-col>
    </v-row>
 
    <v-row>
      <v-col
        v-for="(product, index) in paginatedProducts"
        :key="product.id"
        cols="12"
        sm="6"
        md="4"
        lg="3"
      >
        <v-card class="mx-auto my-4 product-card" max-width="344">
          <v-img :src="product.image" height="200px" />
          <v-card-title>{{ product.title }}</v-card-title>
          <v-card-subtitle>${{ product.price.toFixed(2) }}</v-card-subtitle>
          <v-card-text>{{ product.category }}</v-card-text>
          <v-btn @click="addToCart(product)">Add to Cart</v-btn>
        </v-card>
      </v-col>
    </v-row>

    <v-row class="text-center">
      <v-col cols="12">
        <v-pagination v-model="currentPage" :length="pages" @input="changePage" />
      </v-col>
    </v-row>

    <v-row align="center" justify="center">
      <v-col cols="2">
        <v-text-field v-model="goToPage" label="Go to Page" outlined dense></v-text-field>
      </v-col>
      <v-col cols="2">
        <v-btn @click="goToPageHandler" color="primary" small>Go</v-btn>
      </v-col>
    </v-row>
  </v-container>
</template>



<script>
export default {
  data() {
    return {
      products: [],
      categories: [],
      selectedCategory: '',
      priceRange: 1000,
      maxPrice: 1000,
      cart: [],
      showCart: false,
      currentPage: 1,
      itemsPerPage: 6, 
      goToPage: '', 
    };
  },
  computed: {
    cartItemCount() {
      return this.cart.length;
    },
    filteredProducts() {
      return this.products.filter(product => {
        return (
          (this.selectedCategory === '' || product.category === this.selectedCategory) &&
          product.price <= this.priceRange
        );
      });
    },
    paginatedProducts() {
      const startIndex = (this.currentPage - 1) * this.itemsPerPage;
      return this.filteredProducts.slice(startIndex, startIndex + this.itemsPerPage);
    },
    pages() {
      return Math.ceil(this.filteredProducts.length / this.itemsPerPage);
    },
  },
  async mounted() {
    try {
      const response = await fetch('https://fakestoreapi.com/products');
      const data = await response.json();
      this.products = data;
      this.categories = [...new Set(this.products.map(product => product.category))];
      this.maxPrice = Math.max(...this.products.map(product => product.price));
    } catch (error) {
      console.error('Error fetching products:', error);
    }
  },
  methods: {
    filterProducts() {
      this.currentPage = 1; 
    },
    addToCart(product) {
      this.cart.push(product);
    },
    removeFromCart(index) {
      this.cart.splice(index, 1);
    },
    changePage(page) {
      this.currentPage = page;
    },
    goToPageHandler() {
      const pageNumber = parseInt(this.goToPage);
      if (!isNaN(pageNumber) && pageNumber >= 1 && pageNumber <= this.pages) {
        this.currentPage = pageNumber
      }
      this.goToPage = ''
    },
  },
};
</script>

<style scoped>
.text-center {
  text-align: center;
}

.cart-container {
  display: flex;
  align-items: center;
}

.cart-dropdown-container {
  flex-grow: 1;
}

.cart-dropdown {
  position: relative;
  width: 25%;
  opacity: 0;
  visibility: hidden;
  transition: opacity 0.3s ease, visibility 0.3s ease;
}

.cart-dropdown-container .cart-dropdown {
  opacity: 1;
  visibility: visible;
}

.product-card {
  transition: transform 0.3s ease, box-shadow 0.3s ease; 
}

.product-card:hover {
  transform: scale(1.05); 
  box-shadow: 0px 0px 15px rgba(0, 0, 0, 0.3); 
}

.v-icon:hover {
  cursor: pointer;
}
</style>


