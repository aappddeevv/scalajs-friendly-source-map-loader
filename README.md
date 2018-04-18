Drop in replacement for source-map-loader but this loader understands `file://`
and `http[s]://` prefixes in source map files like the kind you find in scala.js
generated source maps.

Including scala sources increases the map size so use `bundleHttp: false` to not
bundle them. They are bundled by default.

As a drop-in replacement:
```javascript
...
            {
                test: /\.js$/,
                loader: ["scalajs-friendly-source-map-loader"],
                options: {
                    bundleHttp: true // or false, default is true
                },
                enforce: "pre",
            },
```
To use in your loaders *only* for your scalajs output:
```javascript
...
            {
                test: /\.js$/,
                loader: ["scalajs-friendly-source-map-loader"],                
                options: {
                    bundleHttp: true
                },
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

Loaders are used right to lift so the normal source-map-loader will run first
but *ignore* the scala.js output leaving that to the friendly loader.
