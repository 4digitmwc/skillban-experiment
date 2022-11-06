<script>
  import tournamentScores from './lib/scores.tournaments';
  import rankedScores from './lib/scores.ranked';

  let currentPlayer = '';

  const updatePlayer = $event => {
    currentPlayer = $event.target.value;
  }
</script>

<main>
  <div class="player">
    Player username<br />
    <input class="player-input" type="text" on:change={updatePlayer}>
  </div>
  {#if tournamentScores[currentPlayer]}
    <h4>Tournament scores</h4>
    <table class="scores">
      <thead>
        <th>Map</th>
        <th>Score</th>
        <th>Source</th>
      </thead>
      <tbody>
        {#each tournamentScores[currentPlayer] as score}
          <tr>
            <td class="map"><a href={score.link}>{score.map}</a></td>
            <td class="score">{score.score}</td>
            <td class="source">{score.source} {score.category}</td>
          </tr>
        {/each}
      </tbody>
    </table>
  {/if}
  {#if rankedScores[currentPlayer]}
    <h4>Ranked scores</h4>
    <table class="scores">
      <thead>
        <th>Map</th>
        <th>Score</th>
        <th>Category</th>
      </thead>
      <tbody>
        {#each rankedScores[currentPlayer] as score}
          <tr>
            <td class="map"><a href={score.link}>{score.map}</a></td>
            <td class="score">{score.score}</td>
            <td class="category">{score.category}</td>
          </tr>
        {/each}
      </tbody>
    </table>
  {/if}
  {#if !tournamentScores[currentPlayer] && !rankedScores[currentPlayer]}
    <p>Player not found!</p>
  {/if}
</main>

<style>
  main {
    position: fixed;
    left: 0;
    right: 0;
    top: 0;
    bottom: 0;
    overflow: auto;
  }

  .scores {
    margin: auto;
  }

  td {
    padding: 0 1em;
    border-bottom: 1px solid #444;
  }

  .player {
    margin-top: 2em;
  }

  .player-input {
    font-size: 2em;
  }
</style>
