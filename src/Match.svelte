<script>
  /**
   * COMPONENT PROPS
   */

  export let player1 = "Player 1";
  export let player2 = "Player 2";
  export let flat = false; // flat or box-shadow (default)?
  export let color = "dodgerblue"; // the app theme color

  /**
   * STATE
   */

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

  /**
   * REACTIVE STATE PREDICATES (RSPs™)
   */

  $: isDeuce = score.p1.pt === 40 && score.p2.pt === 40;

  $: isAd = score.p1.pt === "Ad" || score.p2.pt === "Ad";

  $: isTiebreak = score.p1.sets[i] === 6 && score.p2.sets[i] === 6; // i === setNumber

  $: isMatchOver = score.p1.setsWon > 1 || score.p2.setsWon > 1;

  /**
   * STATE PREDICATES
   *
   * Need to be called at specific times - can't be reactive 🥲
   */

  const isTiebreakOver = () => {
    return (
      (score.p1.tb >= 7 && score.p2.tb + 1 < score.p1.tb) ||
      (score.p2.tb >= 7 && score.p1.tb + 1 < score.p2.tb)
    );
  };

  const isSetOver = () => {
    return (
      (score.p1.sets[setNumber] >= 6 &&
        score.p2.sets[setNumber] + 1 < score.p1.sets[setNumber]) ||
      (score.p2.sets[setNumber] >= 6 &&
        score.p1.sets[setNumber] + 1 < score.p2.sets[setNumber])
    );
  };

  /**
   * FUNCTIONS TO SCORE POINTS
   */

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

  /**
   * FUNCTIONS TO SCORE GAMES, SETS, & TIEBREAKS
   */

  const scoreGame = (winner) => (winner.sets[i] = +winner.sets[i] + 1);

  const scoreSet = (winner) =>
    isSetOver() && (setNumber += 1) && (winner.setsWon += 1);

  const scoreTiebreak = (winner) => {
    winner.tb += 1;
    if (isTiebreakOver()) {
      scoreGame(winner);
      setNumber += 1;
      winner.setsWon += 1;
      resetPoints();
    }
  };

  /**
   * HELPER FUNCTION & ALIASES
   */

  const resetPoints = () => {
    score.p1.pt = 0;
    score.p2.pt = 0;
    score.p1.tb = 0;
    score.p2.tb = 0;
  };

  // Aliases for a score obj prop
  // It makes code using these more clear and shorter
  let setNumber = score.setIndex;
  $: i = setNumber; // double alias

  /**
   * BUTTON CLICK EVENT HANDLER (CONTROLLER)
   */

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
    score = score; // Reactively refresh the score
    scoreSet(winner);
  };

  /**
   * OPTIONALLY PERSIST DATA BY SUBMITTING THE STATE TO AN API OR...
   */
  const handleSubmit = () => alert(JSON.stringify(score));

  $: isHighSetScore = (currentSet, player) => {
    if (currentSet !== setNumber) {
      if (player === "p1") {
        if (score.p1.sets[currentSet] > score.p2.sets[currentSet]) {
          return true;
        }
      } else {
        if (score.p2.sets[currentSet] > score.p1.sets[currentSet]) {
          return true;
        }
      }
    }
  };
</script>

<section class:flat>
  {#if !isTiebreak}
    <form>
      <div style="background:{color};">
        <span>Players</span>
        <span>Set 1</span>
        <span>Set 2</span>
        <span>Set 3</span>
        <span>Pts.</span>
      </div>

      <div>
        <input bind:value={score.p1.name} />
        <input
          class:winner={isHighSetScore(0, "p1")}
          bind:value={score.p1.sets[0]}
        />
        <input
          class:winner={isHighSetScore(1, "p1")}
          bind:value={score.p1.sets[1]}
        />
        <input
          class:winner={isHighSetScore(2, "p1")}
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
          class:winner={isHighSetScore(0, "p2")}
          bind:value={score.p2.sets[0]}
        />
        <input
          class:winner={isHighSetScore(1, "p2")}
          bind:value={score.p2.sets[1]}
        />
        <input
          class:winner={isHighSetScore(2, "p2")}
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

  {#if !isMatchOver}
    <div>
      <button
        style="background:{color}"
        on:click={() => handleBtnClick(score.p1)}>Player 1</button
      >
      <button
        style="background:{color}"
        on:click={() => handleBtnClick(score.p2)}>Player 2</button
      >
    </div>
  {:else}
    <button style="background:{color}" on:click={handleSubmit}
      >Submit Match</button
    >
  {/if}
</section>

<style>
  section {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    width: 680px;
    margin: 2em auto 3em;
    padding: 2em 0;
    background-image: linear-gradient(
      to bottom,
      transparent,
      rgba(0, 0, 0, 0.1)
    );
    border-radius: 1em;
    box-shadow: 0 20px 40px rgba(0, 0, 0, 0.32);
  }
  aside {
    display: flex;
    flex-direction: column;
    justify-content: space-between;
    align-items: center;
  }
  aside p {
    font-size: 1.5em;
  }
  h3 {
    margin-bottom: 1em;
  }
  form {
    margin-top: 1.5em;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  }
  form div {
    display: grid;
    grid-template-columns: 1.5fr 1fr 1fr 1fr 1fr;
    background-color: white;
  }
  form div:nth-child(1) {
    padding: 1em 0;
    /* background-color: dodgerblue; */
    color: white;
  }
  form div:nth-child(2) input {
    border-bottom: 1px solid #ddd;
  }
  form div:nth-child(2) > input:first-child,
  form div:nth-child(3) > input:first-child {
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
    margin: 1.2em 0.5em;
    padding: 1em 2em;
    /* background-color: dodgerblue; */
    color: white;
    font-weight: bold;
    border-radius: 0.25em;
    transition: 150ms;
  }
  button:hover {
    background-image: linear-gradient(
      to bottom,
      transparent,
      rgba(0, 0, 0, 0.1)
    );
  }
  .winner {
    font-weight: bold;
  }
  .flat {
    box-shadow: none;
  }
  @media screen and (max-width: 767px) {
    section {
      width: 360px;
    }
    input {
      width: calc(360px / 6);
    }
    form div:nth-child(2) > input:first-child,
    form div:nth-child(3) > input:first-child {
      width: 90px;
    }
  }
</style>
