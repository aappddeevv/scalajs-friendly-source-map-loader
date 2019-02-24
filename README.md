Drop in replacement for source-map-loader but this loader understands `file://`
and `http[s]://` prefixes in source map files like the kind you find in scala.js
generated source maps.

Including scala sources increases the map size so use `bundleHttp: false` to not
bundle them. They are bundled by default. Warnings that "file://" resources
cannot be found can be turned off using `skipFileURLWarnings` which is true by
default. If you depend on other scala.js libraries, they may have `file://`
links embedded that cause warnings. If you work offline and do not have access
to http resources (this loader does not cache http resources), then set your
`bundleHttp` option using a nodejs environment variable: `bundleHttp:
process.env.OFFLINE` then set the environment variable `OFFLINE="true"` before
you run webpack. Note that "true" is in quotes to make at a string environment
variable.

As a drop-in replacement:
```javascript
...
            {
                test: /\.js$/,
                // use loader defaults
                use: ["scalajs-friendly-source-map-loader"],
                // set options explicitly
                use: [
                { 
                    loader: "scalajs-friendly-source-map-loader",
                    options: {
                        skipFileURLWarnings: true, // or false, default is true
                        bundleHttp: true // or false, default is true
                    }
                }],
                enforce: "pre",
            },
```

To use in your loaders *only* for your scalajs output:

```javascript
...
            {
                test: /\.js$/,
                use: [
                  { 
                      loader: "scalajs-friendly-source-map-loader"
                      options: {
                        bundleHttp: false // or use the short version above
                       },
                  }],
                enforce: "pre",
                include: [scalapath],
            },
            {
                test: /\.js$/,
                use: ["source-map-loader"],
                enforce: "pre",
                // does not handle scala.js issued https: remote resources
                exclude: [/node_modules/, scalapath],
            },
...
```

Loaders are used right to left so the normal source-map-loader will run first
but *ignore* the scala.js output leaving the scala js file to the friendly
loader. `scalapath` is a resolved path to your scala.js compiler output file.

