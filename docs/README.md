---
title: Welcome to Web3.Storage
description: "Web3.Storage lets you store stuff on the decentralize internet without having to mess around or learn complex stuff."
---

# Welcome to Web3.Storage

Web3.Storage lets you store stuff on the decentralize internet without having to mess around or learn complex stuff.

## Quickstart

Get up and running quickly by following these simple steps!

### Create an account

You need an account to get an API key and manage your stored data. You can sign up using your email address, or GitHub Sign up is free:

#### Use your email

1. Go to [web3.storage/login](https://web3.storage/login)
1. Enter your email address.
1. Verify your email address by clicking the **Log in** button in your email inbox.
1. You're all set!

Next up, [getting an API key ↓](#get-an-api-key)

#### Use GitHub

1. Click **GitHub** on the Login screen
1. Authorize web3-storage when asked by GitHub.

![GitHub asking to authorize the Web3.Storage projet to a user account](./images/github-authorization-process.png)

1. You're all set!

Next up, [getting an API key ↓](#get-an-api-key)

### Get an API key

Now that you've got your account set up, you can create an API key. You'll need an API key to interact with Web3.Storage using the JavaScript client library:

1. Go to the [tokens page](https://web3.storage/tokens) by clicking **API Tokens** → **New Token**.
1. Enter a descriptive name for this token:

    ![Web3.Storage API key creation screen.](./images/name-an-api-key.png)

1. Click **Create**.
1. Make a note of the **Key**. Click **Copy** to copy the API key to your clipboard.

    :::warning Keep your API key private
    Do not share your API key with anyone else. This key is specific to your account.
    :::

Next up, [uploading a file to Web3.Storage ↓](#upload-a-file)

### Grab Web3.Storage client project

1. Create a folder for your Quickstart project and move into it:

    ```shell
    mkdir web3-storage-quickstart
    cd web3-storage-quickstart
    ```

1. Create a file called `put-files.js` and paste in the following code:

    ```javascript
    import process from 'process'
    import minimist from 'minimist'
    import { Web3Storage, getFilesFromPath } from 'web3.storage'

    async function main () {
        const args = minimist(process.argv.slice(2))
            const token = args.token

            if (!token) {
                console.error('A token is needed. You can create one on https://web3.storage')
                    return
            }

        const storage = new Web3Storage({ token })

            const files = await getFilesFromPath('./fixtures')
            const cid = await storage.put(files)

            console.log('Content added with CID:', cid)
    }

    main()
    ```

1. Create another file called `package.json` and paste in the following code:

    ```javascript
    {
        "name": "web3-storage-examples",
            "version": "0.0.0",
            "private": true,
            "description": "Examples of using web3.storage in Node.js",
            "type": "module",
            "scripts": {
                "test": "echo \"Error: no test specified\" && exit 1"
            },
            "dependencies": {
                "minimist": "^1.2.5",
                "web3.storage": "^1.0.0"
            },
            "author": "Vasco Santos",
            "license": "(Apache-2.0 AND MIT)"
    }
    ```

1. Save both files, and then run `npm install` from your project folder:

    ```shell
    npm install
    ```

1. Run the script by calling `node put-files.js` and supplying your token using the `--token` option:

    ```shell
    node put-files.js --token<YOUR_TOKEN>
    ```

    This command will output something like:

    ```shell
    Content added with CID: bafybeiezmummmxc3xclgsnhbkz2vh42cakbccyvatqqdw6hz2tvt74pd3i
    ```

1. Make a note of the CID `bafyb...`. We'll need it in the next section.

Next up, we'll look at how to [get and view your data from Web3.Storage ↓](#view-file)

### View your file

Viewing your files is simple and can be done using a browser gateway:

1. Go to `gateway.web3.storage/YOUR_FILES_CID`, replacing `YOUR_FILES_CID` with the CID you get from uploading your file.
1. You should be able to see your file in the browser!

## Next steps

Congratulations, you've just covered the basics of Web3.Storage! Take a look at the [reference API section](/reference) for more details on what else you can do with this service.
