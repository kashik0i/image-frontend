<script>
  export let name;
  import Conv from "./Conv.svelte";
  import "smelte/src/tailwind.css";
  import Fourier from "./Fourier.svelte";
</script>

<link
  href="https://fonts.googleapis.com/icon?family=Material+Icons"
  rel="stylesheet"
/>

<main>
  Hello
  <div class="grid grid-flow-row grid-row-3">
    <Conv name="convolution-page" />
    <div>separator</div>
    <!-- <Fourier name="fourier-page" /> -->
  </div>
</main>

<style>
  main {
    text-align: center;
    padding: 1em;
    max-width: 240px;
    margin: 0 auto;
  }

  h1 {
    color: #ff3e00;
    text-transform: uppercase;
    font-size: 4em;
    font-weight: 100;
  }

  @media (min-width: 640px) {
    main {
      max-width: none;
    }
  }
</style>
