<!-- IssueList.svelte -->
<script lang="ts">
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

   export let data: Data | null;
   export let graphButtons: GraphButton[] = [];

</script>

{#if data}
  <div class="issue-list-container">
    <div>
      {#if data.type}
        <div class="plant-type">{data.type}</div>
      {/if}
    </div>
   <ul class="issue-list">
      {#each data.issues as item (item.name)}
         <li class="issue-item">
            <h3 class="issue-title">{item.name}</h3>
            <p class="issue-description">{item.description}</p>
            <p class="issue-percent">Probability: {item.percent}</p>
         </li>
      {/each}
    </ul>
      {#each graphButtons as button}
         <button class="graph-button" on:click={() => {button.onClick(button.name)}}>{button.name}</button>
      {/each}
  </div>
{/if}

<style>
   .graph-button {
      margin: 1rem;
      width: 150px;
   }
   .plant-type {
      font-size: 2.5rem; /* For lg:text-3xl */
      font-weight: bold;
      color: #833e0f; /* Example for light theme, adjust colors as needed */
   }
   .issue-item {
      padding: 1px 10px;
      margin: 15px 0;
      box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1), 0 2px 4px -1px rgba(0, 0, 0, 0.06); 
      border-radius: 0.375rem;
      background-color: #404040;
   }
   .issue-list-container {
      padding: 15px;
      text-align: left;
      border-radius: 0.375rem;
      box-shadow: 0 10px 15px -3px rgba(0, 0, 0, 0.1), 0 4px 6px -4px rgba(0, 0, 0, 0.1); 
   }
   .issue-list {
      list-style: none;
      padding: 0;
      overflow-y: auto; /* Enable vertical scrolling */
      max-height: 800px; /* Adjust based on your needs */
      align-items: center;
      max-width: 1200px;
   }

   /* Optional: Improve scroll bar appearance (for WebKit browsers) */
   .issue-list::-webkit-scrollbar {
      width: 12px;
   }

   .issue-list::-webkit-scrollbar-track {
      background: #f1f1f1;
   }

   .issue-list::-webkit-scrollbar-thumb {
      background: #888;
   }

   .issue-list::-webkit-scrollbar-thumb:hover {
      background: #555;
   }
</style>

