<template>
<!--  <div  v-if="holidays && holidays.length > 0">

    <h2>{{ country }} holidays</h2>
    <ul class="holidays">
      <li @click="handleClickItem(item)" v-for="item in holidays" :key="item.name" :style="{background: item.name === choosed ? 'blue' : ''}">
        {{ item.name }} {{ item.date }}
      </li>
    </ul>
  </div>-->
  <div>
    <el-card class="box-card">
      <div class="clearfix">
        <span>{{ country }} holidays</span>
        <el-button style="float: right; padding: 3px 0" type="text">operation bottom</el-button>
      </div>
      <div @click="handleClickItem(item)" v-for="item in holidays" :key="item.name" :style="{background: item.name === choosed ? 'yellow' : ''}" class="text item">
        {{ item.name }} {{ item.date }}
      </div>
    </el-card>
  </div>
</template>

<script>
export default {
  name: "HolidayList",
  props: {
    country: {
      type: String,
      default: () => ''
    },
    holidays: {
      type: Array,
      default: () => []
    }
  },
  data() {
    return {
      choosed: null
    }
  },
  methods: {
    handleClickItem(item) {
      this.choosed = item.name;
      this.$emit('chooseHoliday', item);
    }
  }
};
</script>

<style scoped>
.holidays {
  height: 200px;
  overflow: auto;
} .text {
    font-size: 137px;
  }

.item {
  margin-bottom: 30px;
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
  width: 550px;
}
</style>
