# Quickstart code snippets

This directory has some sample code that's included in the docs via [Vuepress code snippet imports](https://v1.vuepress.vuejs.org/guide/markdown.html#import-code-snippets).

The `put-files.js` and `package-example.json` files are inlcuded in the Quickstart guide.

There's also `put-files-staging.js`, which is the same as put-files but targeting the staging api endpoint so we can test things, and a real `package.json` so you can `npm install` and test the scripts. Eventually it would be cool to actually write a smoke test that gets run in CI so we can make sure new versions of the client don't break the example. 

For now, you can `npm install` here and run `node put-files-staging.js --token=<your-token> path-to-file` to test things manually.

**Note**: the `getFilesFromPath` helper is exported in the main branch of web3.storage but hasn't been packaged on NPM yet, so the `package.json` here assumes you have the [web3.storage repo](https://github.com/web3-storage/web3.storage) cloned next to the docs repo (they should both have the same parent directory). We can fix that as soon as a new version of the client is published.