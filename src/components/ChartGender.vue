<template lang="pug">
section.chart

  header.chart-header

    h4.chart-title
      | 性別比例

  main.chart-body

    .row.g-0.w-100

      .col-3.d-flex.align-items-center.justify-content-center(
        v-if="percentage.length !== 0"
      )
        .fw-bold.d-flex.flex-column.align-items-center
          span {{ percentage[1].gender }}
          span  {{ percentage[1].percent }} %

      PieChart.chart-content.col-6(
        :chartData='chartData')

      .col-3.d-flex.align-items-center.justify-content-center(
        v-if="percentage.length !== 0"
      )
        .fw-bold.d-flex.flex-column.align-items-center
          span {{ percentage[0].gender }}
          span {{ 100 - percentage[1].percent }} %
</template>

<style lang="scss" scoped>
</style>

<script>
import { ref } from 'vue';
import { PieChart } from 'vue-chart-3';

export default {
  components: { PieChart },
  props: {
    apiData: {
      type: Promise,
      default() { return []; },
    },
  },
  setup(props) {
    const apiData = ref(props.apiData);

    const genders = ref([]);
    const counts = ref([]);
    const percentage = ref([]);

    apiData.value
      .then((rawData) => {
        rawData.forEach((people) => {
          const { gender } = people;
          if (!genders.value.includes(gender)) {
            genders.value.push(gender);
            counts.value[genders.value.indexOf(gender)] = 1;
            percentage.value.push({
              gender: gender.replace('性', ''),
              percent: 0,
            });
          } else {
            counts.value[genders.value.indexOf(gender)] += 1;
            const total = counts.value.reduce((x, y) => x + y);
            const percent = Math.round((counts.value[genders.value.indexOf(gender)] / total) * 100);
            percentage.value[genders.value.indexOf(gender)].percent = percent;
          }
        });
      });

    // chart
    const chartData = {
      labels: genders.value,
      datasets: [{
        data: counts.value,
        backgroundColor: [
          '#8E7DFA',
          '#D2CBFD',
        ],
      }],
    };

    return {
      chartData,
      percentage,
    };
  },
};
</script>
