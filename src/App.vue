<script setup>
import {ref} from "vue";
import TapPayDirect from "@/components/TapPayDirect.vue";
import TapPayLinePay from "@/components/TapPayLinePay.vue";
import TapPayApplePay from "@/components/TapPayApplePay.vue";



const selected = ref('linePay')
</script>

<template lang="pug">
  div
    | 交易方式：
    select(v-model="selected")
      option(value="direct")
        | 信用卡
      option(value="linePay")
        | Line Pay
      option(value="applePay")
        | Apple Pay
    template(v-if="selected === 'direct'")
      TapPayDirect
    template(v-else-if="selected === 'linePay'")
      TapPayLinePay
    template(v-else-if="selected === 'applePay'")
      TapPayApplePay
</template>
<style scoped>
</style>
