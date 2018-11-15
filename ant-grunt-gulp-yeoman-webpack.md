## Yeoman vs Slush

When comparing Yeoman vs Slush, the Slant community recommends Yeoman for most people. In the question“What are the best workflow wrappers for front-end development?” Yeoman is ranked 1st while Slush is ranked 3rd. The most important reason people chose Yeoman is:
Yeoman has an active community with new generators being created at a rapid pace. Because of the momentum behind the community, you can expect good support and adoption for new tools and frameworks promptly after they come out.

bower Focuses on packages that are used in the browser. Each bower install <packagename> points to exactly one file to be included for (more can be downloaded). Due to the success of webpack, browserify and babel it's mostly obsolete as a first class dependency manager.

npm Historically focuses on NodeJS code but has overthrown bower for browser modules. Don't let anyone fool you: NPM is huge. NPM also loads MANY files into your project and a fresh npm install is always a good reason to brew a new cup of coffee. NPM is easy to use but can break your app when changing environments due to the loose way of referencing versions and the arbitrariness of module publishing. Research Shrink Wrap and npm install --save-exact

grunt Facilitates task automation. Gulps older and somewhat more sluggish brother. The JavaScript community used to hang out with him in 2014 a lot. Grunt is already considered legacy in some places but there is still a great amount of really powerful automation to be found. Configuration can be a nightmare for larger use-cases. There is a grunt module for that though.

gulp Does the same thing as grunt but is faster.

npm run-script You might not need task runners at all. NodeJS scripts are really easy to write so most use-cases allow for customizedtask-automation workflow. Run scripts from the context of your package.json file using npm run-script

webpack Don't miss out on webpack. Especially if you feel lost on the many ways of writing JavaScript into coherent modular code. Webpack packages .js files into modules and does so splendidly. Webpack is highly extensible and offers a good development environment too: webpack-dev-server Use in conjunction with babel for the best possible JavaScript experience to date.

Yeoman Scaffolding. Extremly valuable for teams with different backgrounds as it provides a controllable common ground for your projects architecture. There even is a scaffolding for scaffolds.(slushJS VS yeoman VS lineman)



Gulp与Grunt一样，也是一个自动任务运行器


### Webpack dev server proxy config
export const devServer = {
  host,
  port,
  compress: true, // enable gzip compression
  historyApiFallback: true, // true for index.html upon 404, object for multiple paths
  hot: true,
  contentBase: path.join(rootPath, 'dist'),
  publicPath: output.publicPath,
  quiet: false,
  noInfo: false,
  lazy: false,
  stats: {
    colors: true,
    chunks: false,
    chunkModules: false,
    modules: false,
    errorDetails: true
  },
  proxy: [{
    path: '/rest/**',
    target: 'https://devhost/test/',
    secure: false
  }]
};

## As a response to the release of Parcel, webpack implemented “0-config” in their latest release of webpack called webpack 4.

Does that mean we now should go back to using webpack 4? Or does Parcel have some advantages over webpack 4? I will go through them both in this article and compare them.

### TOC:

Comparison of zero-config
What do you get with zero-config?
Example project
Caching
Build time
Code splitting
Dev server
Bundle analysis tools
Authors/community
So which one should I choose?

#### Code splitting
Code splitting is a way to increase the performance of your application by splitting the bundle into smaller bundles.

Parcel claims to support zero-configuration code splitting out of the box.

Webpack support code splitting according to documentation.

#### Dev server
When developing on localhost you want short feedback loops – when you make a change in the code you want the change reflected as quick as possible in your browser. You don’t want to do a full production build every time.

Both Parcel and webpack has a dev server to be used when developing on localhost.

webpack requires you to config a webpack-dev-server.

Parcel supports it out-of-the box. Just start it with:
```
parcel index.html
```

#### Bundle analysis tools
Webpack has many bundle analysis tools which are useful for debugging and optimizing the bundle size. Some examples:

https://github.com/th0r/webpack-bundle-analyzer
https://github.com/danvk/source-map-explorer
I wrote an article on how to use these tools and optimizing bundle size in webpack

Parcel has bundle analysis tools as well. For example https://github.com/gregtillbrook/parcel-plugin-bundle-visualiser

#### Authors/community
Webpack has a core team of 5 people and they have a long list of sponsors and backers. There have been 415 different contributors in the lifetime of webpack.

Parcel has not defined their core team as clear as webpack. Parcels list of sponsors and backers are much smaller than webpacks. Parcel has a total of 89 contributors.

Both webpack and Parcel have a active communities. I think both will live for many years.

#### So which one should I choose?
Webpack is the stable choice. You will not get fired for picking webpack. But you don’t get as much stuff for free such as optimized bundles, and code splitting.

The strategy I am using is the following: when I start a new project I start with parcel because it’s so quick to get up and running. If/when it grows large, and I need a more advanced configuration, I move over to webpack.

## Resource
[webpack dllplugin的使用姿势](https://www.jianshu.com/p/d6e0ab2fed25)
[使用 DllPlugin 大幅提升 Webpack 的编译效率](http://imlianer.com/a/20170527-webpack-dll-plugin)
[Webpack & The Hot Module Replacement](https://medium.com/@rajaraodv/webpack-hot-module-replacement-hmr-e756a726a07)
[Webpack HMR 原理解析](https://zhuanlan.zhihu.com/p/30669007)
[Parcel VS Webpack](http://blog.jakoblind.no/parcel-webpack/)




