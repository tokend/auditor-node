# Running auditor node
Running auditor node is as simple as filling one config file and calling a `docker-compose up -d` command.
## Preparing all data
### Platform-dependent data
Request platform administrator the following information:

    - NETWORK_PASSPHRASE   - This is the identificator of the network
    - MASTER_ACCOUNT_ID    - This is the master account of the platform
    - BASE_EXCHANGE_NAME   - This is the human-readable name ot the platform
    - Gateway url          - URL:PORT of the publicly-visible core
    - History buckets' URL - this is the url for downloading history of the platform
    - PUBLIC_KEYs of publicly-visible cores

You should receive something like this (in free format):
```
NETWORK_PASSPHRASE: "Tutorial network!"
MASTER_ACCOUNT_ID:  "GBA4EX43M25UPV4WIE6RRMQOFTWXZZRIPFAI5VPY6Z2ZVVXVWZ6NEOOB"
BASE_EXCHANGE_NAME: "TokenD"
Gateway url: "abd2c72d477b411e984f20aeae5344b9-7951a3850f76c4e7.elb.us-east-1.amazonaws.com:8090"
PUBLIC_KEYS: ["GDEM3RHX6XZVIN23XM3UT44ILBYXP5P4BO6R4MV3US7PEER232RSIXCB", "GBC3SSF7AS4J3SU3FYKXZUKNMAMUOIROGP3ZNY4WJL4WJBFVU66FFEJO"]
History buckets' URL: "https://buckets.tutorial.tokend.io"
```

### Generate
Generate `NODE_SEED` by running following command:
```
docker run --rm --entrypoint=core tokend/core:3.2.1 --genseed
```
You should see the following output:
```
Using DEPRECATED command-line syntax.
Please refer to documentation for new syntax.

Secret seed: SAXK2YCEHV3DLLFQ2N4NHC36C4VISRFIRM4WAX475SHX6WFUFZ373K2X
Public: GAO5AAU4QK2BJAMLAD6EBR4X2KVFV54U6M3CV6USSLLE76ITCEA5V6VB
```
Save secret seed (the `S...` part).

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
where PUBLIC_KEYs are the NODES_PUBLIC_KEYS, and `alpha`, `bravo` are aliases you created to reference this keys.
___

Set `PREFERRED_PEERS` to the array of publicly-visible cores' urls as follows:
```
PREFERRED_PEERS=[
  "cve2c72d876b411e984f20aeae5344b9-7951a3850f76c4e7.elb.us-east-1.amazonaws.com:8090",
  "abd2c72d477b411e984f20aeae5344b9-7951a3850f76c4e7.elb.us-east-1.amazonaws.com:8090"]
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

Set the url in `[HISTORY.src]` section to the History bucket's URL:
```
[HISTORY.src]
get="curl -sf https://buckets.tutorial.tokend.io/{0} -o {1}"
```
Note the slash between the url and `{0}`.
___
*All the other configs in the core.ini file mustn't be changed.*

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
