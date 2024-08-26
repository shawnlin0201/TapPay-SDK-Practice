<script setup>
import {ref, onMounted} from 'vue'
import axios from "axios";
import config from '@/config/paymentInfo.js'
onMounted(() => {
  TPDirect.setupSDK(config.app_id, config.app_key, 'sandbox')
})

const three_domain_secure = ref(true)
const submit = (event) => {
  TPDirect.linePay.getPrime((res) => {
    // note: display prime info
    // console.log('getPrime', JSON.stringify(res, null, 4))

    axios.post('http://localhost:3000/line-pay', {
      prime: res.prime,
      three_domain_secure: three_domain_secure.value,
    }, {
      headers: {
        'Content-type': 'application/json; charset=UTF-8'
      }
    }).then(res => {
      TPDirect.redirect(res.data.payment_url)
    })
  })
}


</script>

<template lang="pug">
  div
    div
      | 開啟 3D 驗證：
      input(v-model="three_domain_secure" type="checkbox")
    button(@click="submit")
      | Pay With LinePay
</template>
<style scoped>
</style>
