<DataTable class="teamInner" table$aria-label="Team Name" style="width: {innerWidth * 0.95 > 380 ? 380 : innerWidth * 0.95}px;">
  <Head> <!-- Team name  -->
    <Row>
      <Cell colspan=4 class="r_{division} clickable">
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
      <Cell class="r_{division}">Year Signed</Cell>
      <Cell class="r_{division}">Contract Length</Cell>
      <Cell class="r_{division}">Salary</Cell>
      <Cell class="r_{division}">Special Designation</Cell>
    </Row>
  </Head>
  <Body>
    <!-- Starters -->
    {#each finalStarters as starter}
      <RosterRow player={starter} />
      {#if starter.name.includes(' ')}
        {#each starter.name.split(' ') as nickname}
          <Cell class="r_{division}">{nickname}</Cell>
        {/each}
      {/if}
    {/each}
    <Row class="interactive" on:click={toggleSelected}>
      <Cell colspan=4 class="{division}"><h5><Icon class="material-icons icon">king_bed</Icon> Bench <span class="italic">({status})</span></h5></Cell>
    </Row>
  </Body>
</DataTable>
<div class="rosterBench" style="max-height: {selected}">
  <DataTable class="teamInner" style="width: 380px">
    <Body class="bench">
      <!-- Bench -->
      {#each finalBench as bench}
        <RosterRow player={bench} />
        {#if bench.name.includes(' ')}
          {#each bench.name.split(' ') as nickname}
            <Cell class="r_{division}">{nickname}</Cell>
          {/each}
        {/if}
      {/each}
      
      <!-- IR -->
      {#if finalIR}
        <Row>
          <Cell colspan=4><h5><Icon class="material-icons icon">healing</Icon> Injured Reserve</h5></Cell>
        </Row>
        {#each finalIR as ir}
          <RosterRow player={ir} />
          {#if ir.name.includes(' ')}
            {#each ir.name.split(' ') as nickname}
              <Cell class="r_{division}">{nickname}</Cell>
            {/each}
          {/if}
        {/each}
      {/if}
      <Row class="interactive" on:click={toggleSelected}>
        <Cell colspan=4 class="{division}"><h5><Icon class="material-icons icon">close_fullscreen</Icon>Close Bench</h5></Cell>
      </Row>
    </Body>
  </DataTable>
</div>
