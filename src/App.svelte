<script>
  const l = (value) => console.log(value);
  $: l(isTiebreak);

  $: score = {
    p1: {
      s1: 5,
      s2: 0,
      s3: 0,
      pt: 0,
      tb: 0,
    },
    p2: {
      s1: 5,
      s2: 0,
      s3: 0,
      pt: 0,
      tb: 0,
    },
  };

  let isDeuce;
  let isTiebreak;

  $: score.p1.pt === 40 && score.p2.pt === 40
    ? (isDeuce = true)
    : (isDeuce = false);

  $: score.p1.s1 === 6 && score.p2.s1 === 6
    ? (isTiebreak = true)
    : (isTiebreak = false);

  const scoreGame = (player) => {
    if (!isTiebreak) {
      if (score.p1.pt === "Ad" || score.p2.pt === "Ad") {
        if (player.pt === "Ad") {
          player.s1 = +player.s1 + 1;
          resetPoints();
          score = score;
        } else {
          score.p1.pt = 40;
          score.p2.pt = 40;
        }
        return;
      }

      if (!isDeuce) {
        if (score.p1.pt <= 40 && score.p2.pt <= 40) {
          if (player.pt === 0) {
            player.pt = 15;
          } else if (player.pt === 15) {
            player.pt = 30;
          } else if (player.pt === 30) {
            player.pt = 40;
          } else {
            player.s1 = +player.s1 + 1;
            resetPoints();
          }
        }
      } else {
        // it's deuce
        player.pt = "Ad";
        isDeuce = false;
      }
      score = score;
    } else {
      scoreTiebreak(player);
    }
  };

  const resetPoints = () => {
    score.p1.pt = 0;
    score.p2.pt = 0;
  };

  const scoreTiebreak = (player) => {
    if (isTiebreak) {
      player.tb += 1;
      if (score.p1.tb >= 7 && score.p2.tb < score.p1.tb) {
        player.s1 += 1;
        isTiebreak = false;
      } else if (score.p1.tb > 6 || score.p2.tb > 6) {
        player.s1 += 1;
        isTiebreak = false;
      }
      score = score;
    }
  };
</script>

<section>
  <article>
    <span />
    <span>1</span>
    <span>2</span>
    <span>3</span>
    <span>Point</span>
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

  {#if score.p1.tb > 0 || score.p2.tb > 0}
    <aside>{score.p1.tb} : {score.p2.tb}</aside>
  {/if}
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
  input {
    width: 80px;
    margin: 0;
    text-align: center;
  }
  span {
    padding-bottom: 0.25em;
    text-align: center;
    font-weight: bold;
  }
</style>
