<script>
  const l = (value) => console.log(value);
  $: l(isTiebreak);

  $: score = {
    p1: {
      name: "Roger",
      sets: [5, 0, 0],
      pt: 0,
      tb: 0,
    },
    p2: {
      name: "Rafa",
      sets: [5, 0, 0],
      pt: 0,
      tb: 0,
    },
  };

  let isDeuce;
  let isTiebreak;
  let setNum = 0;

  $: score.p1.pt === 40 && score.p2.pt === 40
    ? (isDeuce = true)
    : (isDeuce = false);

  $: score.p1.sets[setNum] === 6 && score.p2.sets[setNum] === 6
    ? (isTiebreak = true)
    : (isTiebreak = false);

  const handleGame = (player) => {
    if (isTiebreak) {
      scoreTiebreak(player);
    } else if (score.p1.pt === "Ad" || score.p2.pt === "Ad") {
      scoreAdPoint(player);
      return;
    } else if (!isDeuce) {
      score.p1.pt <= 40 && score.p2.pt <= 40 && scoreNormalPoint(player);
    } else {
      // it's deuce
      player.pt = "Ad";
      isDeuce = false;
    }
    score = score;
  };

  const scoreNormalPoint = (player) => {
    if (player.pt === 0) {
      player.pt = 15;
    } else if (player.pt === 15) {
      player.pt = 30;
    } else if (player.pt === 30) {
      player.pt = 40;
    } else {
      player.sets[setNum] = +player.sets[setNum] + 1;
      resetPoints();
    }
  };

  const scoreAdPoint = (player) => {
    if (player.pt === "Ad") {
      player.sets[setNum] = +player.sets[setNum] + 1;
      resetPoints();
      score = score;
    } else {
      score.p1.pt = 40;
      score.p2.pt = 40;
    }
  };

  const resetPoints = () => {
    score.p1.pt = 0;
    score.p2.pt = 0;
  };

  const scoreTiebreak = (player) => {
    player.tb += 1;
    if (
      (score.p1.tb >= 7 && score.p2.tb + 1 < score.p1.tb) ||
      (score.p2.tb >= 7 && score.p1.tb + 1 < score.p2.tb)
    ) {
      console.log("Player 1 wins!");
      player.sets[setNum] = +player.sets[setNum] + 1;
      setNum += 1;
      isTiebreak = false;
      resetPoints();
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

    <input bind:value={score.p1.name} />
    <input bind:value={score.p1.sets[0]} />
    <input bind:value={score.p1.sets[1]} />
    <input bind:value={score.p1.sets[2]} />
    <input bind:value={score.p1.pt} />

    <input bind:value={score.p2.name} />
    <input bind:value={score.p2.sets[0]} />
    <input bind:value={score.p2.sets[1]} />
    <input bind:value={score.p1.sets[2]} />
    <input bind:value={score.p2.pt} />
  </article>

  <div>
    <button on:click={() => handleGame(score.p1)}>Player 1</button>
    <button on:click={() => handleGame(score.p2)}>Player 2</button>
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
