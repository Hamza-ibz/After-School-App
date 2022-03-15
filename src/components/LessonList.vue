<template>
  <div>
    <div class="product-container">
      <!-- <section class="product"> -->
      <div v-for="product in products" :key="product.id">
        <div class="p-box">
          <!-- <div class="column"> -->
          <!-- <figure> -->
          <img v-bind:src="product.image" />
          <!-- </figure> -->
          <h2>Subject: {{product.topic}}</h2>
          <p>Location: {{product.location}}</p>
          <p>Price: £{{product.price}}</p>
          <p>Spaces: {{product.space}}</p>
          <span v-if="product.space === 0">All out!!!</span>
          <span v-else-if="product.space <= 3 ">Only {{product.space}} left!</span>
          <span v-else>Buy now!!</span>
          
          <button
            class="buy-btn"
            @click="addToCart(product)"
            v-if="canAddToCart(product)"
          >Add to cart</button>
          <button disabled="disabled" v-else>Add to cart</button>
          <!-- </div> -->
        </div>
      </div>
    </div>
    <!-- </section> -->
  </div>
</template>

<script>
export default {
  name: "LessonList",
  props: ["products"],
  data() {
    return {};
  },
  methods: {
    addToCart(product) {
      // product.space--;
      // console.log("emitting value!");
      this.$emit("addToCart", product);
    },

    canAddToCart(product) {
      return product.space > 0;
    }
  }
};
</script>
