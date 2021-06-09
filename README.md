# Relayer

**编译最新 gaia 和 iris**

**启动一条 gaia 链和一条 iris 链**

```bash
./scripts/bifrost
```

**配置 relayer**

```bash
rm -rf ~/.relayer
rly config init
rly config add-chains configs/bifrost/
rly config add-paths configs/path/

rly keys restore irishub rlykey "$(jq -r '.mnemonic' data/irishub/key_seed.json)"
rly keys restore cosmoshub rlykey "$(jq -r '.mnemonic' data/cosmoshub/key_seed.json)"

rly light init irishub -f
rly light init cosmoshub -f
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

rly q clients irishub
rly q clients cosmoshub

iris q ibc client states --node tcp://localhost:26657 -o json
gaiad q ibc client states --node tcp://localhost:26557 -o json --log_level info

**查看账户余额**

```bash
# 使用 relayer 查询
rly q balance irishub
rly q balance cosmoshub
```

```bash
# 链上查询
iris q bank balances $(rly keys show irishub) --node tcp://localhost:26657
gaiad q bank balances $(rly keys show cosmoshub) --node tcp://localhost:26557 --log_level info
```

**启动 relayer**

```bash
# 自动 relay
rly start bifrost
```

```bash
# 手动 relay
rly tx relay-packets bifrost -d
```

**通过 relayer 转账**

```bash
rly tx transfer irishub cosmoshub 1000000samoleans $(rly chains address cosmoshub)
rly tx transfer cosmoshub irishub 1000000ibc/27A6394C3F9FF9C9DCF5DFFADF9BB5FE9A37C7E92B006199894CF1824DF9AC7C $(rly chains address irishub)
```

**通过 cli 转账**

```bash

iris tx ibc-transfer transfer transfer channel-0 $(rly keys show cosmos) 1000000samoleans \
    --from user \
    --home data/iris \
    --keyring-backend test \
    --node tcp://localhost:26657 \
    --broadcast-mode block \
    --chain-id iris \
    -y

gaiad tx ibc-transfer transfer transfer channel-0 $(rly keys show iris) 1000000ibc/27A6394C3F9FF9C9DCF5DFFADF9BB5FE9A37C7E92B006199894CF1824DF9AC7C \
    --from user \
    --home data/cosmos \
    --keyring-backend test \
    --chain-id cosmos \
    --node tcp://localhost:26557 \
    --broadcast-mode block \
    -y
```
