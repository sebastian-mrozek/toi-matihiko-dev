<script lang="ts">
  import P5, { type Sketch } from "p5-svelte";

  let WIDTH = 600,
    HEIGHT = 400,
    ONE = 50,
    BACKGROUND_COLOR = "#000f",
    FOREGROUND_COLOR = "#ffff",
    SPEED = 10,
    MAX_KORU_COUNT = 2,
    RANDOMIZE_COLORS = true;

  $: frameRate = SPEED / 10;
  $: HALF = ONE / 2;

  const START = 1,
    MIDDLE = 0,
    END = -1;

  const LIGHT = 1,
    DARK = 0;

  const sketch: Sketch = (p5) => {
    function calculateCanvasSize() {
      WIDTH = p5.windowWidth * 0.8;
      HEIGHT = p5.windowHeight * 0.68;
    }
    function randomizeColors() {
      if (RANDOMIZE_COLORS) {
        BACKGROUND_COLOR = getRandomColor(DARK);
        FOREGROUND_COLOR = getRandomColor(LIGHT);
      }
    }
    function drawKoru(row, column, mode) {
      let foregroundColor = getForegroundColor(row);
      let backgroundColor = getBackgroundColor(row);
      clearBg(row, column, mode, backgroundColor);
      drawCircle(row, column, foregroundColor);
    }

    function clearBg(row, column, mode, color) {
      let position = getClearRectPos(row, column, mode);
      let size = getClearRectSize(mode);
      p5.fill(color);
      p5.rect(position.x, position.y, size.x, size.y);
    }

    function getClearRectPos(row, column, mode) {
      var x, y;
      if (mode == END) {
        x = column * ONE + HALF;
      } else if (mode == START || mode == MIDDLE) {
        x = column * ONE;
      }
      y = row * HALF + 1;
      return p5.createVector(x, y);
    }

    function getClearRectSize(mode) {
      let width = mode == MIDDLE ? ONE : HALF;
      return p5.createVector(width + 1, HALF);
    }

    function drawCircle(row, column, color) {
      let x = column * ONE + HALF;
      let y = row * HALF;
      p5.fill(color);
      p5.ellipse(x, y, ONE, ONE);
    }

    function drawStripe(row) {
      let color = getForegroundColor(row);
      p5.fill(color);
      p5.rect(0, row * HALF, WIDTH, HALF);
    }
    function getForegroundColor(row) {
      return isEven(row) ? FOREGROUND_COLOR : BACKGROUND_COLOR;
    }

    function getBackgroundColor(row) {
      return isEven(row) ? BACKGROUND_COLOR : FOREGROUND_COLOR;
    }

    function isEven(i) {
      return i % 2 == 0;
    }

    function randomInt(from, to) {
      return p5.round(p5.random(from, to));
    }

    function getRandomColor(mode) {
      let color = "#";
      for (let i = 0; i < 3; i++) {
        const random = Math.random();
        const bit = ((mode ? 8 : 0) + random * 8) | 0;
        color += bit.toString(16);
      }
      return color;
    }

    p5.setup = () => {
      calculateCanvasSize();
      p5.createCanvas(WIDTH, HEIGHT);
      p5.noStroke();
      p5.frameRate(frameRate);
    };

    p5.windowResized = () => {
      calculateCanvasSize();
      p5.resizeCanvas(WIDTH, HEIGHT);
    };

    p5.draw = () => {
      // ONE = randomInt(5, 10) * 12;
      // HALF = ONE / 2;
      randomizeColors();
      p5.background(BACKGROUND_COLOR);
      p5.frameRate(frameRate);

      let rows = HEIGHT / HALF;
      let cols = WIDTH / ONE;
      p5.fill(FOREGROUND_COLOR);

      for (var row = 0; row < rows; row++) {
        drawStripe(row);
        if (row > 0 && row < rows - 1) {
          let column = isEven(row)
            ? randomInt(1, cols / 2 - MAX_KORU_COUNT - 1)
            : randomInt(cols / 2 + 1, cols - MAX_KORU_COUNT);
          drawKoru(row, column, END);
          let middleCount = randomInt(0, MAX_KORU_COUNT);
          for (var m = 1; m <= middleCount; m++) {
            drawKoru(row, column + m, MIDDLE);
          }
          drawKoru(row, column + middleCount + 1, START);
        }
      }
    };
  };
</script>

<div>
  <div class="control-panel">
    <label class="block">
      Size
      <input type="range" bind:value={ONE} min="20" max="200" step="3" />
      {ONE}
    </label>

    <label class="block">
      Speed
      <input type="range" bind:value={SPEED} min="5" max="50" step="1" />
      {SPEED}
    </label>

    <label class="block">
      Density
      <input
        type="range"
        bind:value={MAX_KORU_COUNT}
        min="0"
        max="20"
        step="1"
      />
      {MAX_KORU_COUNT}
    </label>

    <label class="block">
      Random colors
      <input type="checkbox" bind:checked={RANDOMIZE_COLORS} />
    </label>
  </div>
  <div class="picture">
    <P5 {sketch} />
  </div>
</div>

<style>
  .control-panel {
    margin-bottom: 1em;
  }
  .block {
    display: block;
  }
  .picture {
    border: 2em solid #efefef;
  }
</style>
