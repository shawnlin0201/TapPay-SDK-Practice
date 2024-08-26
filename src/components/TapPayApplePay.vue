<script setup>
import {onMounted} from 'vue'
import axios from "axios";
import config from "@/config/paymentInfo.js";

onMounted(() => {
  TPDirect.setupSDK(config.app_id, config.app_key, 'sandbox')
  TPDirect.card.setup({
    fields: {
      number: {
        element: '#card-number',
        placeholder: '**** **** **** ****'
      },
      expirationDate: {
        element: '#card-expiration-date',
        placeholder: 'MM / YY'
      },
      ccv: {
        element: '#card-ccv',
        placeholder: '後三碼'
      }
    },
    styles: {
      'input': {
        'color': 'gray'
      },
      '.valid': {
        'color': 'green'
      },
      '.invalid': {
        'color': 'red'
      },
    },
    isMaskCreditCardNumber: false,
  })
})

const submit = () => {
  const tappayStatus = TPDirect.card.getTappayFieldsStatus()
  if (tappayStatus.canGetPrime === false) {
    alert('can not get prime')
    return
  }
  TPDirect.card.getPrime((result) => {
    if (result.status !== 0) {
      console.log('get prime error ' + result.msg)
      return
    }
    console.log('get prime 成功，prime: ' + result.card.prime)
    // todo: localhost server 方案
    // axios.post('http://localhost:3000/direct', {
    //   prime: result.card.prime,
    // }, {
    //   headers: {
    //     'Content-type': 'application/json; charset=UTF-8'
    //   }
    // }).then(res => {
    //   console.log(res)
    // })
  })
}


</script>

<template lang="pug">
  | test
  div
    | 卡片號碼
    #card-number.tpfield
    | 卡片到期日
    #card-expiration-date.tpfield
    | 卡片後三碼
    #card-ccv.tpfield
    button(@click='submit')
      | Pay Direct
</template>
<style scoped>
.tpfield {
  height: 40px;
  width: 300px;
  border: 1px solid gray;
  margin: 5px 0;
  padding: 5px;
}
</style>
