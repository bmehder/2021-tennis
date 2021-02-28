<script>
  let isMatch = true;
  let isDeuce;
  let isTiebreak;
  let setNum = 0;

  $: score = {
    p1: {
      name: "Roger",
      sets: [0, 0, 0],
      pt: 0,
      tb: 0,
    },
    p2: {
      name: "Rafa",
      sets: [0, 0, 0],
      pt: 0,
      tb: 0,
    },
  };

  // isDeuce
  $: score.p1.pt === 40 && score.p2.pt === 40
    ? (isDeuce = true)
    : (isDeuce = false);

  // isTiebreak
  $: score.p1.sets[setNum] === 6 && score.p2.sets[setNum] === 6
    ? (isTiebreak = true)
    : (isTiebreak = false);

  // is the set over? If so, increment the setNum var
  // and disable the buttons when the match has ended
  $: if (
    (score.p1.sets[setNum] >= 6 &&
      score.p1.sets[setNum] - score.p2.sets[setNum] >= 2) ||
    (score.p2.sets[setNum] >= 6 &&
      score.p2.sets[setNum] - score.p1.sets[setNum] >= 2)
  ) {
    setNum += 1;
    setNum === 3 && (isMatch = false);
  }

  const handleBtnClick = (player) => {
    if (isTiebreak) {
      scoreTiebreak(player);
    } else if (score.p1.pt === "Ad" || score.p2.pt === "Ad") {
      scoreAdPoint(player);
      return;
    } else if (isDeuce) {
      player.pt = "Ad";
    } else {
      scoreNormalPoint(player);
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
    } else {
      score.p1.pt = 40;
      score.p2.pt = 40;
    }
  };

  const resetPoints = () => {
    score.p1.pt = 0;
    score.p2.pt = 0;
    score.p1.tb = 0;
    score.p2.tb = 0;
  };

  const scoreTiebreak = (player) => {
    player.tb += 1;
    if (
      (score.p1.tb >= 7 && score.p2.tb + 1 < score.p1.tb) ||
      (score.p2.tb >= 7 && score.p1.tb + 1 < score.p2.tb)
    ) {
      player.sets[setNum] = +player.sets[setNum] + 1;
      setNum += 1;
      resetPoints();
    }
  };
</script>

<section>
  <article>
    <div>
      <span>Player</span>
      <span>1</span>
      <span>2</span>
      <span>3</span>
      <span>Point</span>
    </div>

    <div>
      <input bind:value={score.p1.name} />
      <input bind:value={score.p1.sets[0]} />
      <input bind:value={score.p1.sets[1]} />
      <input bind:value={score.p1.sets[2]} />
      <input bind:value={score.p1.pt} />
    </div>

    <div>
      <input bind:value={score.p2.name} />
      <input bind:value={score.p2.sets[0]} />
      <input bind:value={score.p2.sets[1]} />
      <input bind:value={score.p2.sets[2]} />
      <input bind:value={score.p2.pt} />
    </div>
  </article>

  {#if isMatch}
    <div>
      <button on:click={() => handleBtnClick(score.p1)}>Player 1</button>
      <button on:click={() => handleBtnClick(score.p2)}>Player 2</button>
    </div>
  {/if}

  {#if isTiebreak}
    <aside>{score.p1.tb} : {score.p2.tb}</aside>
  {/if}
</section>

<style>
  section {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    width: 480px;
    margin: 2em auto;
    padding: 2em;
    background-image: linear-gradient(
      to bottom,
      transparent,
      rgba(0, 0, 0, 0.1)
    );
    border-radius: 1em;
    box-shadow: 0 20px 40px rgba(0, 0, 0, 0.5);
  }
  article {
    border: 1px solid #ddd;
  }
  article div {
    display: grid;
    grid-template-columns: repeat(5, 1fr);
    background-color: white;
  }
  article div:nth-child(1) {
    padding: 1em 0;
    background-color: dodgerblue;
    color: white;
  }
  article div:nth-child(2) input {
    border-bottom: 1px solid #ddd;
  }
  input {
    width: 80px;
    margin: 0;
    padding: 1em 0;
    text-align: center;
    border: none;
    border-radius: 0;
    border-left: 1px solid #ddd;
  }
  div input:first-child {
    text-align: left;
    padding-left: 1em;
    border-left: none;
  }
  span {
    text-align: center;
    font-weight: bold;
  }
  button {
    margin: 1em 0.5em;
    padding: 1em 2em;
    background-color: dodgerblue;
    color: white;
  }
</style>
