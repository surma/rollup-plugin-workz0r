# rollup-plugin-workz0r

An ill-named [rollup] plugin that makes workers “just work”. This plugin makes rollup handle `new Worker()` similarly to `import()` calls.

```
$ npm install --save rollup-plugin-workz0r
```

## Usage

```js
// rollup.config.js
import workz0r from "rollup-plugin-workz0r";

export default {
  input: "src/main.js",
  plugins: [workz0r()]
};
```

And now you can just use the `Worker` constructor and rollup with do its bundling thing.

[rollup]: https://rollupjs.org/

---

License Apache-2.0
