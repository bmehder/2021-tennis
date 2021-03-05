<script>
  const score = {
    p1: {
      sets: [0, 0, 0],
      pt: 0,
      tb: 0,
    },
    p2: {
      sets: [0, 0, 0],
      pt: 0,
      tb: 0,
    },
  };

  // Match State
  let isMatch = true;
  let isDeuce;
  let isAd;
  let isTiebreak;
  let setNum = 0;

  // isDeuce?
  $: score.p1.pt === 40 && score.p2.pt === 40
    ? (isDeuce = true)
    : (isDeuce = false);

  // isAd?
  $: score.p1.pt === "Ad" || score.p2.pt === "Ad"
    ? (isAd = true)
    : (isAd = false);

  // isTiebreak?
  $: score.p1.sets[setNum] === 6 && score.p2.sets[setNum] === 6
    ? (isTiebreak = true)
    : (isTiebreak = false);

  // is the set over?
  $: if (
    (score.p1.sets[setNum] >= 6 &&
      score.p1.sets[setNum] - score.p2.sets[setNum] >= 2) ||
    (score.p2.sets[setNum] >= 6 &&
      score.p2.sets[setNum] - score.p1.sets[setNum] >= 2)
  ) {
    setNum += 1;
  }

  // TODO: make this work for a staight set win
  // TODO: announce the winner
  $: setNum === 3 && (isMatch = false);

  const handleBtnClick = (winner) => {
    if (isTiebreak) {
      scoreTiebreak(winner);
    } else if (isAd) {
      scoreAdPoint(winner);
    } else if (isDeuce) {
      scoreDeucePoint(winner);
    } else {
      scoreNormalPoint(winner);
    }
    score = score;
  };

  const scoreNormalPoint = (winner) => {
    if (winner.pt === 0) {
      winner.pt = 15;
    } else if (winner.pt === 15) {
      winner.pt = 30;
    } else if (winner.pt === 30) {
      winner.pt = 40;
    } else {
      winner.sets[setNum] = +winner.sets[setNum] + 1;
      resetPoints();
    }
  };

  const scoreDeucePoint = (winner) => (winner.pt = "Ad");

  const scoreAdPoint = (winner) => {
    if (winner.pt === "Ad") {
      winner.sets[setNum] = +winner.sets[setNum] + 1;
      resetPoints();
    } else {
      score.p1.pt = 40;
      score.p2.pt = 40;
    }
  };

  const scoreTiebreak = (winner) => {
    winner.tb += 1;
    if (
      (score.p1.tb >= 7 && score.p2.tb + 1 < score.p1.tb) ||
      (score.p2.tb >= 7 && score.p1.tb + 1 < score.p2.tb)
    ) {
      winner.sets[setNum] = +winner.sets[setNum] + 1;
      setNum += 1;
      resetPoints();
    }
  };

  const resetPoints = () => {
    score.p1.pt = 0;
    score.p2.pt = 0;
    score.p1.tb = 0;
    score.p2.tb = 0;
  };
</script>

<section>
  {#if !isTiebreak}
    <form>
      <div>
        <span>Player</span>
        <span>1</span>
        <span>2</span>
        <span>3</span>
        <span>Point</span>
      </div>

      <div>
        <input value="Player 1" />
        <input bind:value={score.p1.sets[0]} />
        <input bind:value={score.p1.sets[1]} />
        <input bind:value={score.p1.sets[2]} />
        <input bind:value={score.p1.pt} />
      </div>

      <div>
        <input value="Player 2" />
        <input bind:value={score.p2.sets[0]} />
        <input bind:value={score.p2.sets[1]} />
        <input bind:value={score.p2.sets[2]} />
        <input bind:value={score.p2.pt} />
      </div>
    </form>
  {:else}
    <aside>
      <h3>Set Tiebreak:</h3>
      <p>{score.p1.tb} : {score.p2.tb}</p>
    </aside>
  {/if}

  {#if isMatch}
    <div>
      <button on:click={() => handleBtnClick(score.p1)}>Player 1</button>
      <button on:click={() => handleBtnClick(score.p2)}>Player 2</button>
    </div>
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
  aside {
    display: flex;
    flex-direction: column;
    justify-content: space-between;
    align-items: center;
  }
  h3 {
    margin-bottom: 1em;
  }
  form {
    border: 1px solid #ddd;
  }
  form div {
    display: grid;
    grid-template-columns: repeat(5, 1fr);
    background-color: white;
  }
  form div:nth-child(1) {
    padding: 1em 0;
    background-color: dodgerblue;
    color: white;
  }
  form div:nth-child(2) input {
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