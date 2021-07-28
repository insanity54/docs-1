---
title: Store
description: Learn how to store your data on the decentralized web with Web3.Storage.
---

# How to store data using Web3.Storage

In this how-to guide, **you'll learn how to store data programmatically for your development projects using the Web3.Storage JavaScript client**. This includes making your data available on the decentralized [IPFS](https:://ipfs.io) network with persistent long-term storage provided by [Filecoin](https://filecoin.io).

**If you just want to quickly store a few files using Web3.Storage rather than include upload functionality in an app or service you're building**, you may want to skip this guide for now and simply use the [Files page][site-files] on the Web3.Storage site.

For developers, Web3.Storage provides a simple interface for storing data using syntax inspired by familiar web APIs such as [`fetch`][mdn-fetch] and [`File`][mdn-file]. This guide focuses on the [JavaScript client library][reference-js], which is the simplest way to use Web3.Storage programmatically. 
<!-- TODO: bring this back once the HTTP reference exists
If you're using another language, see the [HTTP API reference][reference-http] for details on working with the underlying HTTP API.
-->
Uploading data to Web3.Storage using the JavaScript client library requires an API token, which in turn requires a Web3.Storage account. If you already have an account and a token, read on. If not, have a look at the [quickstart guide][quickstart-guide] to get up and running in just a few minutes.


::: danger CAUTION
All data uploaded to Web3.Storage is available to anyone who requests it using the correct CID. Do not store any private or sensitive information in an unencrypted form using Web3.Storage.
:::

## Installing the client

In your JavaScript project, add the `web3.storage` package to your dependencies:

```bash
npm install web3.storage
```

## Creating a client instance

To create a `Web3Storage` client object, we need to pass an access token into the [constructor][reference-js-constructor]:

```javascript
import { Web3Storage } from 'web3.storage'
const token = process.env.WEB3_STORAGE_TOKEN
const client = new Web3Storage({ token })
```

In the example above, we read the token from an environment variable called `WEB3_STORAGE_TOKEN`, since baking credentials into source code is generally a bad idea. To help make environment variables easier to manage, consider using a package like [dotenv](https://www.npmjs.com/package/dotenv), which will read variables from a file called `.env` in your project's root directory. Just make sure not to check the `.env` file into source control!

## Preparing files for upload

The Web3.Storage client's [`put` method][reference-js-put] accepts an array of [`File` objects](https://developer.mozilla.org/en-US/docs/Web/API/File).

When running in the browser, you can use the native `File` object provided by the browser runtime.

On Node.js, import `File` from the `web3.storage` package along with the `Web3Storage` object:

```javascript
import { File, Web3Storage } from 'web3.storage'
```

Once you have a `File` constructor in scope, you can prepare your files for upload:

```javascript
const files = [
  new File(['contents-of-file-1'], 'plain-utf8.txt'),
  new File([aBlobOrArrayBuffer], 'pic.jpeg')
]
```

You can also create a nested directory structure by adding files with path components separated by `/` characters:

```javascript
const files = [
  new File(kittyImageBytes, '/images/cats/kitty.jpeg'),
  new File(pugImageBytes, '/images/dogs/pug.jpeg'),
]
```

In Node.js, the `web3.storage` package exports some helpful utility functions from the [`files-from-path` module](https://www.npmjs.com/package/files-from-path) that allow you to easily read `File` objects from the local file system. The `getFilesFromPath` helper asynchronously returns an array of `File`s that you can use directly with the `put` client method:

```javascript
import { getFilesFromPath, Web3Storage } from 'web3.storage'
const token = 'your-api-token'
const client = new Web3Storage( { token })

async function storeFiles(path) {
  const files = await getFilesFromPath(path)
  const cid = await client.put(files)
}
```

If you expect to be loading a lot of large files, you may be better served by the [`filesFromPath` helper](https://github.com/web3-storage/files-from-path#filesfrompath). It reduces memory pressure by `yield`ing `File` objects one by one as they're loaded from disk, instead of loading everything into memory. You can then issue multiple `put` requests to send each file to Web3.Storage.

In the browser, you can also use a [file input element][mdn-file-input] to allow the user to select files for upload, instead of creating `File` objects manually:

```javascript
const fileInput = document.querySelector('input[type="file"]')
const files = fileInput.files
```

::: tip 
**When uploading multiple files, try to give each file a unique name.** All the files in a `put` request will be bundled into one content archive, and linking to the files inside is much easier if each file has a unique, human-readable name.
:::

## Uploading to Web3.Storage

Once you have a client object and an array of `File`s, uploading is simple:

```javascript
const cid = await client.put(files)
```

::: warning IMPORTANT
Deleting files from the Web3.Storage site's [Files page][site-files] will remove them from the file listing for your account, but that doesn't prevent nodes on the [decentralized storage network][concepts-decentralized-storage] from retaining copies of the data indefinitely. Do not use Web3.Storage for data that may need to be permanently deleted in the future.
:::

### Showing progress to the user

The `put` method has some options that can be passed in to get progress on the upload as it happens in the background. There are two callback parameters you can use: `onRootCidReady`, and `onChunkStored`.

The `onRootCidReady` callback is invoked as soon as the client has calculated the content identifier (CID) that identifies the data being uploaded. Because this calculation happens locally on the client, the callback is invoked before the upload begins.

As each chunk of data is uploaded, the `onChunkStored` callback gets invoked with the size of the chunk in bytes passed in as a parameter.

Here's a simple example of using the callbacks to print the progress of an upload to the console:

```javascript
async function storeWithProgress(files) {  
  // show the root cid as soon as it's ready
  const onRootCidReady = cid => {
    console.log('uploading files with cid:', cid)
  }

  // when each chunk is stored, update the percentage complete and display
  const totalSize = files.map(f => f.size).reduce((a, b) => a + b, 0)
  let uploaded = 0

  const onChunkStored = size => {
    uploaded += size
    const pct = totalSize / uploaded
    console.log(`Uploading... ${pct.toFixed(2)}% complete`)
  }

  // assume that getClient returns a Web3.Storage client instance
  const client = getClient()
  return client.put(files, { onRootCidReady, onChunkStored })
}
```

## Next steps

The `put` method returns an IPFS [content identifier (CID)][ipfs-docs-cid] that can be used to fetch your files over IPFS. To learn how to fetch your data using the Web3.Storage client, or directly from IPFS using a gateway or the IPFS command line, see the [how-to guide on retrieval][howto-retrieve].

You can also get more information about the status of your data. See the [query how-to guide][howto-query] to learn how to get more details about your data, including the status of any Filecoin storage deals.

<!-- internal links -->

[reference-js]: ../reference/client-library.md
[reference-js-constructor]: ../reference/client-library.md#constructor
[reference-js-put]: ../reference/client-library.md#store-files

[quickstart-guide]: ../README.md
[howto-retrieve]: ./retrieve.md
[howto-query]: ./query.md
[concepts-decentralized-storage]: ../concepts/decentralized-storage.md

<!-- links to the web3.storage site -->
[site-profile]: https://web3.storage/profile/
[site-files]: https://web3.storage/files/

<!-- external links -->
[ipfs-docs-cid]: https://docs.ipfs.io/concepts/content-addressing/
[ipfs-docs-cli-quickstart]: https://docs.ipfs.io/how-to/command-line-quick-start/
[mdn-fetch]: https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API
[mdn-file]: https://developer.mozilla.org/en-US/docs/Web/API/File
[mdn-file-input]: https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input/file
