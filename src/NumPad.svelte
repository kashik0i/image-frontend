<script>
  // import { kernelArray, kernelName, handleUploadFunction } from "./store.js";
  import { TextField } from "smelte";
  let defaultValue = 0;
  export let kernelArray, kernelName, handleUploadFunction;
  export let id;
</script>

<div>
  <div {id} class="block float-left w-1/3 h-1/3 ">
    <TextField
      bind:value={kernelArray[id]}
      on:change={(e) => {
        handleUploadFunction();
        if (kernelName != "custom") kernelName = "custom";
      }}
      type="number"
      {defaultValue}
      min="-10"
      max="10"
      step="0.1"
      class="w-full h-full text-xl text-center"
    />
  </div>
</div>

<style>
</style>
