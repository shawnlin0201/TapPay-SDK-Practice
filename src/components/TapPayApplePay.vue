<script setup>
import {onMounted} from 'vue'
import axios from "axios";
import config from "@/config/paymentInfo.js";

onMounted(() => {
  TPDirect.setupSDK(config.app_id, config.app_key, 'sandbox')
  TPDirect.paymentRequestApi.setupApplePay({
    merchantIdentifier: 'APMEzLyqKHy9ikwet94T',
    countryCode: 'TW',
  })
})

const submit = () => {
  const paymentRequest = {
    supportedNetworks: ['VISA'],
    supportedMethods: ['apple_pay'],
    displayItems: [{
      label: 'TapPay - iPhone8',
      amount: {
        currency: 'TWD',
        value: '1.00'
      }
    }],
    total: {
      label: '付給 TapPay',
      amount: {
        currency: 'TWD',
        value: '1.00'
      }
    },
    shippingOptions: [{
      id: "standard",
      label: "🚛 Ground Shipping (2 days)",
      detail: 'Estimated delivery time: 2 days',
      amount: {
        currency: "TWD",
        value: "5.00"
      }
    },
      {
        id: "drone",
        label: "🚀 Drone Express (2 hours)",
        detail: 'Estimated delivery time: 2 hours',
        amount: {
          currency: "TWD",
          value: "25.00"
        }
      },
    ],
    options: {
      requestPayerEmail: false,
      requestPayerName: false,
      requestPayerPhone: false,
      requestShipping: false,
      shippingType: 'shipping'
    }
  }

  TPDirect.paymentRequestApi.setupPaymentRequest(paymentRequest, function (result) {
    if (!result.browserSupportPaymentRequest) {
      console.log('瀏覽器不支援 PaymentRequest')
      return
    }
    if (result.canMakePaymentWithActiveCard === true) {
      console.log('該裝置有支援的卡片可以付款')
    } else {
      console.log('該裝置沒有支援的卡片可以付款')
    }
  })
}


</script>

<template lang="pug">
  div
    button(@click='submit')
      | Pay With ApplePay
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
