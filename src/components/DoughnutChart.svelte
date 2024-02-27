<script lang="ts">
   import { Doughnut } from 'svelte-chartjs'
   export let labelData: string[] = [];
   export let percentData: string[] = [];
</script>
<div class="doughnut-chart">
   <Doughnut
      data={{
         labels: labelData,
         datasets: [{
            label: 'Deficiencies',
            data: percentData.map((percent) => parseInt(percent)),
             backgroundColor: [
               'rgba(255, 205, 86, 0.2)',
               'rgba(75, 192, 192, 0.2)',
               'rgba(54, 162, 235, 0.2)',
               'rgba(153, 102, 255, 0.2)',
               'rgba(201, 203, 207, 0.2)'
             ],
             borderColor: [
               'rgb(255, 205, 86)',
               'rgb(75, 192, 192)',
               'rgb(54, 162, 235)',
               'rgb(153, 102, 255)',
               'rgb(201, 203, 207)'
             ],
         }]
      }}
   />
</div>
<style>
   .doughnut-chart {
      width: 40%;
   }
</style>
