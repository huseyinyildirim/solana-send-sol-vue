<script>
import { RouterLink, RouterView } from 'vue-router'

import { PhantomWalletAdapter, SlopeWalletAdapter, SolflareWalletAdapter, SolletExtensionWalletAdapter, SolletWalletAdapter, TorusWalletAdapter } from '@solana/wallet-adapter-wallets'
import { initWallet } from 'solana-wallets-vue'
import { initWorkspace } from './composables'

export default {
  setup() {
    const wallets = [
        new PhantomWalletAdapter(),
        new SlopeWalletAdapter(),
        new SolflareWalletAdapter(),
        new TorusWalletAdapter(),
        new SolletWalletAdapter(),
        new SolletExtensionWalletAdapter()
    ];

    initWallet({ wallets, autoConnect: true })
    initWorkspace();
  },
    data() {
        return {};
    },
    beforeCreate() {},
    created() {},
    beforeMounted() {},
    mounted() { },
    beforeUpdate() {},
    updated() {},
    beforeUnmount() {},
    unmounted() {},
    methods: { },
    computed: { },
    watch: { },
    components: { }
}
</script>

<template>
  <RouterView/>
</template>

<style>

</style>