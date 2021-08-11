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
          "moniker": "Hugerang",
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
        "delegator_address": "tki1zytvz2z478ed3vh6aqnhldx8wja9cq5pc2ras8",
        "validator_address": "tkivaloper1zytvz2z478ed3vh6aqnhldx8wja9cq5pt2t70y",
        "pubkey": "tkivalconspub1zcjduepqw06gwljrduxy8easxnp3mpumawtzx4rrrfykx76hg4yyg7xyrkus44zt9c",
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
        "value": "AgFneWnvYh55qkhyAL4xRiJIHWbGkh4j7wlMKdLmU1p2"
      },
      "signature": "DnTmPxPEwixfQ+Wk8dXl20YCr+X0kiOMOAQFDCyUNUESk0t0jXUsJS11ygE5/TY7crNWHwf0/FD4CAodtMrtlw=="
    }],
    "memo": "d73b0dd8933f0f8f2d64955993a6a58032bf70c7@173.249.32.186:26656"
  }
}

```
