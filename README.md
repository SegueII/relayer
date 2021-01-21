# Relayer

**编译最新 gaia 和 iris**

**启动一条 gaia 链和一条 iris 链**

```bash
./scripts/bifrost
```

**配置 relayer**

```bash
make install

rly config init
rly config add-dir configs/bifrost/

rly keys restore iris rlykey "$(jq -r '.mnemonic' data/iris/key_seed.json)"
rly keys restore cosmos rlykey "$(jq -r '.mnemonic' data/cosmos/key_seed.json)"

rly light init iris -f
rly light init cosmos -f
```

**查看连接信息**

```bash
tree ~/.relayer
rly chains list
rly paths list
```

**建立连接**

```bash
rly tx link bifrost -d -o 3s
```

**查看账户余额**

```bash
# 使用 relayer 查询 （存在跨链资产时报错）
rly q balance iris
rly q balance cosmos
```

```bash
# 链上查询
iris q bank balances $(rly keys show iris) --node tcp://localhost:26657
gaiad q bank balances $(rly keys show cosmos) --node tcp://localhost:26557
```

**启动 relayer**

```bash
# 自动 relay
rly start bifrost
```

```bash
# 手动 relay
rly tx relay bifrost -d
```

**通过 relayer 转账**

```bash
rly tx transfer iris cosmos 1000000samoleans $(rly chains address cosmos)
rly tx transfer cosmos iris 1000000ibc/27A6394C3F9FF9C9DCF5DFFADF9BB5FE9A37C7E92B006199894CF1824DF9AC7C $(rly chains address iris)
```

**通过 cli 转账**

```bash

iris tx ibc-transfer transfer transfer channel-0 $(rly keys show cosmos) 1000000samoleans \
    --absolute-timeouts \
    --packet-timeout-timestamp 0 \
    --from user \
    --home data/iris \
    --keyring-backend test \
    --node tcp://localhost:26657 \
    --broadcast-mode block \
    --chain-id iris \
    -y

gaiad tx ibc-transfer transfer transfer channel-0 $(rly keys show iris) 1000000ibc/27A6394C3F9FF9C9DCF5DFFADF9BB5FE9A37C7E92B006199894CF1824DF9AC7C \
    --absolute-timeouts \
    --packet-timeout-timestamp 0 \
    --from user \
    --home data/cosmos \
    --keyring-backend test \
    --chain-id cosmos \
    --node tcp://localhost:26557 \
    --broadcast-mode block \
    -y
```
