<script lang="ts">
  import P5, { type Sketch } from "p5-svelte";
  let width = 55;
  let height = 55;

  const sketch: Sketch = (p5) => {
    p5.setup = () => {
      p5.createCanvas(400, 400);
    };

    p5.draw = () => {
      let myWidth = width + Math.random() * 10;
      p5.ellipse(p5.width / 2, p5.height / 2, myWidth, height);
    };
  };
</script>

<label>
  Width
  <input type="range" bind:value={width} min="100" max="1000" step="1" />
  {width}
</label>

<label>
  Height
  <input type="range" bind:value={height} min="100" max="1000" step="1" />
  {height}
</label>

<P5 {sketch} />
