<script lang="ts">
   import { Line } from 'svelte-chartjs'
   export let labelData: string[] = [];
   export let percentData: string[] = [];
</script>
<div class="line-chart">
   <Line
      data={{
         labels: labelData,
         datasets: [{
            label: 'Deficiencies',
            data: percentData.map((percent) => parseInt(percent)),
             backgroundColor: [
               'rgba(201, 203, 207, 0.2)'
             ],
             borderColor: [
               'rgba(75, 192, 192, 0.2)',
             ],
         }]
      }}
   />
</div>
<style>
   .line-chart {
      width: 70%;
   }
</style>
