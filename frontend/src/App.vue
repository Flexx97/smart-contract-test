<script setup>
// This starter template is using Vue 3 <script setup> SFCs
// Check out https://vuejs.org/api/sfc-script-setup.html#script-setup
import { ref } from '@vue/reactivity'
import HelloWorld from './components/HelloWorld.vue'
import { ethers } from 'ethers'
import { onBeforeMount } from '@vue/runtime-core'

const provider = new ethers.providers.Web3Provider(window.ethereum)
provider.send('eth_requestAccounts', [])
const signer = provider.getSigner()
const contractAddress = '0xc5C487289F8EDCc193e0117E0722d44A8b818b74'
const ABI = '[{"inputs": [],"stateMutability": "nonpayable","type": "constructor"},{"inputs": [],"name": "getLatestPrice","outputs": [{"internalType": "int256","name": "","type": "int256"}],"stateMutability": "view","type": "function"},{"inputs": [],"name": "storeLatestPrice","outputs": [],"stateMutability": "nonpayable","type": "function"},{"inputs": [],"name": "storedPrice","outputs": [{"internalType": "int256","name": "","type": "int256"}],"stateMutability": "view","type": "function"}]'
const contract = new ethers.Contract(contractAddress, ABI, signer)

const prices = ref(0)

const getStoredPrice = async () => {
    try {
      const contractPrice = await contract.storedPrice();
      prices.value = parseInt(contractPrice) / 100000000
    } catch (error) {
      console.log("getStoredPrice Error: ", error);
    }
  }
const updatePrice = async () => {
    try {
      const transaction = await contract.storeLatestPrice();
      await transaction.wait();
      await getStoredPrice();
    } catch (error) {
      console.log("updatePrice Error: ", error);
    }
  }
  onBeforeMount(() => {
    getStoredPrice()
  })
</script>

<template>
  <div class="card">
    <button type="button" @click="updatePrice">update Prices</button>
    <h1>Prices: {{prices}}</h1>
  </div>
</template>

<style scoped>
.logo {
  height: 6em;
  padding: 1.5em;
  will-change: filter;
}
.logo:hover {
  filter: drop-shadow(0 0 2em #646cffaa);
}
.logo.vue:hover {
  filter: drop-shadow(0 0 2em #42b883aa);
}
</style>
