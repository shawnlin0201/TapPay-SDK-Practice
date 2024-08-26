<script setup>
import {ref, onMounted} from 'vue'
import axios from "axios";
import config from "@/config/paymentInfo.js";
const payButtonClass = ref(null)
const command = ref(null)
onMounted(() => {
  if (TPDirect.paymentRequestApi.checkAvailability()) {
    TPDirect.setupSDK(config.app_id, config.app_key, 'sandbox')
    TPDirect.paymentRequestApi.setupApplePay({
      merchantIdentifier: 'APMEzLyqKHy9ikwet94T',
      countryCode: 'TW',
    })
  }
})

const checkProduct = () => {
  const paymentRequest = {
    supportedNetworks: ['AMEX', 'JCB', 'MASTERCARD', 'VISA'],
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
      payButtonClass.value = 'buy'
      return
    }
    if (result.canMakePaymentWithActiveCard === true) {
      console.log('該裝置有支援的卡片可以付款')
      payButtonClass.value = 'buy'
    } else {
      console.log('該裝置沒有支援的卡片可以付款')
      payButtonClass.value = 'set-up'
    }
  })
}

const submit = () => {
  TPDirect.paymentRequestApi.getPrime(function(result) {
    console.log('paymentRequestApi.getPrime result', result)
    if (result.status !== 0) {
      console.error('getPrime failed: ' + result.msg)
      return
    }
    const prime = result.prime

    command.value = `
    curl -X POST https://sandbox.tappaysdk.com/tpc/payment/pay-by-prime \\
    -H 'content-type: application/json' \\
    -H 'x-api-key: partner_ddnaYdNtwqPdK7r1Keu8lACTy1Eousn52yhbROWoamzaAbZ0pwsq1Mqn' \\
    -d '{
        "partner_key": "partner_ddnaYdNtwqPdK7r1Keu8lACTy1Eousn52yhbROWoamzaAbZ0pwsq1Mqn",
        "prime": "${result.prime}",
        "amount": "${parseInt(result.total_amount)}",
        "merchant_id": "APMEzLyqKHy9ikwet94T",
        "details": "Some item",
        "cardholder": {
            "phone_number": "0912345678",
            "name": "Shawn",
            "email": "test@example.com",
            "zip_code": "123",
            "address": "台北市xxx街xx號",
            "national_id": "A123456789"
        }
    }'`.replace(/                /g, '')

    console.log('prime', prime)
  })

}


</script>

<template lang="pug">
  div
    button(@click='checkProduct')
      | Check Item
    div#apply-pay(:class="payButtonClass" @click="submit")
      | Pay With ApplePay {{ payButtonClass }}
    <pre>{{ command }}</pre>
</template>
<style scoped></style>
