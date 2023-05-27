<template>
  <header>
    <div class="header flex justify-center items-center" id="header">
      <div class="container flex flex-col items-start text-white">
        <h1 class="font-bold">Carbon Credits su Blockchain</h1>
        <h2 class="text-gray-400">Crediti di carbonio certificati su Blockchain</h2>
      </div>
    </div>
  </header>
  <main>
    <tableContent></tableContent>
  </main>
</template>

<script>
import TableContent from './components/TableContent.vue';

export default {
  name: 'App',
  components: {
    TableContent
  }
}
</script>
