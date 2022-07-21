<script lang="ts">

  import Good from "$lib/good.wav"
  import Bad from "$lib/bad.wav"
  import Arrow from "$lib/Arrow.svelte"

  function playGood() {
    var audio = new Audio(Good);
    audio.play();
  }

  function playBad() {
    var audio = new Audio(Bad);
    audio.play(); 
  }

  let score = 0;

  interface Direction {
    name: string;
    angle: number; // in degrees
    enabled?: boolean;
  }

  $: if (directions) {
    current = getRandomDirection()
  }

  let directions: { [key: string]: Direction } = {
    "left": {
      name: "left",
      angle: 0,
      enabled: true
    },
    "right": {
      name: "right",
      angle: 180,
      enabled: true
    },
    "up": {
      name: "up",
      angle: 90,
      enabled: true
    },
    "down": {
      name: "down",
      angle: 270,
      enabled: true
    }
  }

  function getRandomDirection(): Direction {

    const filteredDirections = Object.values(directions).filter(direction => direction.enabled)

    // shuffle directions
    directions = Object.fromEntries(Object.values(directions).map(value => ({ value, sort: Math.random() }))
      .sort((a, b) => a.sort - b.sort)
      .map(({ value }) => value)
      .map(value => [value.name, value]))

    return filteredDirections[Math.floor(Math.random() * filteredDirections.length)];
  }

  let current = getRandomDirection()
</script>

<div class="m-8 p-8 text-center border border-black bg-white rounded-lg shadow-xl">
  {#each Object.values(directions) as direction}
    <div class="inline-block mx-4">
      {direction.name}: <input type="checkbox" bind:checked={direction.enabled}>
    </div>
  {/each}
  <p class="m-6 mb-10 text-6xl font-bold">Score: {score}</p>

  <div class="m-8 inline-block" style="transform: rotate({current.angle}deg)"><Arrow/></div>
  <br/>
  {#each Object.values(directions) as direction, _ (Math.random())}
    {#if direction.enabled}
      <button on:click={() => {
        if (direction.name === current.name) {
          score++;
          playGood()
        } else {
          score = 0;
          playBad()
        }
        current = getRandomDirection();
      }} class="px-4 py-2 m-4 text-3xl rounded-md shadow-lg ring ring-gray-400 bg-gray-100 hover:bg-gray-200 active:bg-gray-300">{direction.name}</button>
    {/if}
  {/each}
</div>