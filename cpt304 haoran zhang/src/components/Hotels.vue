<template>
  <div>
    <h1>------------------Hotel----------------------</h1>
    <!--<ul>
      <li v-for="(item, index) in hotels" :key="index">{{ item.name }}  {{ item.destinationId }}</li>
    </ul>-->
    <el-card class="box-card">
      <div  v-for="(item, index) in hotels" :key="index">{{ item.name }}  {{ item.destinationId }} class="text item">
        {{ item.name }} {{ item.destinationId }}
      </div>
    </el-card>
  </div>
</template>

<script>
export default {
  name: 'Hotels',
  props: {
    hotels: {
      type: Array,
      default: () => []
    }
  }
}
</script>

<style>
  .text {
    font-size: 14px;
  }

  .item {
    margin-bottom: 18px;
  }

  .clearfix:before,
  .clearfix:after {
    display: table;
    content: "";
  }
  .clearfix:after {
    clear: both
  }

  .box-card {
    width: 480px;
  }
</style>