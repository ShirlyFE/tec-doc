Yarn is a new JavaScript package manager built by Facebook, Google, Exponent and Tilde. As can be read in the official announcement, its purpose is to solve a handful of problems that these teams faced with npm, namely:

installing packages wasn’t fast/consistent enough, and
there were security concerns, as npm allows packages to run code on installation.
But, don’t be alarmed! This is not an attempt to replace npm completely. Yarn is only a new CLI client that fetches modules from the npm registry. Nothing about the registry itself will change — you’ll still be able to fetch and publish packages as normal.

Should everyone jump aboard the Yarn hype train now? Chances are you never encountered these problems with npm. In this article, we’re going to compare npm and Yarn, so you can decide which is best for you.

Yarn vs npm: Functional Differences
At a first glance Yarn and npm appear similar. As we peek under the hood though, we realize what makes Yarn different.

The yarn.lock File
In package.json, the file where both npm and Yarn keep track of the project’s dependencies, version numbers aren’t always exact. Instead, you can define a range of versions. This way you can choose a specific major and minor version of a package, but allow npm to install the latest patch that might fix some bugs.

In an ideal world of semantic versioning, patched releases won’t include any breaking changes. This, unfortunately, is not always true. The strategy employed by npm may result into two machines with the same package.json file, having different versions of a package installed, possibly introducing bugs.

To avoid package version mis-matches, an exact installed version is pinned down in a lock file. Every time a module is added, Yarn creates (or updates) a yarn.lock file. This way you can guarantee another machine installs the exact same package, while still having a range of allowed versions defined in package.json.

In npm, the npm shrinkwrap command generates a lock file as well, and npm install reads that file before reading package.json, much like how Yarn reads yarn.lock first. The important difference here is that Yarn always creates and updates yarn.lock, while npm doesn’t create one by default and only updates npm-shrinkwrap.json when it exists.

yarn.lock documentation
npm shrinkwrap documentation
Parallel Installation
Whenever npm or Yarn needs to install a package, it carries out a series of tasks. In npm, these tasks are executed per package and sequentially, meaning it will wait for a package to be fully installed before moving on to the next. Yarn executes these tasks in parallel, increasing performance.

For comparison, I installed the express package using both npm and Yarn without a shrinkwrap/lock file and with a clean cache. This installs 42 packages in total.

npm: 9 seconds
Yarn: 1.37 seconds
I couldn’t believe my eyes. Repeating the steps yielded similar results. I then installed the gulp package, resulting in 195 dependencies.

npm: 11 seconds
Yarn: 7.81 seconds
It seems the difference closely depends on the amount of packages that are being installed. Either way, Yarn is consistently faster.

Cleaner Output
By default npm is very verbose. For example, it recursively lists all installed packages when running npm install <package>. Yarn on the other hand, isn’t verbose at all. When details can be obtained via other commands, it lists significantly less information with appropriate emojis (unless you’re on Windows).

Yarn vs npm: CLI Differences
Other than some functional differences, Yarn also has different commands. Some npm commands were removed, others modified and a couple of interesting commands were added.

yarn global
Unlike npm, where global operations are performed using the -g or --global flag, Yarn commands need to be prefixed with global. Just like npm, project-specific dependencies shouldn’t need to be installed globally.

The global prefix only works for yarn add, yarn bin, yarn ls and yarn remove. With the exception of yarn add, these commands are identical to their npm equivalent.

yarn global documentation
yarn install
The npm install command will install dependencies from the package.json file and allows you to add new packages. yarn install only installs the dependencies listed in yarn.lock or package.json, in that order.

yarn install documentation
npm install documentation
yarn add [–dev]
Similar to npm install <package>, yarn add <package> allows you to add and install a dependency. As the name of the command implies, it adds a dependency, meaning it automatically saves a reference to the package in the package.json file, just as npm’s --save flag does. Yarn’s --dev flag adds the package as a developer dependency, like npm’s --save-dev flag.

yarn add documentation
npm install documentation
yarn licenses [ls|generate-disclaimer]
At the time of writing, no npm equivalent is available. yarn licenses ls lists the licenses of all installed packages. yarn licenses generate-disclaimer generates a disclaimer containing the contents of all licenses of all packages. Some licenses state that you must include the project’s license in your project, making this a rather useful tool to do that.

yarn licenses documentation
yarn why
This command peeks into the dependency graph and figures out why given package is installed in your project. Perhaps you explicitly added it, perhaps it’s a dependency of a package you installed. yarn why helps you figure that out.

yarn why documentation
yarn upgrade [package]
This command upgrades packages to the latest version conforming to the version rules set in package.json and recreates yarn.lock. This is similar to npm update.

Interestingly, when specifying a package, it updates that package to latest release and updates the tag defined in package.json. This means this command might update packages to a new major release.

yarn upgrade documentation
yarn generate-lock-entry
The yarn generate-lock-entry command generates a yarn.lock file based on the dependencies set in package.json. This is similar to npm shrinkwrap. This command should be used with caution, as the lock file is generated and updated automatically when adding and upgrading dependencies via yarn add and yarn upgrade.

yarn generate-lock-entry documentation
npm shrinkwrap documentation


CLI commands comparison

npm (v5)	Yarn
npm install	yarn install
(N/A)	yarn install --flat
(N/A)	yarn install --har
npm install --no-package-lock	yarn install --no-lockfile
(N/A)	yarn install --pure-lockfile
npm install [package]	yarn add [package]
npm install [package] --save-dev	yarn add [package] --dev
(N/A)	yarn add [package] --peer
npm install [package] --save-optional	yarn add [package] --optional
npm install [package] --save-exact	yarn add [package] --exact
(N/A)	yarn add [package] --tilde
npm install [package] --global	yarn global add [package]
npm update --global                  	yarn global upgrade                    
npm rebuild	yarn install --force
npm uninstall [package]	yarn remove [package]
npm cache clean	yarn cache clean [package]
rm -rf node_modules && npm install	yarn upgrade


According to Facebook’s announcement, the immediate need for Yarn was the npm’s dependency on having an active internet connection, which broke down Continuous Integration on their offline Sandbox environments i.e. npm install doesn’t work if your Environment is offline.
This means that you can be offline, but still install your npm packages using Yarn if you have installed them at some point in the past.This works because Yarn pulls the packages from it’s global cache, where it stores every package it ever downloads.


Other awesome features of Yarn
Yarn uses checksums to verify the integrity of every installed package before executing code.
Concise lockfile format, and a deterministic algorithm for installs. This means that Yarn is able to guarantee that an install that worked on one system will work exactly the same way on any other system. Isn’t that what you always wanted?
npm and bower — Install any package from either npm or Bower and keep your package workflow the same.
Flat mode — Resolve mismatching versions of dependencies to a single version to avoid creating duplicates.
Network Resilience — A single request failing won’t cause an install to fail. Requests are retried upon failure.
