<template>
    <!-- Attention Banner -->
    <div class="attention-banner">
      <p>Attention: Special offer this week! Free shipping on all orders over $50. <a href="/products">Shop Now</a></p>
    </div>

    <!-- Welcome Section -->
    <div class="welcome">
      <h1 class="data a"><span class="E">E</span></h1>
      <h1 class="data b"><span class="N">N</span></h1>
      <h1 class="data c"><span class="V">V</span></h1>
      <h1 class="data d"><span class="I">I</span></h1>
      <h1 class="data e"><span class="S">S</span></h1>
      <h1 class="data j"><span class="A">A</span></h1>
      <h1 class="data g"><span class="G">G</span></h1>
      <h1 class="data h"><span class="E">E</span></h1>
    </div>

    <!-- Header Section -->
    <header class="header" id="header">
      <nav class="nav container">

        <div class="nav_menu" id="nav-menu">
          <ul class="nav_list">
            <li v-for="(item, index) in navItems" :key="index" class="nav_item">
              <a :href="item.link" :class="{ 'nav_link': true, 'active-link': index === activeIndex }">{{ item.text }}</a>
            </li>
          </ul>
        </div>

        <div class="user-info">
          <span v-if="userLoggedIn" class="material-symbols-outlined icons" :title="userName">
manage_accounts
</span>
<span v-else class="material-symbols-outlined icons" @click="this.$router.push('/login')">
person
</span>
    </div>
        <span class="material-symbols-outlined icons1">shopping_cart</span>
        <a href="#" class="button button_header poojit">Order Now!</a>
      </nav>
    </header>
  <div class="product-page">
    <div class="search-bar">
      <input v-model="search" placeholder="Search products..." />
    </div>
    <div v-if="filteredProducts.length === 0">No results found.</div>

    <ul class="cards">
      <li v-for="product in filteredProducts" :key="product.Handle">
        <a href="" class="card">
          <img :src="product['Image Src'] || product['Variant Image']" class="card__image" alt="Product Image" />
          <div class="card__overlay">
            <div class="card__header">
              <div class="card__header-text">
                <h3 class="card__title">{{ product.Title }}</h3>
                <span class="card__tagline">{{ product['Body (HTML)'].substring(0, 100) + '...' }}</span>
                <span class="card__status">{{ product['Variant Price'] }} Rupees</span>
              </div>
              <button @click="addToCart(product)" class="add-to-cart-button button1">
            <span class="material-symbols-outlined button1">
add_shopping_cart
</span> Add to Cart
        </button>
            </div>
            <p class="card__description">{{ product['Body (HTML)'].substring(0, 100) + '...' }}</p>
          </div>
        </a>
      </li>
    </ul>
  </div>
</template>

<script>
import { getAuth, onAuthStateChanged } from 'firebase/auth';
import productsData from '../assets/ss.json'; // Update the path accordingly

export default {
  data() {
    return {
      navItems: [
        { text: 'Home', link: '/' },
        { text: 'About us', link: '/about' },
        { text: 'Bracelets', link: '/products' },
        { text: 'Contact us', link: '/ContactUs' },
        { text: 'Refer & Earn 🎉🌟', link: '#referEarn' }
      ],
      search: '',
      uniqueProducts: productsData,
      allProducts: [...productsData], // Store all products for resetting
      userLoggedIn: false,
      userName: '',
      activeIndex: 0 // Set the active index based on your requirements
    };
  },
  computed: {
    filteredProducts() {
      return this.uniqueProducts.filter((product) =>
        product.Title.toLowerCase().includes(this.search.toLowerCase())
      );
    },
  },
  watch: {
    search() {
      // Update the filtered products whenever the search query changes
      this.filterUniqueProducts();
    },
  },
  methods: {
    filterUniqueProducts() {
      // Use a Set to keep track of unique product titles
      const uniqueTitles = new Set();
      this.uniqueProducts = this.filterBySearchQuery(uniqueTitles);
    },
    filterBySearchQuery(uniqueTitles) {
      return this.allProducts.filter((product) => {
        if (product.Title.toLowerCase().includes(this.search.toLowerCase()) && !uniqueTitles.has(product.Title.toLowerCase())) {
          uniqueTitles.add(product.Title.toLowerCase());
          return true;
        }
        return false;
      });
    },
  },
  created() {
    this.filterUniqueProducts();
  },
  mounted(){
    const auth = getAuth();
    onAuthStateChanged(auth, (user) => {
      if (user) {
        this.userLoggedIn = true;
        this.userName = user.displayName || user.email;
      } else {
        this.userLoggedIn = false;
        this.userName = '';
      }
    });
  }
};
</script>

