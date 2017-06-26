---
layout     : post
title      : "TODO Git dependencies"
author     : Jeremy Judeaux
author_url : "https://github.com/Volune"
date       : 2017-TODO 00:00:00
categories : announcements
share_text : "TODO"
---

There are various reason you may want to have dependencies from a git repository. It could be testing a library before publishing, or a specific commit. Or you may have a library on a private repository, and don't want the contrainst and benefits of a private registry.

Until recently having a dependency on git was not convenient, even worst if you need to transpile code in the library. The `prepublish` script was not working as expected, not at all for dependencies on git, generating a [4 years long npm issue](https://github.com/npm/npm/issues/3055), numerous blog posts and workarounds, not always successful.

Hopefully with Yarn *TODO version* and [NPM 5](https://github.com/npm/npm/releases/tag/v5.0.0), this dark age is over. Dependencies on git will be handled almost as if they come from a registry.

## Here is how it works

To benefit from the new feature, **your library needs to provide a `prepare` script**. Here is an example using Babel:

```json
{
  "name": "my-package",
  "version": "1.0.0",
  "main": "lib/index.js",
  "files": [
    "lib/*"
  ],
  "scripts": {
    "prepare": "babel src -d lib"
  },
  "devDependencies": {
    "babel-cli": "^6.24.1"
  }
}
```

When Yarn or NPM read the `package.json` and see the `prepare` script, a new installation flow happens:

- Yarn will clone the library from git, at the specified commit or branch
- Yarn will install the library's dependencies, including the `devDepdendencies`! As if we just executed `yarn install`. 
- Yarn will execute the `prepare` script. (In fact, that's also part of `yarn install`)
- Yarn will pack the library, in the same way it would for publishing. The resulting _packed content_ is used to install the library in your application.

That's it, you library with its code transpiled, ready for use in your application.
