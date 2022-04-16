<template>

<div class="container">
  <div class="row mt-3">

  <div class="col-3">

    <WalletMultiButton></WalletMultiButton>

    <div id="token-list" class="mb-3 mt-3 container" v-if="tokens.length > 0">
      <div v-for="token in tokens" :key="token.amount" class="row border border-2 p-2 rounded-3 mb-1">
        <div class="col" style="overflow:hidden; white-space:nowrap; text-overflow: ellipsis;">{{ token.symbol }}</div>
        <div class="col text-end">{{ token.amount }}</div>
      </div>
    </div>

   <div class="mb-3 container" v-if="connected_wallet !== null" v-show="false">
      <div class="row border border-2 p-2 rounded-3 mb-1'">
        <div class="col')">{{ connected_wallet }}</div>
      </div>
   </div>

  </div>
  <div class="col-9">
    
    <table id="example" class="table table-striped table-bordered">
			<tbody>
				<tr>
					<td> <a @click="transfer('6nGHjmLDhNP8FsnXABQKrukuzpmG328FEmSTtrGK7jAk')">6nGHjmLDhNP8FsnXABQKrukuzpmG328FEmSTtrGK7jAk</a></td>
				</tr>
        <tr>
					<td> <a @click="transfer('DwZvDmnP7B6JYFonXd6m3fzYW9q56y8nqfbAp7AH39KJ')">DwZvDmnP7B6JYFonXd6m3fzYW9q56y8nqfbAp7AH39KJ</a></td>
				</tr>
      </tbody>
    </table>

  </div>
  </div>
</div>
</template>

<script>

import { watchEffect } from 'vue'
import { WalletMultiButton, useWallet } from 'solana-wallets-vue'

import { clusterApiUrl, Connection, PublicKey, LAMPORTS_PER_SOL, Transaction, SystemProgram } from "@solana/web3.js";

import { TOKEN_PROGRAM_ID, getAccount } from "@solana/spl-token";
import * as SPLToken from "@solana/spl-token";

export default {
  setup() {
    const { connected, wallet } = useWallet()

    watchEffect(() => {
      
    });

    return { connected, wallet }
  },
    data() {
        return {
          tokens: [],
          connected_wallet: ''
        };
    },
    beforeCreate() { },
    created() { },
    beforeMounted() { },
    mounted() { },
    beforeUpdate() { },
    updated() { },
    beforeUnmount() { },
    unmounted() { },
    methods: {
      async transfer(wa){


        const connection = new Connection(clusterApiUrl('testnet'))
        const { publicKey, sendTransaction } = useWallet();
        if (!publicKey.value) return;

        const transaction = new Transaction().add(
          SystemProgram.transfer({
            fromPubkey: publicKey.value,
            toPubkey: new PublicKey(wa),
            lamports: 1*LAMPORTS_PER_SOL,
          })
        );

        const signature = await sendTransaction(transaction, connection);
        await connection.confirmTransaction(signature, 'processed');

        console.log("SIGNATURE", signature)
    }
    },
    computed: { },
    watch: {
      async connected(newConnected, oldConnected) {
        if (this.connected) {
          if (this.connected == true) {

            this.connected_wallet = this.wallet.publicKey;
            
            let connection = new Connection(clusterApiUrl("testnet"));

            let solBalance = await connection.getBalance(new PublicKey(this.wallet.publicKey));

            this.tokens.push({
              symbol: 'SOL',
              amount: solBalance / LAMPORTS_PER_SOL
            });

            let splTokensBalance = await connection.getTokenAccountsByOwner(
              new PublicKey(this.wallet.publicKey), // owner here
              {
                programId: TOKEN_PROGRAM_ID,
              }
            );

            splTokensBalance.value.forEach((e) => {
              const accountInfo = SPLToken.AccountLayout.decode(e.account.data);

              this.tokens.push({
              symbol: accountInfo.mint,
              amount: accountInfo.amount
            });
            });
          }
        }
      }
    },
    components: { WalletMultiButton }
}
</script>

<style scoped>
</style>