<style scoped>
/* Add your custom CSS styles for the product cards here */
:root {
  --surface-color: #fff;
  --curve: 40;
}

* {
  box-sizing: border-box;
}

body {
  background-color: #fef8f8;
  scroll-behavior: smooth;
  font-family: 'Poppins', sans-serif;
  font-weight: 300;
  font-style: italic;
  overflow-x: hidden;
}

.cards {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
  gap: 2rem;
  margin: 4rem 5vw;
  padding: 0;
  list-style-type: none;
}

.card {
  position: relative;
  display: block;
  height: 100%;  
  border-radius: calc(var(--curve) * 1px);
  overflow: hidden;
  text-decoration: none;
}

.card__image {      
  width: 100%;
  height: auto;
}

.card__overlay {
  position: absolute;
  bottom: 0;
  left: 0;
  right: 0;
  z-index: 1;      
  border-radius: calc(var(--curve) * 1px);    
  background-color: var(--surface-color);      
  transform: translateY(100%);
  transition: .2s ease-in-out;
}

.card:hover .card__overlay {
  transform: translateY(0);
  /* From https://css.glass */
background: rgba(255, 255, 255, 0.25);
border-radius: 16px;
box-shadow: 0 4px 30px rgba(0, 0, 0, 0.1);
backdrop-filter: blur(11.2px);
-webkit-backdrop-filter: blur(11.2px);
border: 1px solid rgba(255, 255, 255, 0.3);
}

.card__header {
  position: relative;
  display: inline-block;
  align-items: center;
  gap: 2em;
  padding: 2em;
  border-radius: calc(var(--curve) * 1px) 0 0 0;    
  background-color: var(--surface-color);
  transform: translateY(-100%);
  transition: .2s ease-in-out;
}

.card__arc {
  width: 80px;
  height: 80px;
  position: absolute;
  bottom: 100%;
  right: 0;      
  z-index: 1;
}

.card__arc path {
  fill: var(--surface-color);
  d: path("M 40 80 c 22 0 40 -22 40 -40 v 40 Z");
}       

.card:hover .card__header {
  transform: translateY(0);
}

.card__thumb {
  flex-shrink: 0;
  width: 50px;
  height: 50px;      
  border-radius: 50%;      
}

.card__title {
  font-size: 1em;
  margin: 0 0 .3em;
  color: #6A515E;
}

.card__tagline {
  display: block;
  margin: 1em 0;
  font-family: "MockFlowFont";  
  font-size: .8em; 
  color: #D7BDCA;  
}

.card__status {
  font-size: .8em;
  color: #D7BDCA;
}

.card__description {
  padding: 0 2em 2em;
  margin: 0;
  color: #D7BDCA;
  font-family: "MockFlowFont";   
  display: -webkit-box;
  -webkit-box-orient: vertical;
  -webkit-line-clamp: 3;
  overflow: hidden;
}

.button1{
  display: inline-block;
  position: relative;
  background-color: #f6c28f;
  color: #1A1A1A;
  padding: .75rem 1.5rem;
  border-radius: 3rem;
  font-weight: 600;
  transition: .3s;
  left: 223px;
  text-decoration: none;
}

.button1:hover{
  background-color: #F4CE14;
  padding-left: 1.5rem; /* Adjust the padding to maintain size */
  padding-right: 1.5rem; /* Adjust the padding to maintain size */
  box-shadow: 20px 20px black;
}
</style>