<script>
  import { gotoManager } from '$lib/utils/helper';
  import DataTable, { Head, Body, Row, Cell } from '@smui/data-table';
  import { Icon } from '@smui/icon-button';
  import RosterRow from "./RosterRow.svelte"

  export let roster, leagueTeamManagers, startersAndReserve, players, rosterPositions, division, expanded;

  // ...existing code...

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

        default:
          innerRecord.push("gray");
          break;
      }
    }
    return innerRecord;
  }

  // ...existing code...
</script>

<svelte:window bind:innerWidth={innerWidth} />

<style>
  /* ...existing styles... */
</style>


<div class="team">
	<DataTable class="teamInner" table$aria-label="Team Name" style="width: {innerWidth * 0.95 > 380 ? 380 : innerWidth * 0.95}px;">
		<Head>
			<!-- Existing row with "Team Name" -->
			<Row>
				<Cell colspan=1 class="r_{division} clickable">
					<h3 on:click={() => gotoManager({leagueTeamManagers, rosterID: roster.roster_id})}>
						<img alt="team avatar" class="teamAvatar" src="{team ? team.avatar : 'https://sleepercdn.com/images/v2/icons/player_default.webp'}" />
						{team?.name ? team.name : 'No Manager'}
					</h3>

					<div class="record">
						{#each record as result}
							<img alt="match result" class="result" src="./{result}.png" />
						{/each}
					</div>
				</Cell>
			</Row>
      
			<!-- New row with additional headers -->
			<Row>
				<Cell colspan=1 class="r_{division}"></Cell>
				<Cell colspan=1 class="r_{division}"><h5>POS</h5></Cell>
				<Cell colspan=1 class="r_{division}"><h5>FACE</h5></Cell>
				<Cell colspan=1 class="r_{division}"><h5>TEAM</h5></Cell>
				<Cell colspan=1 class="r_{division}"><h5>PLAYER</h5></Cell>
				<Cell colspan=1 class="r_{division}"><h5>SIGNED</h5></Cell>
				<Cell colspan=1 class="r_{division}"><h5>Length</h5></Cell>
				<Cell colspan=1 class="r_{division}"><h5>Salary</h5></Cell>
				<Cell colspan=1 class="r_{division}"><h5>Designation</h5></Cell>
			</Row>
      
      <Body>
        <!-- Starters -->
        {#each finalStarters as starter}
          <RosterRow player={starter} />
        {/each}
        <Row class="interactive" on:click={toggleSelected}>
          <Cell colspan=9 class="{division}"><h5><Icon class="material-icons icon">king_bed</Icon> Bench <span class="italic">({status})</span></h5></Cell>
        </Row>
      </Body>
    </DataTable>
    
    <div class="rosterBench" style="max-height: {selected}">
      <DataTable class="teamInner" style="width: 380px">
        <Body class="bench">
          <!-- Bench -->
          {#each finalBench as bench}
            <RosterRow player={bench} />
          {/each}
          
          <!-- IR -->
          {#if finalIR}
            <Row>
              <Cell colspan=9><h5><Icon class="material-icons icon">healing</Icon> Injured Reserve</h5></Cell>
            </Row>
            {#each finalIR as ir}
              <RosterRow player={ir} />
            {/each}
          {/if}
          <Row class="interactive" on:click={toggleSelected}>
            <Cell colspan=9 class="{division}"><h5><Icon class="material-icons icon">close_fullscreen</Icon> Close Bench</h5></Cell>
          </Row>
        </Body>
      </DataTable>
    </div>
  </div>
