<script>
  export let player1 = "Player 1";
  export let player2 = "Player 2";

  const score = {
    p1: {
      name: player1,
      sets: [0, 0, 0],
      pt: 0,
      tb: 0,
      setsWon: 0,
    },
    p2: {
      name: player2,
      sets: [0, 0, 0],
      pt: 0,
      tb: 0,
      setsWon: 0,
    },
    setIndex: 0,
  };

  let setIndex = score.setIndex;

  $: isDeuce = score.p1.pt === 40 && score.p2.pt === 40;

  $: isAd = score.p1.pt === "Ad" || score.p2.pt === "Ad";

  $: isTiebreak =
    score.p1.sets[setIndex] === 6 && score.p2.sets[setIndex] === 6;

  $: isMatch = score.p1.setsWon > 1 || score.p2.setsWon > 1 ? false : true;

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
    scoreSet(winner);
  };

  const scoreNormalPoint = (winner) => {
    if (winner.pt === 0) {
      winner.pt = 15;
    } else if (winner.pt === 15) {
      winner.pt = 30;
    } else if (winner.pt === 30) {
      winner.pt = 40;
    } else {
      scoreGame(winner);
      resetPoints();
    }
  };

  const scoreDeucePoint = (winner) => (winner.pt = "Ad");

  const scoreAdPoint = (winner) => {
    if (winner.pt === "Ad") {
      scoreGame(winner);
      resetPoints();
    } else {
      score.p1.pt = 40;
      score.p2.pt = 40;
    }
  };

  const scoreGame = (winner) =>
    (winner.sets[setIndex] = winner.sets[setIndex] + 1);

  const scoreTiebreak = (winner) => {
    winner.tb += 1;
    if (
      (score.p1.tb >= 7 && score.p2.tb + 1 < score.p1.tb) ||
      (score.p2.tb >= 7 && score.p1.tb + 1 < score.p2.tb)
    ) {
      scoreGame(winner);
      setIndex += 1;
      winner.setsWon += 1;
      resetPoints();
    }
  };

  const scoreSet = (winner) => {
    if (
      (score.p1.sets[setIndex] >= 6 &&
        score.p1.sets[setIndex] - score.p2.sets[setIndex] >= 2) ||
      (score.p2.sets[setIndex] >= 6 &&
        score.p2.sets[setIndex] - score.p1.sets[setIndex] >= 2)
    ) {
      setIndex += 1;
      winner.setsWon += 1;
    }
  };

  const resetPoints = () => {
    score.p1.pt = 0;
    score.p2.pt = 0;
    score.p1.tb = 0;
    score.p2.tb = 0;
  };

  const handleSubmit = () => alert(JSON.stringify(score, null, "  "));
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
        <input bind:value={score.p1.name} />
        <input
          class:winner={setIndex > 0 && score.p1.sets[0] > score.p2.sets[0]}
          bind:value={score.p1.sets[0]}
        />
        <input
          class:winner={setIndex > 1 && score.p1.sets[1] > score.p2.sets[1]}
          bind:value={score.p1.sets[1]}
        />
        <input
          class:winner={setIndex > 2 && score.p1.sets[2] > score.p2.sets[2]}
          bind:value={score.p1.sets[2]}
        />
        <input
          class:winner={score.p1.pt !== 0 &&
            (score.p1.pt >= score.p2.pt || score.p1.pt === "Ad")}
          bind:value={score.p1.pt}
          readonly
        />
      </div>

      <div>
        <input bind:value={score.p2.name} />
        <input
          class:winner={setIndex > 0 && score.p2.sets[0] > score.p1.sets[0]}
          bind:value={score.p2.sets[0]}
        />
        <input
          class:winner={setIndex > 1 && score.p2.sets[1] > score.p1.sets[1]}
          bind:value={score.p2.sets[1]}
        />
        <input
          class:winner={setIndex > 2 && score.p2.sets[2] > score.p1.sets[2]}
          bind:value={score.p2.sets[2]}
        />
        <input
          class:winner={score.p2.pt !== 0 &&
            (score.p2.pt >= score.p1.pt || score.p2.pt === "Ad")}
          bind:value={score.p2.pt}
          readonly
        />
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
  {:else}
    <button on:click={handleSubmit}>Submit Match</button>
  {/if}
</section>

<style>
  section {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    width: 680px;
    margin: 2em auto;
    padding: 2em 0;
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
    /* grid-template-columns: repeat(5, 1fr); */
    grid-template-columns: 1.5fr 1fr 1fr 1fr 1fr;
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
  form div:nth-child(2) > input:first-child {
    width: 150px;
  }
  input {
    width: calc(600px / 6);
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
  .winner {
    font-weight: bold;
  }
  @media screen and (max-width: 828px) {
    section {
      width: 360px;
    }
    input {
      width: calc(360px / 6);
    }
    form div:nth-child(2) > input:first-child {
      width: 90px;
    }
  }
</style>
