---
sidebar_position: 5
---

# Replication

Replication allows you to store data in multiple locations for backup or performance reasons. Replication is also how traditional client/server experiences can be built with Fireproof.

If you are just getting started [try the PartyKit connector for multi-user development](https://www.npmjs.com/package/@fireproof/partykit) — the README should be enough to get your users connected.

Fireproof's replication can use WebSocket, S3, IPFS, HTTP, and more, which enables the efficient and reliable replication of data across multiple nodes on the network. Each node on the network maintains its own copy of the ledger and its clock. When a new update is made to the ledger, a new CID is generated and added to the local clock. The CID is then shared with other devices in the group, which validate the update and applies it to their local copy of the ledger.

This replication mechanism allows for storage in a PartyKit instance, an S3 bucket, basic REST server, or IPFS-compatible block store, such as web3.storage, or any Fireproof instance running in another browser or a cloud environment. In addition, Fireproof enables serving ledger snapshots from static asset servers, allowing for fast loading of game levels, web pages, and other content.

To learn more about Fireproof's storage and sync capabilities, read the [Storage, Sync and Sharing Model](/docs/concept-guide/storage-sync-and-sharing-model) section of the Concept Guide.

## Configuring Replication

Import the `connect` module to configure replication, create a ledger, and connect it to a service:

```js
import { fireproof, connect } from 'use-fireproof'

const ledger = fireproof('my-ledger')
const connection = new connect.raw({ upload, download })
const connection = connect(ledger, connection)
```

Raw connection config a simple object with upload and download fields:

```js
const mockStore = new Map()
const mockConnect = {
  // eslint-disable-next-line @typescript-eslint/require-await
  upload: async function (bytes, { type, name, car, branch }) {
    const key = new URLSearchParams({ type, name, car, branch }).toString()
    mockStore.set(key, bytes)
  },
  // eslint-disable-next-line @typescript-eslint/require-await
  download: async function ({ type, name, car, branch }) {
    const key = new URLSearchParams({ type, name, car, branch }).toString()
    return mockStore.get(key)
  }
}
```

This code is from the [connection tests](https://github.com/fireproof-storage/fireproof/blob/main/packages/fireproof/src/connect.ts), you can use it as inspiration, writing a rest client or other connector.

### S3 Connection

You can [see an application test case using the S3 connection here](https://fireproof.storage/s3up-test.html?list=replication). Share your list with a friend. [The bucket it is using is public, so any valid CAR file can be uploaded, and the metadata is inline and world readable.](https://github.com/fireproof-storage/fireproof/blob/main/packages/fireproof/test/www/todo.html#L19) Don't rely on it for anything other than testing, it will be cleared occasionally. In production, you'll want to use a custom connection to move the metadata to a permissioned store. If you want to [modify the behavior of the S3 store to add Cognito, Lambda and DynamoDB for this purpose, the source is available here](https://github.com/fireproof-storage/valid-cid-s3-bucket/blob/cars/getSignedURL/app.ts#L101).

### Web3 Connection

[web3.storage](https://web3.storage) is an IPFS upload API that uses self-sovereign [UCAN](https://ucan.xyz) authorization. Currently it's the recommended option if you want your users to be able to store to the cloud, and link to them from the web. Read about the web3.storage connection [here](/docs/connect).

### PartyKit Connection

[PartyKit](https://partykit.io) is a realtime program host for collaborative applications. It is a great option if you want to build a realtime application with Fireproof. Read about the PartyKit connection [here](/docs/connect).
