<script>
  import tournamentPlayers from './lib/players.tournaments';
  import rankedPlayers from './lib/players.ranked';
  import tournamentScores from './lib/scores.tournaments';
  import rankedScores from './lib/scores.ranked';

  let currentTournamentValueStr = localStorage.getItem('values:tournament') || '';
  let currentRankedValueStr = localStorage.getItem('values:ranked') || '';
  let currentTournamentValues = currentTournamentValueStr ? currentTournamentValueStr.split(/\r?\n/) : tournamentPlayers.map(() => '');
  let currentRankedValues = currentRankedValueStr ? currentRankedValueStr.split(/\r?\n/) : rankedPlayers.map(() => '');

  let currentPlayer = '';

  const samplePlayer = () => {
    // Take a player who hasn't been rated yet
    const unratedTournamentIndexes = currentTournamentValues.reduce((arr, line, index) => {
      if (!line.trim()) arr.push(index);
      return arr;
    }, []);
    const unratedRankedIndexes = currentRankedValues.reduce((arr, line, index) => {
      if (!line.trim()) arr.push(index);
      return arr;
    }, []);
    const randomIndex = Math.floor(Math.random() * (unratedTournamentIndexes.length + unratedRankedIndexes.length));
    const isTournament = randomIndex < unratedTournamentIndexes.length;
    if (isTournament) {
      const tournamentIndex = unratedTournamentIndexes[randomIndex];
      currentPlayer = tournamentPlayers[tournamentIndex];
    } else {
      const rankedIndex = unratedRankedIndexes[randomIndex - unratedTournamentIndexes.length];
      currentPlayer = rankedPlayers[rankedIndex];
    }
  }

  const vote = sentence => {
    if (tournamentScores[currentPlayer]) {
      const tournamentIndex = tournamentPlayers.indexOf(currentPlayer);
      currentTournamentValues[tournamentIndex] = sentence;
      currentTournamentValueStr = currentTournamentValues.map(x => x || '').join('\n');
      localStorage.setItem('values:tournament', currentTournamentValueStr);
    }
    if (rankedScores[currentPlayer]) {
      const rankedIndex = rankedPlayers.indexOf(currentPlayer);
      currentRankedValues[rankedIndex] = sentence;
      currentRankedValueStr = currentRankedValues.map(x => x || '').join('\n');
      localStorage.setItem('values:ranked', currentRankedValueStr);
    }
    samplePlayer();
  }

  const reload = () => {
    localStorage.setItem('values:tournament', currentTournamentValueStr);
    localStorage.setItem('values:ranked', currentRankedValueStr);
    samplePlayer();
  }

  $: {
    samplePlayer();
  }
</script>

<main>
  <h1><a href={`https://osu.ppy.sh/users/${currentPlayer}`}>{currentPlayer}</a></h1>
  <div class="actions">
    <button on:click={() => vote('Not Skillbanned')}>Not Skillbanned</button>
    <button on:click={() => vote('Unsure')}>Unsure</button>
    <button on:click={() => vote('Skillbanned')}>Skillbanned</button>
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
</main>

<div class="values">
  <div>
    Copy from below (click below, Ctrl+A, Ctrl+C) and paste in the <code>player_list_tournaments</code> sheet in your column in Line 2 to save your work (Ctrl+V).<br />
    <textarea bind:value={currentTournamentValueStr}></textarea>
  </div>
  <button on:click={reload}>Reload</button>
  <div>
    Copy from below (click below, Ctrl+A, Ctrl+C) and paste in the <code>player_list_ranked</code> sheet in your column in Line 2 to save your work (Ctrl+V).<br />
    <textarea bind:value={currentRankedValueStr}></textarea>
  </div>
</div>

<style>
  main {
    position: fixed;
    left: 0;
    right: 300px;
    top: 0;
    bottom: 0;
    overflow: auto;
  }

  .values {
    position: fixed;
    right: 0;
    top: 0;
    bottom: 0;
    width: 300px;
    border-left: 1px solid #eee;
    display: flex;
    flex-direction: column;
    justify-content: space-between;
    overflow-y: scroll;
  }
  .values textarea {
    width: 90%;
    height: 200px;
  }

  .scores, .actions {
    margin: auto;
  }

  button {
    margin: 1em 4em;
  }

  td {
    padding: 0 1em;
    border-bottom: 1px solid #444;
  }
</style>
