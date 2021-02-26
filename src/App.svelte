<script>
  const l = (value) => console.log(value);

  const score = {
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
    <button>Player 1</button>
    <button>Player 2</button>
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
  }
  article {
    display: grid;
    grid-template-columns: repeat(5, 1fr);
  }
</style>
