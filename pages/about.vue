<template>
  <div>
    <section>
      <b-container class="main pt-4 pb-5">
        <h1 class="mb-4">ABOUT</h1>
        <p>This is about <a href="https://polkadot.network" target="_blank"><strong>Polkadot</strong></a> and now <a href="https://kusama.network" target="_blank"><strong>Kusama</strong></a>, of course ;-)</p>
        <p>I code this app to experiment and learn about how to retrieve and represent live (and useful for humans :-)) information about the Polkadot network. </p>
        <p>Thanks to all the people from <a href="https://riot.im/app/#/room/#polkadot-watercooler:matrix.org" target="_blank">Polkadot Watercooler</a> for point me to the right direction and save me hours of useless coding.</p>
      
        <h2 class="mt-5 mb-4">CHANGELOG</h2>

        <p><strong>26/10/2019</strong></p>

        <ul>
          <li>Version 2!</li>
          <li>New GitHub repos: <a href="https://github.com/Colm3na/polkastats-v2" target="_blank">https://github.com/Colm3na/polkastats-v2</a> and <a href="https://github.com/Colm3na/polkastats-backend-v2" target="_blank">https://github.com/Colm3na/polkastats-backend-v2</a></li>
          <li>Added Kusama CC2 network support.</li>
          <li>Changed default network to Kusama CC2 (<a href="https://polkastats.io" target="_blank">https://polkastats.io</a>).</li>
          <li>Alexander network now is at <a href="https://alexander.polkastats.io" target="_blank">https://alexander.polkastats.io</a>.</li>
          <li>Improved MySQL backend!</li>
        </ul>

        <p><strong>07/08/2019</strong></p>

        <ul>
          <li>Use new <a href="https://github.com/polkadot-js/ui/tree/master/packages/vue-identicon" target="_blank">vue-identicon component</a>.</li>
          <li>Use formatBalance function from @polkadot/util</li>
        </ul>        

        <p><strong>01/08/2019</strong></p>

        <ul>
          <li>Polkadot identity icons support</li>
          <li>New weekly and monthly total bonded charts</li>
          <li>Show period changes in daily, weekly and monthly total bonded charts</li>
          <li>Show last block and session/era info</li>
          <li>Improved navigation speed</li>
        </ul>
      </b-container>
    </section>
  </div>
</template>
<script>
import axios from 'axios'
export default {
  head () {
    return {
      title: 'PolkaStats - About PolkaStats',
      meta: [
        { hid: 'description', name: 'description', content: 'About PolkaStats' }
      ]
    }
  }
}
</script>
<style>
</style>