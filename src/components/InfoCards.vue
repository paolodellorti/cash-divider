<template>
  <div>
    <div
      v-for="(quantity, days) in people"
      :key="days + 'people'"
      class="card"
    >
      <b>{{ quantity }}</b> person{{ quantity == 1 ? 'a' : 'e' }} con <b>{{ days }}</b> giorni di presenza
    </div>
    <div
      v-for="(familiesData, familyCapacity) in families"
      :key="familyCapacity + 'families'"
    >
      <div
        v-for="(familiesQuantity, days) in familiesData"
        :key="days + 'familiesQuantity'"
        class="card"
      >
        <b>{{ familiesQuantity }}</b> famigli{{ familiesQuantity == 1 ? 'a' : 'e' }} di <b>{{ familyCapacity }}</b> person{{ familyCapacity == 1 ? 'a' : 'e' }} con <b>{{ days }}</b> giorni di presenza
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'InfoCards',
  props: {
    people: {
      type: Object,
      default: null
    },
    families: {
      type: Object,
      default: null
    },
  },
}
</script>

<style scoped>
.card {
  padding: 10px;
  width: 100%;
  background: #fff;
  border-radius: 4px;
  margin-bottom: 10px;
  box-shadow: 0 1px 2px rgba(0,0,0,.12);
  box-sizing: border-box;
}
</style>
