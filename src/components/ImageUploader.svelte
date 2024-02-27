<script lang="ts">
   export let handleInputChange: (event: Event) => void;
   export let handleFileChange: (event: Event) => void;
   export let getGeminiResponse: (input: string) => void;
   export let handleSubmit: (event: MouseEvent) => void;
   export let input: string = "https://imgs.search.brave.com/P4xkGJ-N800TxNhQ3UAE_s16BOdmJFJiRa3N6uPLGZY/rs:fit:860:0:0/g:ce/aHR0cHM6Ly9oYW5k/eW1hbmRnLndwZW5n/aW5lcG93ZXJlZC5j/b20vd3AtY29udGVu/dC91cGxvYWRzL29s/ZF9pbWFnZXMvZy1w/ZXN0c2FuZGRpc2Vh/c2VzLW51dHJpZW50/ZGVmaWNpZW5jaWVz/LVBIT1NQSE9SVVNf/MC5qcGc";
</script>

<div class="container">
   <label class="label">Image URL:</label>
   <input
      class="input"
      on:change={handleInputChange}
      placeholder="https://example.com/image.jpg"
   />
   <div>
      <input id="upload" type="file" class="file-input" on:change={handleFileChange} />
   </div>
   <section class="button-section">
      <button
         class="button"
         on:click={() => getGeminiResponse(input)}
      >
         Submit URL
      </button>
      <div class="spacer"></div>
      <button
         class="button"
         on:click={handleSubmit}
      >
         Submit File
      </button>
   </section>
   {#if input !== ''}
      <img src={input} alt="Uploaded Image" height="450px"/>
   {/if}
</div>

<style>
   .container {
      display: flex;
      flex-direction: column;
      height: 100%;
      padding: 20px;
   }
   .label {
      margin-bottom: 4px;
      color: #833e0f; /* Example for light theme, adjust colors as needed */
      font-weight: bold;
   }
   .input {
      width: 75%;
   }
   .input, .file-input {
      border: 1px solid #ccc;
      padding: 8px;
      border-radius: 4px;
      color: black;
      background-color: #f0f0f0;
      margin-bottom: 20px;
   }
   .button-section {
      display: flex;
      flex-direction: row;
      width: 100%;
      justify-content: start;
      padding: 10px 0;
   }
   .button {
      background-color: #f0f0f0;
      color: #333;
      padding: 10px 20px;
      border: none;
      cursor: pointer;
      border-radius: 4px;
   }
   .spacer {
      width: 8px;
   }
</style>

