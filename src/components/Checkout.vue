<template>
  <div>
    <h2>Checkout</h2>

    <div class="product-container">
      <div v-for="(basket,index) in cart" :key="basket.id">
        <div class="p-box">
          <img v-bind:src="basket.image" />
          <h2 v-text="basket.topic"></h2>
          <p>Location: {{basket.location}}</p>
          <p>Price: £{{basket.price}}</p>
          <!-- 
            User can remove lesson from shopping cart; the removed lesson is
            added back to the lesson list.
          -->
          <button class="remove-btn" @click="removeFromCart(index,basket._id)">remove from cart</button>
        </div>
      </div>
    </div>

    <h2>Checkout</h2>
    <!-- 
      user only can type letters in the "first" and "second" name and the user can only type numbers in the 
      ’Phone number’. the check is done using regular expressions;
    -->
    <p>
      <strong>First Name:</strong>
      <input type="text" v-model.trim="order.firstName" v-on:keypress="isLetter($event)" />
    </p>

    <p>
      <strong>Last Name:</strong>
      <input type="text" v-model.trim=" order.lastName" v-on:keypress="isLetter($event)" />
    </p>

    <strong>Phone number</strong>
    <input v-model.trim="phone_value" type="text" @input="acceptNumber" />

    <button
      :disabled="checkoutDisable(this.order.firstName,this.order.lastName,this.phone_value)"
      v-on:click="submitForm"
    >Checkout</button>
  </div>
</template>
<script>
export default {
  name: "CheckOut",
  props: ["cart"],
  data() {
    return {

      order: {
        firstName: "",
        lastName: ""
      },
      phone_value: "",
      name: "",
      phone: "",
      showProduct: false
    };
  },
  methods: {
    removeFromCart(index, d) {

      this.$emit("removeFromCart", index, d);
    },
    checkoutDisable(first, second, phone) {
      if (first.length > 0 && second.length > 0 && phone.length > 0) {
        return false;
      } else {
        return true;
      }
    },
    submitForm() {
      fetch("https://cw2-web-app.herokuapp.com/collection/orders/", {
        method: "POST",
        body: JSON.stringify({
          firstName: this.order.firstName,
          lastName: this.order.lastName,
          phone: this.phone_value,

          lessons: this.cart.map(item => {
            return {
              _id: item._id,
              spaces: item.space,
              topic: item.topic
            };
          })
        }),
        headers: {
          "Content-Type": "application/json"
        }
      })
        .then(response => response.json())
        .then(() => {
          for (var i = 0; i < this.cart.length; ++i) {
            console.log(this.cart[i]._id);
            fetch(
              "https://cw2-web-app.herokuapp.com/collection/lessons/" +
                this.cart[i]._id,
              {
                method: "PUT",
                body: JSON.stringify({
                  space: this.cart[i].space
                }),
                headers: {
                  "Content-Type": "application/json"
                }
              }
            )
              .then(response => response.json())
              .then(() => {
                location.reload();
              });
          }
        });

      alert("order submitted!");
    },
    acceptNumber() {
      var x = this.phone_value
        .replace(/\D/g, "")
        .match(/(\d{0,3})(\d{0,3})(\d{0,4})/);
      this.phone_value = !x[2] ? x[1] : x[1] + x[2] + (x[3] ? +x[3] : "");
    },
    isLetter(e) {
      let char = String.fromCharCode(e.keyCode); // Get the character
      if (/^[A-Za-z]+$/.test(char)) return true;
      // Match with regex
      else e.preventDefault(); // If not match, don't add to input text
    },

    submit() {
      alert(`Dear ${this.name}, your order has been submitted! `);
      location.reload();
    }
  }
};
</script>

