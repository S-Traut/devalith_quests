# OSBX - How to Layout

In storyboard programming, creating a layout system can be really powerful to gain productivity and have a generic template to create some effects in a better and more accurate way.

A layout system is a tool that can solve most positionning problematics you can encounter as a storyboarder. e.g: You want to create a transition that use 10 bars positionned vertically, the more common solution that might come in your mind is creating a for loop that generate those 10 bars, which is fine but can become very something you will repeat over and over in all your storyboard effects.

So the solution for that is creating a tool that can give all the positions you need, that way you would just have to call a function to generate the position for a specific layout instead of making a new one everytime.

---

## Set-up

For this lesson, we are gonna use osbx because it gives a good boilerplate plugin system. First, let's create a very basic project with 2 files:

**plugin_layout.ts**

```js
import { Plugin } from "osbx";

/**
 * The Layout plugin class will contain the logic
 * behind the positions generation system using
 * some useful functions
 */
export default class Layout extends Plugin {}
```

**main.ts**

```js
import { osbx, Component } from "osbx";
import Layout from "./plugin_layout";

/**
 * Storyboard entry point class
 */
class Main extends Component {
  init() {
    // We simply load our plugin in the component
    const layout = this.getPlugin(Layout);
  }
}

osbx.create(Main);
```

**osbx.config.json**

```json
{
  "beatmap_path": "./",
  "beatmap_name": "layout"
}
```

Install osbx: `npm init -y && npm i osbx`

You can run your project using nodemon or ts-node:
`nodemon main.ts`

---

## Creating a basic row

Now that we have a plugin up and running properly, let's start codding our first function that will help us creating a row.

First thing first, let's thing of a clean approach to have a good typing for our system. The function will take a configuration as input, and give us back void, however the callback should return a specific object that will be helpful for positions.

Let's create those types at the top of our plugin script!

**plugin_layout.ts**

```ts
import { Plugin } from "osbx";

export type Position = {
  x: number;
  y: number;
};

export type RowConfig = {
  position: Position;
  width: number;
  element_count: number;
};

export type LayoutOutput = {
  position: Position;
  i: number;
};

export interface LayoutCallback {
  (ouput: LayoutOutput): void;
}
```

We can then, change our function signature like so:

```ts
public row(config: RowConfig, callback: LayoutCallback): void
```

So we have now a good base for creating our row function, what we have done is that the function asks for a configuration object, and will setup a callback parameter that we'll use to position our elements.

so let's first create a simple configuration in the **main.ts** class:

```ts
init() {

  const layout = this.getPlugin(Layout);

  // We define a config object that have some
  // simple values as example
  const configuration: RowConfig = {
    position: { x: 320, y: 240 },
    element_count: 10,
    width: 300,
  };

  layout.row(configuration, element => {
    // This function will be called for every
    // elements in our layout. (10 times)
    console.log(element);
  });

}
```

So now, your code should compile, but nothing will happens, so let's dig in the plugin side to write some functional code.

**plugin_layout.ts**

```ts
export default class Layout extends Plugin {
  public row(config: RowConfig, callback: LayoutCallback): void {
    // Let's loop the number of elements we have an generate a static position
    // for each of those
    for (let i = 0; i < config.element_count; i++) {
      // Creating the layoutoutput object
      const output: LayoutOutput = {
        position: { x: 320, y: 240 },
        i: i,
      };

      // And finally call our function to give the hand on the component code.
      callback(output);
    }
  }
}
```

At this point your console should output 10 values with i increasing.

So what's next? let's now simply code our function to position our row elements properly!

**plugin_layout.ts**

```ts
for (let i = 0; i < config.element_count; i++) {
  // Positioning logic for a row.
  // Starting from the left to the right.
  const element_width = config.width / config.element_count;
  const x = config.position.x - config.width / 2 + i * element_width;
  const output: LayoutOutput = {
    position: { x: x, y: config.position.y },
    i: i,
  };

  callback(output);
}
```

And finally here the implementation of the code in our component:

**main.ts**

```ts
layout.row(configuration, (element) => {
  const sprite = layout.createSprite(
    "sb/p.png",
    Layers.Background,
    Origins.Centre,
    element.position
  );
  sprite.fade([0, 10000], [1, 1]);
  sprite.vScale(0, [40, 100]);
});
```

So we now simply use the position of the generated element in the layout plugin function to place our elements in the component. So what's is great about this? You can export your plugin to any of your projects and generate a row in only few seconds instead of writing the function anytime your want to create this kind of positioning!

---

## Creating a grid

WIP

---

## Creating a circle

WIP

---
