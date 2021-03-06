<script>
  import Match from "./Match.svelte";

  let color = "#ff6600";
</script>

<main>
  <h1>
    JStennis
    <input type="color" bind:value={color} />
    <small>v5</small>
  </h1>

  <p>
    <em>Made with <a href="https://svelte.dev/" target="_blank">Svelte</a></em>
  </p>

  <Match {color} />

  <p>
    <a href="https://github.com/bmehder/2021-tennis" target="_blank"
      >View the GitHub Repo</a
    >
  </p>
</main>

<style>
  main {
    padding: 2em 0;
  }
  h1 {
    margin-bottom: -0.25em;
    letter-spacing: 0.2em;
    font-weight: 300;
    text-align: center;
  }
  p {
    text-align: center;
  }
  input {
    height: 1.5em;
    width: 1.5em;
    padding: 0.25em;
  }
</style>
