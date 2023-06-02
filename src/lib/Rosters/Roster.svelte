<script>
  import { gotoManager } from '$lib/utils/helper';
  import DataTable, { Head, Body, Row, Cell } from '@smui/data-table';
  import { Icon } from '@smui/icon-button';
  import RosterRow from "./RosterRow.svelte"

  export let roster, leagueTeamManagers, startersAndReserve, players, rosterPositions, division, expanded;

  let i = 0;

  const digestData = (passedPlayers, rawPlayers, startingPlayers = false, reserve = false) => {
    let digestedRoster = [];

    for (const singlePlayer of rawPlayers) {
      if (!startingPlayers && !reserve && startersAndReserve.includes(singlePlayer)) {
        continue;
      }
      let player = {};
      let slot = "BN"
      if (startingPlayers) {
        slot = rosterPositions[i] == "WRRB_FLEX" ? "WR/RB" : rosterPositions[i];
      }

      if (singlePlayer == "0") {
        player = {
          name: "Empty",
          poss: null,
          team: null,
          avatar: null,
          slot: slot
        }
        i++;
        digestedRoster.push(player);
        continue;
      }

      let injury = null;
      switch (passedPlayers[singlePlayer].is) {
        case "Questionable":
          injury = "Q";
          break;
        case "Out":
          injury = "OUT";
          break;
        case "PUP":
          injury = "PUP";
          break;
        case "IR":
          injury = "IR";
          break;

        default:
          break;
      }
      player = {
        name: `${passedPlayers[singlePlayer].fn} ${passedPlayers[singlePlayer].ln}${injury ? `<span class="injury ${injury}">${injury}</span>` : ""}${roster.metadata && roster.metadata[`p_nick_${singlePlayer}`] ? `<br /><span class="nickname">"${roster.metadata[`p_nick_${singlePlayer}`]}"</span>` : ""}`,
        poss: passedPlayers[singlePlayer].pos,
        team: passedPlayers[singlePlayer].t,
        avatar: passedPlayers[singlePlayer].pos == "DEF" ? `background-image: url(https://sleepercdn.com/images/team_logos/nfl/${singlePlayer.toLowerCase()}.png)` : `background-image: url(https://sleepercdn.com/content/nfl/players/thumb/${singlePlayer}.jpg), url(https://sleepercdn.com/images/v2/icons/player_default.webp)`,
        slot: slot
      }
      i++;
      digestedRoster.push(player);
    }
    i = 0;

    return digestedRoster;
  }

  let finalStarters = digestData(players, roster.starters, true);
  let finalBench = [];
  if (roster.players) {
    finalBench = digestData(players, roster.players);
  }
  let finalIR = null;
  if (roster.reserve) {
    finalIR = digestData(players, roster.reserve, false, true);
  }

  const buildRecord = (newRoster) => {
    const innerRecord = [];
    // Check to make sure that record exists
    if (!newRoster.metadata || !newRoster.metadata.record) return innerRecord;
    // simplify record
    for (const c of newRoster.metadata.record) {
      switch (c) {
        case "W":
          innerRecord.push("green");
          break;
        case "L":
          innerRecord.push("red");
          break;
        case "T":
          innerRecord.push("yellow");
          break;
        default:
          innerRecord.push("grey");
          break;
      }
    }
    return innerRecord;
  }

  const status = roster.players.length === 0 ? "Empty" : `1/${roster.players.length}`;

  let selected = expanded ? "1000px" : "0";

  const toggleSelected = () => {
    selected = selected === "0" ? "1000px" : "0";
  }
</script>

<svelte:window bind:innerWidth={innerWidth} />

<style>
  /* ...existing code... */
</style>

<div class="team">
  <DataTable class="teamInner" table$aria-label="Team Name" style="width: {innerWidth * 0.95 > 380 ? 380 : innerWidth * 0.95}px;">
    <Head>
      <!-- ...existing code... -->
    </Head>
    <Body>
      <!-- ...existing code... -->

      <!-- Replaced code -->
      {#each finalStarters as starter}
        <RosterRow player={starter} />
      {/each}
      <Row class="interactive" on:click={toggleSelected}>
        <Cell colspan=4 class="{division}"><h5><Icon class="material-icons icon">king_bed</Icon> Bench <span class="italic">({status})</span></h5></Cell>
      </Row>
    </Body>
  </DataTable>
  <div class="rosterBench" style="max-height: {selected}">
    <DataTable class="teamInner" style="width: 380px">
      <Body class="bench">
        <!-- Replaced code -->
        {#each finalBench as bench}
          <RosterRow player={bench} />
        {/each}

        <!-- Replaced code -->
        {#if finalIR}
          <Row>
            <Cell colspan=4><h5><Icon class="material-icons icon">healing</Icon> Injured Reserve</h5></Cell>
          </Row>
          {#each finalIR as ir}
            <RosterRow player={ir} />
          {/each}
        {/if}
        <Row class="interactive" on:click={toggleSelected}>
          <Cell colspan=4 class="{division}"><h5><Icon class="material-icons icon">close_fullscreen</Icon>Close Bench</h5></Cell>
        </Row>
      </Body>
    </DataTable>
  </div>
</div>
