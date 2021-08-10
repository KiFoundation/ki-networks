## Gentx file

**Location:** `<node-dir>/kid/config/gentx/` (custom location) or `/home/<user>/.kid/config/gentx/` (default location)
**Format:**

```json

{
  "type": "cosmos-sdk/StdTx",
  "value": {
    "msg": [{
      "type": "cosmos-sdk/MsgCreateValidator",
      "value": {
        "description": {
          "moniker": "interdimensional_node",
          "identity": "",
          "website": "",
          "details": ""
        },
        "commission": {
          "rate": "0.100000000000000000",
          "max_rate": "0.100000000000000000",
          "max_change_rate": "0.100000000000000000"
        },
        "min_self_delegation": "1",
        "delegator_address": "tki1stx50k8pfxyyv45k5ujt2pzlpqqugxs9ck67fp",
        "validator_address": "tkivalconspub1zcjduepqcg0l34ylmj6h63sd5yxlvwusguzjg4el08dakm4jc0q7h85f9e2q4qpq5e",
        "pubkey": "tkivalconspub1zcjduepqcg0l34ylmj6h63sd5yxlvwusguzjg4el08dakm4jc0q7h85f9e2q4qpq5e",
        "value": {
          "denom": "utki",
          "amount": "100000000"
        }
      }
    }],
    "fee": {
      "amount": [],
      "gas": "200000"
    },
    "signatures": [{
      "pub_key": {
        "type": "tendermint/PubKeySecp256k1",
        "value": "A5+nu2RpAECrpXTtwmt71EeAwXgS0pvUPim0ZtI5XQGi"
      },
      "signature": "GcvxVfB3YH2UPlKrsCLwZzvfssTUWe9lfkrT1ZZreUV1UMGdwmxP7z3Z2tDce2nLfolaJTXU+WdpNBjGL0Lbng=="
    }],
    "memo": "9ffaacf087aafd0cac86e5f0d8eae9b95243c5a4@78.31.66.151:26656"
  }
}
```
