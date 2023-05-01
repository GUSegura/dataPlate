<script>
    export let x = 50;
    export let y = 50;
    export let width = 200;
    export let height = 20;
    export let stops = [
      {offset: '0%', color: '#FFFFFF'},
      {offset: '100%', color: '#145e90'}
    ];
  </script>
  
  <svg>
    <defs>
      <linearGradient id="gradient-rect" x1="0%" y1="0%" x2="100%" y2="0%">
        {#each stops as stop}
          <stop offset={stop.offset} stop-color={stop.color} />
        {/each}
      </linearGradient>
    </defs>
    <rect class="legend" x={x} y={y} width={width} height={height} fill="url(#gradient-rect)" />
    {#each Array.from(Array(6).keys()) as i}
      <text class="legend" x={x + i * (width / 5)} y={y + height + 10} font-size="10px">{i}</text>
    {/each}
    <text x={150} y={y - 25} class="legend" font-size="12px" text-anchor="middle">Days adults in the household reduced food consumption</text>
    <text x={150} y={y - 13} class="legend" font-size="12px" text-anchor="middle">so that young children could eat (out of the last 7 days)</text>
  </svg>