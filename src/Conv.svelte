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
  import { blobToBase64, URLToBlob, b64toBlob } from "./helper";
  import NumPad from "./NumPad.svelte";
  import { Button, Select, TextField } from "smelte";

  export let showImage = false;
  let kernelArray = [0, -1, 0, -1, 5, -1, 0, -1, 0];
  let kernelName = "box_blur";
  let inputImageElement;
  let outputImageElement;
  let inputFileElement;
  let outputFileElemet;
  let inputFile;
  let handleUploadFunction;
  let isLoading = false;
  let isError = false;
  const items = ["box_blur", "gaussian_blur", "median", "sobel", "robert"];
  const processList = [
    "convolution",
    "noise",
    "interpolation",
    "fourier",
    "pipeline",
  ];
  const noiseList = ["salt_and_pepper", "gaussian"];
  let noiseName = "salt_and_pepper";
  let interpolationSize = { width: 400, height: 400 };

  let processName = "convolution";
  const onNoiseNameChange = (e) => {
    console.log(e.detail);
  };
  const handleInterpolationChange = (e) => {
    console.log(e.target.clientWidth, e.target.clientHeight);
    //outputImageElement.style.border = "2px solid green";
  };
  const handleUseOutput = async (e) => {
    if (!showImage) {
      alert("Output is empty!");
      return;
    }

    inputImageElement.src = outputImageElement.src;
    const blob = await URLToBlob(inputImageElement.src);
    inputFile = blob;
    console.log("output is now input", inputFile);
    // inputFileElement.name=
  };
  const handleUpload = async () => {
    if (!showImage) {
      alert("Please choose an image first!");
      return;
    }
    isError = false;
    isLoading = true;

    if (processName == "convolution") {
      const formData = new FormData();
      console.log(inputFile);

      formData.append("input_image", inputFile);
      formData.append("kernel", JSON.stringify(kernelArray));
      formData.append("kernel_name", kernelName);
      console.log(outputImageElement, JSON.stringify(kernelArray));
      (async () => {
        const rawResponse = await fetch(
          "https://159.223.126.72/api/convolution",
          {
            method: "POST",
            // mode: "no-cors",
            headers: {
              Accept: "application/json",
              // contentType: "application/json",
            },
            body: formData,
          }
        );
        const content = await rawResponse.json();

        if (content.error) {
          isError = true;
          isLoading = false;
          return;
        }
        const base64Response = await fetch(
          `data:image/png;base64,${content.output_image}`
        );
        const blob = await base64Response.blob();
        console.log(blob.size);
        let src = URL.createObjectURL(blob);
        outputImageElement.src = src;
        console.log(src);
        isLoading = false;
      })();
    } else if (processName == "fourier") {
      const formData = new FormData();
      console.log(inputFileElement.files[0]);

      formData.append("input_image", inputFile);
      console.log(outputImageElement, inputFile);
      (async () => {
        const rawResponse = await fetch("https://159.223.126.72/api/fourier", {
          method: "POST",
          // mode: "no-cors",
          headers: {
            Accept: "application/json",
            // contentType: "application/json",
          },
          body: formData,
        });
        const content = await rawResponse.json();

        if (content.error) {
          isError = true;
          isLoading = false;
          return;
        }
        const base64Response = await fetch(
          `data:image/png;base64,${content.output_image}`
        );
        const blob = await base64Response.blob();
        console.log(blob.size);
        let src = URL.createObjectURL(blob);
        outputImageElement.src = src;
        console.log(src);
        isLoading = false;
      })();
    } else if (processName == "noise") {
      const formData = new FormData();
      console.log("NOISE");

      formData.append("input_image", inputFile);
      formData.append("noise", noiseName);
      console.log(outputImageElement);
      formData.forEach((value, key) => {
        console.log("key", key);
        console.log("value", value);
      });
      (async () => {
        const rawResponse = await fetch("https://159.223.126.72/api/noise", {
          method: "POST",
          // mode: "no-cors",
          headers: {
            Accept: "application/json",
            // contentType: "application/json",
          },
          body: formData,
        });
        const content = await rawResponse.json();

        if (content.error) {
          isError = true;
          isLoading = false;
          return;
        }

        const base64Response = await fetch(
          `data:image/png;base64,${content.output_image}`
        );
        const blob = await base64Response.blob();
        console.log(blob.size);
        let src = URL.createObjectURL(blob);
        outputImageElement.src = src;
        console.log(src);
        isLoading = false;
      })();
    } else if ((processName = "interpolation")) {
      const formData = new FormData();
      console.log("NOISE");

      formData.append("input_image", inputFile);
      formData.append("width", interpolationSize.width);
      formData.append("height", interpolationSize.height);
      console.log(outputImageElement);
      formData.forEach((value, key) => {
        console.log("key", key);
        console.log("value", value);
      });
      (async () => {
        const rawResponse = await fetch(
          "https://159.223.126.72/api/interpolation",
          {
            method: "POST",
            // mode: "no-cors",
            headers: {
              Accept: "application/json",
              // contentType: "application/json",
            },
            body: formData,
          }
        );
        const content = await rawResponse.json();

        if (content.error) {
          isError = true;
          isLoading = false;
          return;
        }

        const base64Response = await fetch(
          `data:image/png;base64,${content.output_image}`
        );
        const blob = await base64Response.blob();
        console.log(blob.size);
        let src = URL.createObjectURL(blob);
        outputImageElement.src = src;
        // outputImageElement.clientWidth = interpolationSize.width;
        // outputImageElement.clientHeight = interpolationSize.height;
        console.log(outputImageElement);
        isLoading = false;
      })();
    }
  };
  handleUploadFunction = handleUpload;
  const onProcessNameChange = (e) => {
    console.log(e.detail, processName);
    switch (e.detail) {
      case "convolution":
        break;
      case "noise":
        break;
      case "interpolation":
        break;
      case "fourier":
        break;
      default:
        console.error("bad process name");
    }
  };
  const onKernelNameChange = (e) => {
    console.log(e.detail, kernelArray);
    switch (e.detail) {
      // case "sharpened":
      //   kernelArray = [0, -1, 0, -1, 5, -1, 0, -1, 0];
      //   break;
      case "box_blur":
        kernelArray = [...Array(9).fill((1 / 9).toFixed(3))];
        break;
      // case "gaussian_blur":
      //   const sum = [1, 2, 1, 2, 4, 2, 1, 2, 1].reduce(
      //     (partial_sum, a) => partial_sum + a,
      //     0
      //   );
      //   kernelArray = [1, 2, 1, 2, 4, 2, 1, 2, 1].map((v) => v / sum);
      //   break;
      default:
        break;
    }
    console.log(e.detail, kernelArray);
  };
