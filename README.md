# webpack-middleware

> Run Webpack compiler as Express.js/Browsersync middleware

**Note**: *This is a fork from [webpack-dev-middleware](https://github.com/webpack/webpack-dev-middleware)
with the only difference that it supports configurations with mixed
web/node.js bundles (great for isomorphic apps).*

## What is it?

It's a simple wrapper middleware for webpack. It serves the files emitted from webpack over a connect server.

It has a few advantages over bundling it as files:

* No files are written to disk, it handle the files in memory
* If files changed in watch mode, the middleware no longer serves the old bundle, but delays requests until the compiling has finished. You don't have to wait before refreshing the page after a file modification.
* I may add some specific optimization in future releases.

## Getting Started

``` javascript
import webpack from 'webpack';
import webpackMiddleware from 'webpack-middleware';
import webpackConfig from './webpack.config.js'; // <- array of Webpack configs
import runNode from './runNode.js';

// Launch Webpack compiler in watch mode
const wp = webpackMiddleware(webpack(webpackConfig));

// Launch Node.js app in a child process
runNode('./build/server.js').then(() => {
  // Launch Browsersync dev server in proxy mode
  const bs = require('browser-sync').create();
  bs.init({
    proxy: {
      target: 'localhost:3000', // <- where Node.js app is running
      middleware: [wp]
    }
  });
}, (err) => console.error(err));
```

### Configuration Options

``` javascript
app.use(webpackMiddleware(webpack({
	// webpack options
	// webpackMiddleware takes a Compiler object as first parameter
	// which is returned by webpack(...) without callback.
	entry: '...',
	output: {
		path: '/'
		// no real path is required, just pass '/'
		// but it will work with other paths too.
	}
}), {
	// all options optional

	noInfo: false,
	// display no info to console (only warnings and errors)

	quiet: false,
	// display nothing to the console

	lazy: true,
	// switch into lazy mode
	// that means no watching, but recompilation on every request

	watchOptions: {
		aggregateTimeout: 300,
		poll: true
	},
	// watch options (only lazy: false)

	publicPath: '/assets/',
	// public path to bind the middleware to
	// use the same as in webpack

	headers: { 'X-Custom-Header': 'yes' },
	// custom headers

	stats: {
		colors: true
	}
	// options for formatting the statistics
}));
```

## Related Projects

 * [Webpack](http://webpack.github.io/) — JavaScript module bundler
 * [React Starter Kit](https://github.com/kriasoft/react-starter-kit) — Isomorphic web application boilerplate
 * [isomorphic-style-loader](https://github.com/kriasoft/isomorphic-style-loader) — Isomorphic CSS styles loader for Webpack