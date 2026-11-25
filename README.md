# easy-confetti

Launch canvas confetti from a DOM element, with control over particle size, motion, and front/back colors.

[![npm](https://img.shields.io/npm/v/easy-confetti.svg?colorB=brightgreen)](https://www.npmjs.com/package/easy-confetti)
[![NPM Downloads](https://img.shields.io/npm/dm/easy-confetti.svg?style=flat)](https://www.npmjs.com/package/easy-confetti)

[Demo](https://codepen.io/ux-ui/full/NWmaVKQ)

## Installation

```bash
npm install easy-confetti
```

## Quick start

In a browser app with a bundler, add a button as the confetti origin:

```html
<button id="sprinkler" type="button">Celebrate</button>
```

The library appends a canvas with `id="easy-confetti"` to `document.body`. Add this CSS to make it cover the viewport without blocking clicks:

```css
#easy-confetti {
  position: fixed;
  inset: 0;
  width: 100%;
  height: 100%;
  pointer-events: none;
}
```

Run this TypeScript module after the button exists:

```ts
import EasyConfetti from 'easy-confetti';

const button = document.getElementById('sprinkler');

if (!button) {
  throw new Error('Sprinkler button was not found');
}

const confetti = new EasyConfetti();

button.addEventListener('click', () => confetti.sprinkle());
```

Each click emits a burst from the center of the button. Creating an instance starts the animation loop; call `confetti.destroy()` when you remove this part of the page.

## API

`EasyConfetti` is the default export. `new EasyConfetti(options?)` creates the canvas, measures it, and starts the animation loop. You can supply any subset of the options below.

### Options

| Option | Type | Default | Description |
| --- | --- | --- | --- |
| `particleCount` | `number` | `70` | Number of particles per `sprinkle()` call. |
| `particleSizeRange` | `object` | See below | Width and height ranges for each particle. |
| `initialSpeed` | `number` | `25` | Initial launch speed. |
| `gravity` | `number` | `0.65` | Gravity applied each frame. |
| `airResistance` | `number` | `0.08` | Air resistance factor. |
| `maxFallSpeed` | `number` | `6` | Base cap for a particle's fall speed. |
| `flipFrequency` | `number` | `0.017` | Controls how often particles flip between their front and back colors. |
| `colors` | `Array<{ front: string; back: string }>` | See below | Color pairs for the two sides of a particle. |

`particleSizeRange` has the shape `{ width: [number, number]; height: [number, number] }`. The default ranges and palette are:

```ts
const defaults = {
  particleSizeRange: {
    width: [5, 20],
    height: [10, 18],
  },
  colors: [
    { front: '#FF5733', back: '#C70039' },
    { front: '#DAF7A6', back: '#FFC300' },
  ],
};
```

### Methods

- `sprinkle()` emits one burst from the `#sprinkler` element.
- `destroy()` stops the animation loop, stops observing the canvas with `ResizeObserver`, and removes the canvas.
- `init()` sets up canvas sizing, the animation loop, and the resize observer. The constructor calls it automatically; you do not need to call it after `new EasyConfetti()`.

## Change colors

As an alternative to the instance in Quick start, pass a palette when creating it. This example uses the same button and canvas CSS:

```ts
const confetti = new EasyConfetti({
  colors: [
    { front: '#4C9E14', back: '#30610A' },
    { front: '#CC7600', back: '#874900' },
    { front: '#FF4E44', back: '#AA302B' },
  ],
});

button.addEventListener('click', () => confetti.sprinkle());
```

## Repeat a burst

Continuing with `confetti` from Quick start, emit three bursts 250 milliseconds apart:

```ts
const sprinkleMultiple = (times: number, interval: number) => {
  let count = 0;

  const intervalId = setInterval(() => {
    confetti.sprinkle();
    count++;

    if (count === times) {
      clearInterval(intervalId);
    }
  }, interval);

  return intervalId;
};

const intervalId = sprinkleMultiple(3, 250);
```

If you remove the page before the sequence finishes, call `clearInterval(intervalId)` before `confetti.destroy()`.

## Browser support

- The `#sprinkler` element and `document.body` must exist before you create the instance.
- The browser needs Canvas 2D, `requestAnimationFrame`, and `ResizeObserver`.
- Canvas layout comes from your CSS; the library sets its drawing-buffer size from the rendered size.

## License

MIT