</script>

<div class="grid grid-flow-col grid-cols-3">
  <div>
    {showImage}
    <ImagePreview
      bind:showImage
      bind:inputImageElement
      bind:inputFileElement
      bind:interpolationSize
      bind:inputFile
    />
  </div>
  <div class="grid grid-flow-row grid-row-3">
    {#if processName == "convolution"}
      <div class="">
        {kernelName}
        {processName}
        <br />
        {kernelArray}
        {#if kernelName == "box_blur"}
          {#each kernelArray as k, i}
            <NumPad
              id={i}
              bind:kernelArray
              bind:kernelName
              bind:handleUploadFunction
            />
          {/each}
        {/if}
      </div>
      <Select on:change={onKernelNameChange} {items} bind:value={kernelName} />
    {:else if processName == "fourier"}
      <!-- else content here -->
    {:else if processName == "noise"}
      <Select
        items={noiseList}
        on:change={onNoiseNameChange}
        bind:value={noiseName}
      />
      <!-- else content here -->
    {:else if processName == "interpolation"}
      <TextField
        on:change={(e) => {
          handleInterpolationChange(e);
        }}
        label="Width"
        type="number"
        bind:value={interpolationSize.width}
        class="w-full h-full text-xl text-center"
      />

      <TextField
        bind:value={interpolationSize.height}
        on:change={(e) => {
          handleInterpolationChange(e);
        }}
        label="Height"
        type="number"
        class="w-full h-full text-xl text-center"
      />
    {/if}

    <Select
      on:change={onProcessNameChange}
      items={processList}
      bind:value={processName}
    />
    <div>
      <Button on:click={handleUpload}>Process</Button>

      <Button on:click={handleUseOutput}>Use Output</Button>
    </div>
  </div>
  <!-- {#if !isError || !isLoading} -->
  <div>
    {isLoading
      ? "Loading"
      : isError
      ? "Error"
      : outputImageElement?.src != ""
      ? "Output"
      : "Not Set"}
    <img
      class="object-cover max-h-full"
      bind:this={outputImageElement}
      alt="Output"
    />
  </div>
  <!-- {/if} -->
</div>

<style>
</style>
