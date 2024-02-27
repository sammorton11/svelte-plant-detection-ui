<script lang="ts">
   import { writable, type Writable  } from 'svelte/store';
   import ImageUploader from './components/ImageUploader.svelte';
   import IssueList from './components/IssueList.svelte';
   import 'chart.js/auto';
   import BarGraph from './components/BarGraph.svelte';
   import RadarChart from './components/RadarChart.svelte';
   import LineChart from './components/LineChart.svelte';
   import PolarAreaChart from './components/PolarAreaChart.svelte';
   import DoughnutChart from './components/DoughnutChart.svelte';
   
   function toggle() {
	   window.document.body.classList.toggle('dark')
   }

   interface GraphButton {
      name: string;
      onClick: (name: string) => void;
   }

   interface Issue {
      name: string;
      description: string;
      percent: string;
   }

   interface Data {
      type: string;
      issues: Issue[];
   }

   const fakeData: Data = {
     "type": "Tomato",
     "issues": [
       {
         "name": "Nitrogen Deficiency",
         "description": "Symptoms include older leaves turning yellowish-green and may become brown and die. Plant growth is stunted, and leaves are smaller than usual. Produces fewer, smaller fruits. Solutions include adding composted manure or compost rich in organic matter, using a balanced fertilizer with a higher nitrogen ratio (e.g., 10-5-5 NPK), and conducting regular soil testing to adjust soil based on results.",
         "percent": "80"
       },
       {
         "name": "Phosphorus Deficiency",
         "description": "Symptoms include dark green or purplish leaves, and slow growth. Roots may be stunted. Solutions involve adding bone meal or rock phosphate to the soil, and using a high-phosphorus fertilizer to promote root development.",
         "percent": "45"
       },
       {
         "name": "Potassium Deficiency",
         "description": "Leaves show yellowing at the edges, brown scorching, and curling. Fruit quality is poor. Solutions include using a potassium-rich fertilizer (e.g., sulfate of potash) and avoiding high nitrogen fertilizers that can exacerbate the deficiency.",
         "percent": "20"
       },
     ]
   }


   let data: Data | null = fakeData;
   let input = writable("");
   let loading = writable(false);
   let error = writable("");
   let file: File | null = null;

   let currentGraph = 'Bar';
   let graphButtons = ['Bar', 'Radar', 'Polar Area', 'Line', 'Doughnut'];
   let mappedGraphButtons: GraphButton[] = graphButtons.map((button) => {
      return {
         name: button,
         onClick: (name: string) => {
            currentGraph = name;
            graphSwitcher(button, currentGraph);
         }
      };
   });

   function graphSwitcher(graphType: string, currentGraph: string) {
      switch (graphType) {
         case 'Bar':
            currentGraph = 'Bar';
            break;
         case 'Radar':
            currentGraph = 'Radar';
            break;
         case 'Pie':
            currentGraph = 'Pie';
            break;
         case 'Line':
            currentGraph = 'Line';
            break;
         case 'Doughnut':
            break; 
      }
   }

   let labelData: Writable<string[]> = writable([""]);
   let percentData: Writable<string[]> = writable([""]);

   function updateData() {
      if (data !== null) {
         const issueNames = data.issues.map(issue => issue.name);
         const issuePercents = data.issues.map(issue => issue.percent);
         labelData.set(issueNames);
         percentData.set(issuePercents);
      }
   }

   $: data, updateData(); 
   async function getGeminiResponse(input: string) {
      loading.set(true);
      if (!input || input === "") return;
      const opts = {
         method: 'POST',
         headers: {
            'Content-Type': 'application/json'
         },
         body: JSON.stringify({
            image_url: input
         })
      };

      const response = await fetch('http://127.0.0.1:5001/process_image_url', opts);

      if (response.ok) {
         const responseData = await response.json();
         if (responseData.error) {
            return;
         }
         const result = responseData;
         data = result;
         console.log(data);
      }
      loading.set(false);
   }

   async function sendDataToServer() {
      if (!file) return;
      error.set("");
      loading.set(true);

      const formData: FormData = new FormData();
      formData.append('img', file);

      const response = await fetch('http://localhost:5001/process_image_file', {
         method: 'POST',
         body: formData,
      });

      if (response.ok) {
         const result = await response.json();
         data = result;
      } else {
         console.log('Error:', response);
      }
      loading.set(false);
   }

   function handleInputChange(event: Event) {
      const target = event.target as HTMLInputElement;
      input.set(target.value);
   }

   function handleFileChange(event: Event) {
      const target = event.target as HTMLInputElement;
      if (target.files && target.files[0]) {
         file = target.files[0];
         const reader = new FileReader();
         reader.onload = (e) => {
            console.log(e.target?.result);
            input.set(e.target?.result as string);
         };
         reader.readAsDataURL(file);
      }
   }

   function handleSubmit(event: Event) {
      event.preventDefault();
      sendDataToServer();
   }
