<template>
  <div class="center">
    <form @submit.prevent="pay(domain, subPlan, curr)" action="" method="post">
      <label>Plan</label><br />
      Mensuel<input
        type="radio"
        name="plan"
        value="1"
        v-model="subPlan"
        id=""
      /><br />
      Annuel<input
        type="radio"
        name="plan"
        value="12"
        v-model="subPlan"
        id=""
      /><br />
      <label>Server</label>
      <select name="" v-model="curr" id="">
        <option value="m1S">m1Small</option>
        <option value="m1M">m1Medium</option>
        <option value="m2S">m2Small</option>
        <option value="m2M">m2Medium</option>
        <option value="m2M2">m2Medium2</option>
        <option value="m2L">m2Large</option></select
      ><br />
      <p>{{ curr }}</p>
      <button v-if="!loading" type="submit">Pay</button>
      <button v-else>Loading</button>
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
      loading: false,
    }
  },
  computed: {
    amount() {
      return this.subPlan == 'Plan1' ? '10000' : '9600'
    },
  },
  methods: {
    async pay(domain, subPlan, curr) {
      this.loading = true
      var data = {
        "username": "albandev36216@gmail.com",
        "password": "@#Alban36216",
        "name": "server_bruhtest",
        "workspacename": "albandev_workspace",
        "flavorid": "1",
        "flavorname": "m1.medium",
        "osname": "CentOS8",
        "workspaceid": "8f8ba9a003944d2f9f67fa09c39adbbd",
        "Int_Net_ID": "485e7393-a3ac-4add-89a5-25600cc46bd6"
      }
      var params = {
        server: JSON.stringify(data),
        plan: 2,
        mail: 'jdogbevi@axe-tag.fr',
        sub_type: 'm',
        event: 'subscription' 
      }
      const sessionID = await this.$axios
        .$post('http://10.0.0.61:5000/api/v1/stripe/getSessionId', params)
        .then(function (res) {
          console.log(res)
          return res.sessionId
        })
        .catch(function (error) {
          console.log(error)
          return error
        })
      this.loading = false
      return this.stripe.redirectToCheckout({ sessionId: sessionID })
    },
  },
  async created() {
    console.log('created')
    const pkey = await fetch('http://10.0.0.61:5000/api/v1/stripe/getKey', {
      // credentials: 'include',
      // headers: {
      //   "Authorization": 'Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJleHAiOjE2NTM5NTM0ODEsImlhdCI6MTY1Mzg2NzA4MSwic3ViIjoiYWxiYW5kZXYzNjIxNkBnbWFpbC5jb20ifQ.osjQC6Kud6cbQEMS8S-QHUx5thPOJFmv5H-4eJA-5-8'
      // }
    })
      .then((result) => result.json())
      .then((data) => data.public_key)
    const stripeInstance = await loadStripe(pkey)
    this.stripe = stripeInstance
    console.log(this.stripe)
  },
}
</script>

<style scoped>
.center {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
}
</style>
