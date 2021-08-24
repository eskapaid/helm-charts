### Install validator keystore and password

Validator secret management inspired by https://github.com/fangpenlin/eth2-validator-helm

**WARNING: Please ensure proper access control is used for those Secrets, anyone who has access to these can get your private keypairs from the keystore and withdraw fund from your validators once it's available**

Create a wallet using the official `eth2.0-deposit-cli` and keep your mnemonic stored offline, safely.

To run ETH2 validator in a Kubernete cluster with this Helm chart, you will need to install your keystore into the Kubernete cluster first. By default, two Secrets 

- `eth2-validator-keystore`
- `eth2-validator-password`

are used for keeping the secret data. For example, say you just created a keystore with eth2deposit cli command, and the file structure looks like this

```
+ validator_keys
   - keystore-m_12381_3600_0_0_01610836528.json
   - deposit_data-1610836529.json
```

You can run this command to create the keystore secret

```bash
kubectl create secret generic eth2-validator-keystore \
   --from-file=validator_keys/keystore-m_12381_3600_0_0_0-1610836528.json
```

If you have multiple keystores, you can repeat the `--from-file` argument to specify different files, like this

```bash
kubectl create secret generic eth2-validator-keystore \
  --from-file=validator_keys/keystore-00.json \
  --from-file=validator_keys/keystore-01.json
```

This will create a Kubernete Secret with content like

```
keystore-00.json: <keystore 00 json content>
keystore-01.json: <keystore 00 json content>
```

Since the keypairs in the keystore are encrypted, your validator will need the password to decrypt it, so you also need to install your keystore password. You need to first create the file with your keystore password as its content, and name the file exactly as the public key of your validator. For example, my validator's public key is `0xb8c7cdcaad73437a65125adfc3068bfc011122bac84edca77e9f41c6e6978f2c90579ff3e0170a434e80ba25a42b7e7a`, so I will create a file

```0xb8c7cdcaad73437a65125adfc3068bfc011122bac84edca77e9f41c6e6978f2c90579ff3e0170a434e80ba25a42b7e7a```

with the password as its content. Once you have this file, you can then run

```bash
kubectl create secret generic eth2-validator-password \
   --from-file=0xb8c7cdcaad73437a65125adfc3068bfc011122bac84edca77e9f41c6e6978f2c90579ff3e0170a434e80ba25a42b7e7a
```

This will create a Secret with key values like this:

```
0xb8c7cdcaad73437a65125adfc3068bfc011122bac84edca77e9f41c6e6978f2c90579ff3e0170a434e80ba25a42b7e7a: <password content>
```

Likewise, if you have more than one validator to run, you can apply `--from-file=` multiple times.