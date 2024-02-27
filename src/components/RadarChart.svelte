<!-- RadarChart.svelte -->
<script lang="ts">
   import { Radar } from 'svelte-chartjs'
   export let labelData: string[] = [];
   export let percentData: string[] = [];
</script>
<div class="radar-graph">
   <Radar
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
            pointBackgroundColor: 'rgba(75, 192, 192, 0.2)',
            fill: true,
         }]
      }}
      title="Deficiencies"
   />
</div>
<style>
   .radar-graph {
      width: 50%;
      padding: 15px;
   }
</style>
