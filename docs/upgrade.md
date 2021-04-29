# Upgrade Notes

## From Bitcore 3.0.0 to 4.0.0

`widecore-node@2.1.1` to `widecore-node@3.0.0`

This major upgrade includes changes to indexes, API methods and services. Please review below details before upgrading.

### Indexes

Indexes include *more information* and are now also *faster*. Because of this a **reindex will be necessary** when upgrading as the address and database indexes are now a part of widecoind with three new `widecoin.conf` options:
- `-addressindex`
- `-timestampindex`
- `-spentindex`

To start reindexing add `reindex=1` during the **first startup only**.

### Configuration Options

- The `widecoin.conf` file in will need to be updated to include additional indexes *(see below)*.
- The `datadir` option is now a part of `widecoind` spawn configuration, and there is a new option to connect to multiple widecoind processes (Please see [Bitcoin Service Docs](services/widecoind.md) for more details). The services `db` and `address` are now a part of the `widecoind` service. Here is how to update `bitcore-node.json` configuration options:

**Before**:
```json
{
  "datadir": "/home/<username>/.widecoin",
  "network": "livenet",
  "port": 3001,
  "services": [
    "address",
    "widecoind",
    "db",
    "web"
  ]
}
```

**After**:
```json
{
  "network": "livenet",
  "port": 3001,
  "services": [
    "widecoind",
    "web"
  ],
  "servicesConfig": {
    "widecoind": {
      "spawn": {
        "datadir": "/home/<username>/.widecoin",
        "exec": "/home/<username>/widecore-node/bin/widecoind"
      }
    }
  }
}
```

It will also be necessary to update `widecoin.conf` settings, to include these fields:
```
server=1
whitelist=127.0.0.1
txindex=1
addressindex=1
timestampindex=1
spentindex=1
zmqpubrawtx=tcp://127.0.0.1:<port>
zmqpubhashblock=tcp://127.0.0.1:<port>
rpcallowip=127.0.0.1
rpcuser=<user>
rpcpassword=<password>
```

**Important**: Once changes have been made you'll also need to add the `reindex=1` option **only for the first startup** to regenerate the indexes. Once this is complete you should be able to remove the `widecore-node.db` directory with the old indexes.

### API and Service Changes
- Many API methods that were a part of the `db` and `address` services are now a part of the `widecoind` service. Please see [Bitcoin Service Docs](services/widecoind.md) for more details.
- The `db` and `address` services are deprecated, most of the functionality still exists. Any services that were extending indexes with the `db` service, will need to manage chain state itself, or build the indexes within `widecoind`.
