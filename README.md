# Relayer

**编译最新 gaia 和 iris**

**启动一条 gaia 链和一条 iris 链**

```bash
./scripts/two-chainz
```

**查看连接信息**

```bash
tree ~/.relayer
rly chains list
rly paths list
```

**建立连接**

```bash
rly tx link demo -d -o 3s
```

**查看账户余额**

```bash
# 使用 relayer 查询 （存在跨链资产时报错）
rly q balance atom
rly q bal iris
```

```bash
# 链上查询
gaiad q bank balances $(rly keys show atom) --node tcp://localhost:26657
iris q bank balances $(rly keys show iris) --node tcp://localhost:26557
```

**启动 relayer**

```bash
# 自动 relay
rly start demo
```

```bash
# 手动 relay
rly tx relay demo -d
```

**通过 relayer 转账**

```bash
rly tx transfer atom iris 1000000samoleans $(rly chains address iris)
rly tx transfer iris atom 1000000ibc/27A6394C3F9FF9C9DCF5DFFADF9BB5FE9A37C7E92B006199894CF1824DF9AC7C $(rly chains address atom)
```

**通过 cli 转账**

```bash
gaiad tx ibc-transfer transfer transfer channel-0 $(rly keys show iris) 1000000samoleans \
    --absolute-timeouts \
    --packet-timeout-timestamp 0 \
    --from user \
    --home data/atom \
    --keyring-backend test \
    --chain-id atom \
    --node tcp://localhost:26657 \
    --broadcast-mode block \
    -y

iris tx ibc-transfer transfer transfer channel-0 $(rly keys show atom) 1000000ibc/27A6394C3F9FF9C9DCF5DFFADF9BB5FE9A37C7E92B006199894CF1824DF9AC7C \
    --absolute-timeouts \
    --packet-timeout-timestamp 0 \
    --from user \
    --home data/iris \
    --keyring-backend test \
    --node tcp://localhost:26557 \
    --broadcast-mode block \
    --chain-id iris \
    -y
```
