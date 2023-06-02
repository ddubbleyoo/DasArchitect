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
      <Row>
        <Cell colspan=4 class="r_{division} clickable">
          <!-- ...existing code... -->

          <Cell class="r_{division}">Year Signed</Cell>
          <Cell class="r_{division}">Contract Length</Cell>
          <Cell class="r_{division}">Salary</Cell>
          <Cell class="r_{division}">Designation</Cell>
        </Row>
      </Head>
      <Body>
        <!-- ...existing code... -->
        {#each finalStarters as starter}
          <Row>
            <Cell>{starter.name}</Cell>
            <Cell>{starter.poss}</Cell>
            <Cell>{starter.team}</Cell>
            <Cell>{starter.slot}</Cell>
            <!-- Add the new columns here -->
            <Cell>{starter.yearSigned}</Cell>
            <Cell>{starter.contractLength}</Cell>
            <Cell>{starter.salary}</Cell>
            <Cell>{starter.designation}</Cell>
            <!-- ...existing code... -->
          </Row>
        {/each}
        <!-- ...existing code... -->
      </Body>
    </DataTable>
    <div class="rosterBench" style="max-height: {selected}px;">
      <DataTable class="teamInner" style="width: 380px;">
        <Body class="bench">
          <!-- ...existing code... -->
        </Body>
      </DataTable>
    </div>
  </DataTable>
</div>

<script>
  // ...existing code...

  // Populate the new columns with delimited data from Player Nickname
  finalStarters = finalStarters.map(starter => {
    const playerNicknameParts = starter.playerNickname.split(' ');
    return {
      ...starter,
      yearSigned: playerNicknameParts[0] || '',
      contractLength: playerNicknameParts[1] || '',
      salary: playerNicknameParts[2] || '',
      designation: playerNicknameParts[3] || ''
    };
  });
</script>
