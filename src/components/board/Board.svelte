<script lang="ts">
  import { getRowData, words } from "../../utils";

  import Row from "./Row.svelte";
  import ContextMenu from "../widgets/ContextMenu.svelte";

  export let value: string[];
  export let board: GameBoard;
  export let guesses: number;
  export let icon: string;
  export let cake: boolean;
  export function shake(row: number) {
    rows[row].shake();
  }
  export function bounce(row: number) {
    rows[row].bounce();
  }
  export function hideCtx(e?: MouseEvent) {
    if (!e || !e.defaultPrevented) showCtx = false;
  }
  let rows: Row[] = [];
  let showCtx = false;
  let pAns = 0;
  let pSols = 0;
  let x = 0;
  let y = 0;
  let word = "";

  function context(cx: number, cy: number, num: number, val: string) {
    if (guesses >= num) {
      x = cx;
      y = cy;
      showCtx = true;
      word = guesses > num ? val : "";

      const match = getRowData(num, board);
      pAns = words.words.filter((w) => match(w)).length;
      pSols = pAns + words.valid.filter((w) => match(w)).length;
    }
  }
</script>

{#if showCtx}
  <ContextMenu {pAns} {pSols} {x} {y} {word} />
{/if}

<div class="board">
  {#each value as _, i}
    <Row
      num={i}
      {guesses}
      bind:this={rows[i]}
      bind:value={value[i]}
      state={board.state[i]}
      on:ctx={(e) => context(e.detail.x, e.detail.y, i, value[i])}
    />
  {/each}
  {#if icon}
    <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 200 200" fill="none">
      <path d={icon} stroke-width="14" />
    </svg>
  {/if}
  {#if cake}
    <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 64 64" fill="none">
      <path
        d="M6 59.998V29.996a2 2 0 0 1 2-2h48a2 2 0 0 1 2 2v30.002"
        stroke-width="2"
      />
      <path stroke-width="2" d="M2 59.995h59.998" />
      <path
        d="M5.998 34.998a7.001 7.001 0 0 0 7 7c3.867 0 7-2.133 7-6m.002 0c0 3.867 3.135 6 7 6 3.867 0 7-2.133 7-6m0 0c0 3.867 3.135 6 7 6 3.867 0 7-2.133 7-6m0 0c0 3.867 3.135 6 7 6a6.974 6.974 0 0 0 3.002-.676M31.998 17.998v9.998m-16.002-9.998v9.998M48 17.998v9.998"
        stroke-width="2"
      />
      <path
        d="M18.996 8.998a7.341 7.341 0 0 0-3-5.998s-3 2.679-3 5.998a2.993 2.993 0 0 0 5.86.867 2.594 2.594 0 0 0 .14-.867zm16.002 0a7.341 7.341 0 0 0-3-5.998s-3 2.679-3 5.998a2.993 2.993 0 0 0 5.86.867 2.597 2.597 0 0 0 .14-.867zm16.002 0A7.341 7.341 0 0 0 48 3s-3 2.679-3 5.998a2.993 2.993 0 0 0 5.86.867 2.594 2.594 0 0 0 .14-.867z"
        stroke-width="2"
      />
    </svg>
  {/if}
</div>

<style>
  .board {
    display: grid;
    grid-template-rows: repeat(var(--rows), 1fr);
    gap: 5.5px;
    max-height: 420px;
    flex-grow: 1;
    aspect-ratio: var(--cols) / var(--rows);
    padding: 10px;
    position: relative;
  }
  svg {
    position: absolute;
    z-index: -1;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    width: min(130%, 100vw);
  }
  path {
    stroke: var(--bg-secondary);
  }
</style>
