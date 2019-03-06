# rollup-plugin-workz0r

An ill-named [rollup] plugin to add worker bundling support. With this plugin you can just use the `new Worker("somefile.js")` constructor and rollup will bundle that file and its dependencies into a separate chunk — similar to what happens with a dynamic import.

```
$ npm install --save rollup-plugin-workz0r
```

> Note: If you are a webpack user, take a look at [worker-loader](https://github.com/GoogleChromeLabs/worker-plugin).

## Usage

```js
// rollup.config.js
import workz0r from "rollup-plugin-workz0r";

export default {
  input: "src/main.js",
  plugins: [workz0r()]
};
```

## The modules-in-workers problems

At the time of writing, no browser has support for ES6 modules in workers. Until browsers have caught up, I wrote [`rollup-plugin-loadz0r`](https://github.com/surma/rollup-plugin-loadz0r) to make modules in workers “just work”. If you want to use these two plugins in conjunction, some extra plumbing is needed to let `loadz0r` know which chunks are worker chunks, as they need the loader code, too:

```js
// rollup.config.js
import loadz0r from "rollup-plugin-loadz0r";
import workz0r from "rollup-plugin-workz0r";

const workerModules = new Set();
export default {
  input: "input.js",
  output: {
    dir: "./dist",
    format: "amd"
  },
  plugins: [
    workz0r({
      onWorkerModule: id => workerModules.add(id)
    }),
    loadz0r({
      // `prependLoader` will be called for every chunk. If it returns `true`,
      // the loader code will be prepended.
      prependLoader: (chunk, inputs) => {
        // If the chunk contains one of the worker modules, prepend a loader.
        if (Object.keys(chunk.modules).some(mod => workerModules.has(mod))) {
          return true;
        }
        // If not, fall back to the default behavior.
        return loadz0r.isEntryModule(chunk, inputs);
      }
    })
  ]
};
```

[rollup]: https://rollupjs.org/
[loadz0r]: https://github.com/surma/loadz0r

---

License Apache-2.0