</script>

<div class="main-container">
   <header class="header">
      <div>
         <h1>Plant Deficiency Identifier</h1>
         <p>Upload an image of a plant to identify potential deficiencies</p>
      </div>
      <div>
         <button>Sign In</button>
         <button on:click={toggle}>Theme</button>
      </div>
   </header>
   <section class="controls-container">
      <ImageUploader
         handleInputChange={handleInputChange}
         handleFileChange={handleFileChange}
         getGeminiResponse={getGeminiResponse}
         handleSubmit={handleSubmit}
         input={$input}
      />
      <IssueList 
         data={data}
         graphButtons={mappedGraphButtons}
      />
      {#if $loading}
         <p>Loading...</p>
      {/if}

      {#if $error}
         <p>{$error}</p>
      {/if}
   </section>
   <section class="graphs">
      <div class="mini-issue-list">
         {#if data}
            {#each data.issues as item, index (item.name)}
               <li class="issue-item">
                  <h3 class="issue-title">{index + 1}. {item.name}</h3>
               </li>
            {/each}
         {/if}
      </div>
      {#if currentGraph === 'Bar'}
         <BarGraph
            labelData={$labelData}
            percentData={$percentData}
         />
      {/if}
      {#if currentGraph === 'Radar'}
         <RadarChart
            labelData={$labelData}
            percentData={$percentData}
         />
      {/if}
      {#if currentGraph === 'Line'}
         <LineChart
            labelData={$labelData}
            percentData={$percentData}
         />
      {/if}
      {#if currentGraph === 'Doughnut'} 
         <DoughnutChart
            labelData={$labelData}
            percentData={$percentData}
         />
      {/if}
      {#if currentGraph === 'Polar Area'}
         <PolarAreaChart
            labelData={$labelData} 
            percentData={$percentData}
         />
      {/if}
   </section>
   <footer class="footer">
      <div class="footer-col-01">
         <p>Plant Deficiency Identifier</p>
         <p>Powered by Gemini © 2024 Gemini</p>
         <p>Version 1.0</p>
      </div>
   </footer>
</div>

<style>
   :global(body) {
      font-family: 'Roboto', sans-serif;
      margin: 0;
      padding: 0;
      box-sizing: border-box;
   }
   :global(body.dark) {
      background-color: #242424;
   }
   .main-container {
      display: flex;
      flex-direction: column;
      justify-content: center;
      padding: 17px;
      height: 100%;
   }
   .controls-container {
      display: flex;
      flex-direction: row;
      justify-content: space-evenly;
      padding: 17px;
      height: 100%;
   }
   .mini-issue-list {
      list-style: none;
      padding: 0;
      align-items: center;
   }
   .issue-item {
      padding: 1rem;
      margin: 0.5rem 0;
      box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1), 0 2px 4px -1px rgba(0, 0, 0, 0.06);
      border-radius: 0.375rem;
   }
   .graphs {
      display: flex;
      flex-direction: row;
      justify-content: space-evenly;
      width: 100%;
   }
   .header {
      display: flex;
      flex-direction: row;
      justify-content: space-between;
      padding: 17px;
      height: 100%;
   }
   .footer {
      display: flex;
      flex-direction: row;
      justify-content: start;
      padding: 17px;
      height: 100%;
      padding-top: 100px;
   }
   .footer-col-01 {
      display: flex;
      flex-direction: column;
      justify-content: center;
      height: 100%;
   }
</style>
