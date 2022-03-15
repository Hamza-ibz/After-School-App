<template>
  <div id="app">
    <header>
      <h1 v-text="sitename"></h1>
      <br />
      <br />
    </header>
    <div v-if="showProduct">
      <section class="product">
        <div class="p-heading">
          <h2>
            <font>After</font>School Club
          </h2>
        </div>
        <div class="product-container">
          <p>
            <input
              type="text"
              v-model="search"
              placeholder="search"
              class="search"
              v-on:keyup="filterLesson()"
            />

            <button class="basket" v-on:click="showCheckOut" v-if="this.cart.length > 0">
              {{this.cart.length}}
              <span class="fas fa-cart-plus"></span>Shopping cart
            </button>
            <button class="basket" disabled="disabled" v-else>
              <span class="fas fa-cart-plus"></span>Shopping cart
            </button>
            <br />

            <input
              type="radio"
              v-on:change="sort_a_d(sort.option)"
              id="price"
              value="price"
              v-model="sort.attributes"
            />
            <label for="price">Price</label>
            <input
              type="radio"
              v-on:click="sort_a_d(sort.option)"
              v-on:change="sort_a_d(sort.option)"
              id="location"
              value="location"
              v-model="sort.attributes"
            />
            <label for="location">Location</label>
            <input
              type="radio"
              v-on:click="sort_a_d(sort.option)"
              v-on:change="sort_a_d(sort.option)"
              id="space"
              value="space"
              v-model="sort.attributes"
            />
            <label for="space">Spaces</label>
            <input
              type="radio"
              v-on:change="sort_a_d(sort.option)"
              id="topic"
              value="topic"
              v-model="sort.attributes"
            />
            <label for="topic">Subject</label>
            <br />

            <!-- 
            There is an option to sort in ascending or descending order, regardless of the attribute selected;
            -->
            <input
              type="radio"
              v-on:click="sortLowToHigh(sort.attributes)"
              id="acending"
              value="acending"
              v-model="sort.option"
            />
            <label for="acending">acending</label>
            <input
              type="radio"
              v-on:click="sortHighToLow(sort.attributes)"
              id="decending"
              value="decending"
              v-model="sort.option"
            />
            <label for="decending">decending</label>
          </p>

          <LessonList :products="products" @addToCart="addProduct" />
         
        </div>
      </section>
    </div>

    <div v-else>
      <section class="product">
        <div class="product-container">
          <button class="basket" @click="showCheckOut">
            {{this.cart.length}}
            <span class="fas fa-cart-plus"></span>Shopping cart
          </button>
          <CheckOut :cart="cart" @removeFromCart="removeToCart" />
        </div>
      </section>
    </div>
  </div>
</template>

<style>
@import "./assets/style.css";
</style>

<script>
import LessonList from "./components/LessonList.vue";
import CheckOut from "./components/Checkout.vue";
export default {
  name: "App",
  components: {
    LessonList,
    CheckOut
  },
  data() {
    return {
      sitename: "After School Club",
      cart: [],
      showProduct: true,
      products: [],
      sort: {
        attributes: "price",
        option: ""
      },
      prodNum: true,
      search: ""
    };
  },
  methods: {
    showCheckOut() {
      this.showProduct = this.showProduct ? false : true;
    },
    canAddToCart(product) {
      return product.space > 0;
    },
    filterLesson() {
      fetch(
        "https://cw2-web-app.herokuapp.com/collection/lessons/search/" +
          this.search
      )
        .then(response => {
          return response.json();
        })
        .then(_lessons => {
          this.products = _lessons;
          
        });
    },
    addProduct(product) {
      product.space = product.space - 1;
      if (product.space <= 0) {
        this.prodNum = false;
      }
      this.cart.push(product);
    },
    sortLowToHigh(n) {
      if (n == "price") {
        return this.products.sort((a, b) => (a.price > b.price ? 1 : -1));
        // return 0;
      } else if (n == "location") {
        return this.products.sort((a, b) => (a.location > b.location ? 1 : -1));
        // return 0;
      } else if (n == "space") {
        return this.products.sort((a, b) => (a.space > b.space ? 1 : -1));
        // return 0;
      } else if (n == "topic") {
        return this.products.sort((a, b) => (a.topic > b.topic ? 1 : -1));
        // return 0;
      }
    },

    sortHighToLow(n) {
      if (n == "price") {
        return this.products.sort((a, b) => (a.price < b.price ? 1 : -1));
        // return 0;
      } else if (n == "location") {
        return this.products.sort((a, b) => (a.location < b.location ? 1 : -1));
        // return 0;
      } else if (n == "space") {
        return this.products.sort((a, b) => (a.space < b.space ? 1 : -1));
        // return 0;
      } else if (n == "topic") {
        return this.products.sort((a, b) => (a.topic < b.topic ? 1 : -1));
        // return 0;
      }
    },

    sort_a_d(n) {
      if (n == "decending") {
        this.sortHighToLow(this.sort.attributes);
      } else if (n == "acending") {
        this.sortLowToHigh(this.sort.attributes);
      }
    },
    removeToCart(index, d) {
      // console.log(index);
      for (let i = 0; i < this.products.length; i++) {
        if (this.products[i]._id === d) {
          this.products[i].space = this.products[i].space + 1;
        }
      }
      this.cart.splice(index, 1);
    }
  },
  computed: {

  },
  created: function() {
    fetch("https://cw2-web-app.herokuapp.com/collection/lessons/")
      .then(response => {
        return response.json();
      })
      .then(_lessons => {
        this.products = _lessons;
        
      });
  }
};
</script>

