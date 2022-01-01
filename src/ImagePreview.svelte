<!-- Special thanks to Dcode for this tutorial: https://youtu.be/VElnT8EoEEM -->
<script>
  import { Button } from "smelte";
  // import { showImage, inputImageElement, inputFileElement } from "./store";
  export let container = undefined;
  export let placeholder = undefined;
  export let showImage = undefined;
  export let inputImageElement = undefined;
  export let inputFileElement = undefined;
  export let inputFile = undefined;
  export let interpolationSize = undefined;
  function onChange(event) {
    console.log(inputFileElement);
    const file = inputFileElement.files[0];
    console.log(file);
    if (file) {
      showImage = true;

      const reader = new FileReader();
      reader.addEventListener("load", function (e) {
        inputImageElement.setAttribute("src", reader.result);

        inputFile = file;
        interpolationSize.width = inputImageElement.clientWidth;
        interpolationSize.height = inputImageElement.clientHeight;
        console.log(interpolationSize);
      });
      reader.readAsDataURL(file);

      return;
    }
    showImage = false;
  }
  const removeImage = () => {
    showImage = false;
    inputFileElement.value = "";
    inputImageElement.setAttribute("src", "");
  };
</script>

<h5>Image Preview on File Upload</h5>
<input
  accept="image/*"
  bind:this={inputFileElement}
  on:change={onChange}
  type="file"
/>
{#if showImage}
  <Button on:click={removeImage}>remove</Button>
{/if}
<div bind:this={container}>
  {#if showImage}
    <img bind:this={inputImageElement} alt="Preview" />
  {:else}
    <span bind:this={placeholder}>Image Preview</span>
  {/if}
</div>

<style>
  div {
    min-height: 100px;
    border: 2px solid #ddd;
    margin-top: 15px;

    align-items: center;
    justify-content: center;
    font-weight: bold;
    color: #ccc;
  }
  img {
    /* width: 100%; */
  }
</style>
