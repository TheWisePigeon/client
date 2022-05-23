<template>
  <div class="center">
    <form @submit.prevent="pay(domain, subPlan, curr)" action="" method="post">
      <label>Plan</label><br>
      Mensuel<input type="radio" name="plan" value="Plan1" v-model="subPlan" id=""><br>
      Annuel<input type="radio" name="plan" value="Plan2" v-model="subPlan" id=""><br>
      <label>Currency</label>
      <select name="" v-model="curr" id="">
        <option value="usd" >usd</option>
        <option value="eur" >eur</option>
      </select><br>
      <button v-if="!loading" type="submit">Pay</button>
      <button v-else >Loading</button>
    </form>
  </div>
</template>

<script>
import { loadStripe } from '@stripe/stripe-js'
export default {
  name: 'IndexPage',
  data() {
    return {
      stripe: null,
      subPlan: '',
      curr: '',
      domain: 'http://localhost:3000/',
      loading: false
    }
  },
  computed:{
    amount(){
      return this.subPlan=='Plan1'? '10000' : '9600'
    }
  },
  methods: {
    
    async pay(domain, subPlan, curr, amount) {
      this.loading = true
      var params = {
        "domain_url": domain,
        "plan": subPlan,
        "currency": curr,
        "amount": amount
      }
      const sessionID = await this.$axios.$post('http://10.0.0.61:5000/api/v1/stripe/getSessionId', params )
      .then(function (res) {
        console.log(res)
        return res.sessionId
      })
      .catch(function(error){
        console.log(error);
        return error
      })
      this.loading = false
      return this.stripe.redirectToCheckout({sessionId: sessionID})
    },
  },
  async created() {
    console.log('created')
    const pkey = await fetch('http://10.0.0.61:5000/api/v1/stripe/getKey')
      .then((result) => result.json())
      .then((data) => data.public_key)
    const stripeInstance = await loadStripe(pkey)
    this.stripe = stripeInstance
    console.log(this.stripe);
  },
}
</script>

<style scoped>
.center{
display:flex; 
justify-content:center;
align-items:center;
height:100vh;
}
</style>