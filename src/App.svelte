<script>
  import Match from "./Match.svelte";
</script>

<main>
  <h1>JStennis <small>v5</small></h1>
  <p>
    <em>Made with <a href="https://svelte.dev/" target="_blank">Svelte</a></em>
  </p>

  <Match player1="Brad" player2="Novak" />

  <p>
    <small
      ><a href="https://github.com/bmehder/2021-tennis" target="_blank"
        >View the GitHub Repo</a
      ></small
    >
  </p>
</main>

<style>
  main {
    padding: 2em;
  }
  h1 {
    margin-bottom: 0.2em;
    letter-spacing: 0.2em;
    font-weight: 300;
    text-align: center;
  }
  p {
    text-align: center;
  }
</style>
