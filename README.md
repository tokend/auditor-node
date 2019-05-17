# Running auditor node
Running auditor node is as simple as filling one config file and calling a `docker-compose up -d` command.
## Preparing all data
### Platform-dependent data
Request platform administrator the following information:

    - NETWORK_PASSPHRASE  - This is the identificator of the network
    - MASTER_ACCOUNT_ID   - This is the master account of the platform
    - BASE_EXCHANGE_NAME  - This is the human-readable name of the platform
    - PREFERRED_PEERS     - Array of strings "URL:PORT" of the publicly-visible cores
    - NODE_NAMES          - Array of strings "PUBLIC_KEY alias" of publicly-visible cores
    - HISTORY_GET_COMMAND - this is the history get command for downloading history of the platform

You should receive something like this (in free format):
```
NETWORK_PASSPHRASE: "Tutorial network!"
MASTER_ACCOUNT_ID:  "GBA4EX43M25UPV4WIE6RRMQOFTWXZZRIPFAI5VPY6Z2ZVVXVWZ6NEOOB"
BASE_EXCHANGE_NAME: "TokenD"
PREFERRED_PEERS: ["core-alpha.tutorial.tokend.io:8090", "core-bravo.tutorial.tokend.io:8090"]
NODE_NAMES: ["GDEM3RHX6XZVIN23XM3UT44ILBYXP5P4BO6R4MV3US7PEER232RSIXCB alpha", "GBC3SSF7AS4J3SU3FYKXZUKNMAMUOIROGP3ZNY4WJL4WJBFVU66FFEJO bravo"]
HISTORY_GET_COMMAND: "curl -sf https://history.tutorial.tokend.io/{0} -o {1}"
```

### Generate
Generate `NODE_SEED` by running following command:
```
docker run --rm --entrypoint=core tokend/core:3.3.1 gen-seed
```
You should see the following output:
```
Secret seed: SAXK2YCEHV3DLLFQ2N4NHC36C4VISRFIRM4WAX475SHX6WFUFZ373K2X
Public: GAO5AAU4QK2BJAMLAD6EBR4X2KVFV54U6M3CV6USSLLE76ITCEA5V6VB
```
Save secret seed, you'll need it later.

## Configuring
Please open the configs/core.ini file in the editor of your choice and tune its content following provided instructions.

Set `NODE_SEED` to the value you've generated + " self":
```
NODE_SEED="SAXK2YCEHV3DLLFQ2N4NHC36C4VISRFIRM4WAX475SHX6WFUFZ373K2X self"
```
___

Set `NETWORK_PASSPHRASE`, `MASTER_ACCOUNT_ID`, `BASE_EXCHANGE_NAME` to the values you've got from platform administrator:
```
NETWORK_PASSPHRASE="Tutorial network!"
MASTER_ACCOUNT_ID="GBA4EX43M25UPV4WIE6RRMQOFTWXZZRIPFAI5VPY6Z2ZVVXVWZ6NEOOB"
BASE_EXCHANGE_NAME="TokenD"
```
___

Set `NODE_NAMES` to the array as follows:
```
NODE_NAMES=[
 "GDEM3RHX6XZVIN23XM3UT44ILBYXP5P4BO6R4MV3US7PEER232RSIXCB alpha",
 "GBC3SSF7AS4J3SU3FYKXZUKNMAMUOIROGP3ZNY4WJL4WJBFVU66FFEJO bravo"]
```
___

Set `PREFERRED_PEERS` to the array of publicly-visible cores' urls as follows:
```
PREFERRED_PEERS=[
  "core-alpha.tutorial.tokend.io:8090",
  "core-bravo.tutorial.tokend.io:8090"]
```
___

Populate `VALIDATORS` array of the `[QUORUM_SET]` section with aliases from NODE_NAMES array, and also add "$self" to the list:
```
[QUORUM_SET]
THRESHOLD_PERCENT=51
VALIDATORS=[
    "$self",
    "$alpha",
    "$bravo",
]
```
Note the leading `$`.
___

Set the get command in `[HISTORY.src]` section to the HISTORY_GET_COMMAND:
```
[HISTORY.src]
get="curl -sf https://history.tutorial.tokend.io/{0} -o {1}"
```
___
*All the other configs in the core.ini file must not be changed.*

## Running
When configs are filled with correct values running the node is quite easy.
Go to the directory with docker-compose.yaml file and run:
```
docker-compose up -d
```

## Removing
To remove node just run in the directory with docker-compose.yaml:
```
docker-compose down -v
```
