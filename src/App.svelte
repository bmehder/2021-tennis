<script>
  const l = (value) => console.log(value);

  $: score = {
    p1: {
      s1: 0,
      s2: 0,
      s3: 0,
      pt: 0,
    },
    p2: {
      s1: 0,
      s2: 0,
      s3: 0,
      pt: 0,
    },
  };

  const scoreGame = (player) => {
    console.log(player);
    if (score.p1.pt <= 40 && score.p2.pt <= 40) {
      if (player.pt === 0) {
        player.pt = 15;
      } else if (player.pt === 15) {
        player.pt = 30;
      } else {
        player.pt = 40;
      }
    }
    score = score;
  };
</script>

<section>
  <article>
    <input value="Player 1" />
    <input bind:value={score.p1.s1} />
    <input bind:value={score.p1.s2} />
    <input bind:value={score.p1.s3} />
    <input bind:value={score.p1.pt} />
    <input value="Player 2" />
    <input bind:value={score.p2.s1} />
    <input bind:value={score.p2.s2} />
    <input bind:value={score.p2.s3} />
    <input bind:value={score.p2.pt} />
  </article>
  <div>
    <button on:click={() => scoreGame(score.p1)}>Player 1</button>
    <button on:click={() => scoreGame(score.p2)}>Player 2</button>
  </div>
</section>

<style>
  section {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    min-width: 600px;
    margin: 2em auto;
  }
  section div {
    display: flex;
    margin: 2em auto;
    gap: 1em;
  }
  article {
    display: grid;
    grid-template-columns: repeat(5, 1fr);
  }
</style>
