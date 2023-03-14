<template>
  <div class="col-md-6">
    <div v-if="!paidFor">
      <h1> Compra esta Lámpara - ${{ product.price }}</h1>

      <p>{{ product.description }}</p>

    </div>

    <div v-if="paidFor">
      <h1>¡has comprado una hermosa lámpara!</h1>
    </div>

 <div id="xxx" :src="image">
  <img :src="image" alt="">
 </div>

    <div ref="paypal"></div>
  </div>
</template>

<script>
// import image from "../assets/lamp.png"
export default {
  name: "HelloWorld",

  data: function() {
    return {
      loaded: false,
      paidFor: false,
      image: 'https://pandorafms.com/blog/wp-content/uploads/2019/02/para-que-sirve-una-API-featured.png',
      product: {
        price: 777.77,
        description: "lámpara de pierna de esa película",
        img: "./assets/logo.png"
      }
    };
  },
  mounted: function() {
    const script = document.createElement("script");
    script.src =
      "https://www.paypal.com/sdk/js?client-id=AQL_P8EX1dw50RGbVWAhG9qWQVN2wzPSMhILyEJbV9roAdndxTRpPuKKwHSRz9dZNKKwicE8jUT0UR3T";
    script.addEventListener("load", this.setLoaded);
    document.body.appendChild(script);
  },
  methods: {
    setLoaded: function() {
      this.loaded = true;
      window.paypal
        .Buttons({
          createOrder: (data, actions) => {
            return actions.order.create({
              purchase_units: [
                {
                  description: this.product.description,
                  amount: {
                    currency_code: "USD",
                    value: this.product.price
                  }
                }
              ]
            });
          },
          onApprove: async (data, actions) => {
            const order = await actions.order.capture();
            this.paidFor = true;
            console.log(order);
          },
          onError: err => {
            console.log(err);
          }
        })
        .render(this.$refs.paypal);
    }
  }
};
</script>