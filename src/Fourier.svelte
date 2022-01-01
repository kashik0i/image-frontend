<script>
  // import {
  //   showImage,
  //   kernelArray,
  //   kernelName,
  //   inputImageElement,
  //   outputImageElement,
  //   inputFileElement,
  //   handleUploadFunction,
  // } from "./store.js";
  import ImagePreview from "./ImagePreview.svelte";
  import { blobToBase64, b64toBlob } from "./helper";
  import NumPad from "./NumPad.svelte";
  import { Button, Select } from "smelte";

  export let showImage = false;
  let kernelArray = [0, -1, 0, -1, 5, -1, 0, -1, 0];
  let kernelName = "sharpened";
  let inputImageElement;
  let outputImageElement;
  let inputFileElement;
  let handleUploadFunction;
  let isLoading = false;
  let isError = false;
  const items = ["sharpened", "box_blur", "gaussian_blur"];
  const handleUpload = async () => {
    if (!showImage) {
      alert("Please choose an image first!");
      return;
    }
    isLoading = true;
    const formData = new FormData();
    console.log(inputFileElement.files[0]);

    formData.append("input_image", inputFileElement.files[0]);
    formData.append("kernel", JSON.stringify(kernelArray));

    console.log(
      outputImageElement,
      inputFileElement.files[0],
      JSON.stringify(kernelArray)
    );
    isLoading = true;
    (async () => {
      const rawResponse = await fetch("http://localhost:5001/api/convolution", {
        method: "POST",
        // mode: "no-cors",
        headers: {
          Accept: "application/json",
          // contentType: "application/json",
        },
        body: formData,
      });
      const content = await rawResponse.json();
      isLoading = false;
      if (content.error) isError = true;
      const base64Response = await fetch(
        `data:image/png;base64,${content.output_image}`
      );
      const blob = await base64Response.blob();
      console.log(blob.size);
      let src = URL.createObjectURL(blob);
      outputImageElement.src = src;
      console.log(src);
    })();
  };
  handleUploadFunction = handleUpload;
  const onKernelNameChange = (e) => {
    console.log(e.detail, kernelArray);
    switch (e.detail) {
      case "sharpened":
        kernelArray = [0, -1, 0, -1, 5, -1, 0, -1, 0];
        break;
      case "box_blur":
        kernelArray = [...Array(9).fill((1 / 9).toFixed(3))];
        break;
      case "gaussian_blur":
        kernelArray = [1, 2, 1, 2, 4, 2, 1, 2, 1].map((v) => v / 16);
        break;
      default:
        break;
    }
    console.log(e.detail, kernelArray);
  };
</script>

<div class="grid grid-flow-col grid-cols-3">
  <div>
    {showImage}
    <ImagePreview bind:showImage bind:inputImageElement bind:inputFileElement />
  </div>
  <div class="grid grid-flow-row grid-row-3">
    <div class="">
      {kernelName}
      <br />
      {kernelArray}
      {#each kernelArray as k, i}
        <NumPad
          id={i}
          bind:kernelArray
          bind:kernelName
          bind:handleUploadFunction
        />
      {/each}
    </div>
    <Select on:change={onKernelNameChange} {items} bind:value={kernelName} />
    <div>
      <Button on:click={handleUpload}>Process</Button>
    </div>
  </div>
  <!-- {#if !isError || !isLoading} -->
  <div>
    <img bind:this={outputImageElement} alt="Output" />
  </div>
  <!-- {/if} -->
</div>

<style>
</style>
