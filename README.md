1\) качаем всякое этим скриптом:
``` bash
curl -sSL http://bit.ly/2ysbOFE | bash -s --
```
По умолчанию он качает все. Образы, сэмплы (одним из которых я и воспользуюсь), бинарники. 
А поскольку Я не доверяю скриптам, скачал заранее и прочитал.<br />
<br />
2\) Заэкспортил каталог в переменную PATH

``` bash
export PATH=/home/pavel/workspace/myfabric/fabric-samples/bin:$PATH
```
3\) Перешел в каталог с тестовой сетью и контрактом
``` bash
cd ./fabric-samples/first-network/
``` 
4\) Дал разрешение на выполнение скрипта, запускающего сеть
``` bash
chmod +x ./byfn.sh/
```
5\) Сгенерировал первый блок реестра
``` bash
./byfn.sh generate -c testchan
```
<details>
    <summary>log</summary>
        
```
pavel@Pavel-PC:~/workspace/myfabric/fabric-samples/first-network$ ./byfn.sh generate -c testchan
Generating certs and genesis block for channel 'testchan' with CLI timeout of '10' seconds and CLI delay of '3' seconds
Continue? [Y/n] y
proceeding ...
/home/pavel/workspace/myfabric/fabric-samples/first-network/../bin/cryptogen

##########################################################
##### Generate certificates using cryptogen tool #########
##########################################################
+ cryptogen generate --config=./crypto-config.yaml
org1.example.com
org2.example.com
+ res=0
+ set +x

Generate CCP files for Org1 and Org2
/home/pavel/workspace/myfabric/fabric-samples/first-network/../bin/configtxgen
##########################################################
#########  Generating Orderer Genesis block ##############
##########################################################
2020-02-16 20:14:19.780 MSK [common.tools.configtxgen] main -> INFO 001 Loading configuration
2020-02-16 20:14:19.817 MSK [common.tools.configtxgen.localconfig] completeInitialization -> INFO 002 orderer type: etcdraft
2020-02-16 20:14:19.817 MSK [common.tools.configtxgen.localconfig] completeInitialization -> INFO 003 Orderer.EtcdRaft.Options unset, setting to tick_interval:"500ms" election_tick:10 heartbeat_tick:1 max_inflight_blocks:5 snapshot_interval_size:16777216 
2020-02-16 20:14:19.817 MSK [common.tools.configtxgen.localconfig] Load -> INFO 004 Loaded configuration: /home/pavel/workspace/myfabric/fabric-samples/first-network/configtx.yaml
2020-02-16 20:14:19.819 MSK [common.tools.configtxgen] doOutputBlock -> INFO 005 Generating genesis block
2020-02-16 20:14:19.820 MSK [common.tools.configtxgen] doOutputBlock -> INFO 006 Writing genesis block

#################################################################
### Generating channel configuration transaction 'channel.tx' ###
#################################################################
+ configtxgen -profile TwoOrgsChannel -outputCreateChannelTx ./channel-artifacts/channel.tx -channelID testchan
2020-02-16 20:14:19.853 MSK [common.tools.configtxgen] main -> INFO 001 Loading configuration
2020-02-16 20:14:19.885 MSK [common.tools.configtxgen.localconfig] Load -> INFO 002 Loaded configuration: /home/pavel/workspace/myfabric/fabric-samples/first-network/configtx.yaml
2020-02-16 20:14:19.885 MSK [common.tools.configtxgen] doOutputChannelCreateTx -> INFO 003 Generating new channel configtx
2020-02-16 20:14:19.887 MSK [common.tools.configtxgen] doOutputChannelCreateTx -> INFO 004 Writing new channel tx
+ res=0
+ set +x

#################################################################
#######    Generating anchor peer update for Org1MSP   ##########
#################################################################
+ configtxgen -profile TwoOrgsChannel -outputAnchorPeersUpdate ./channel-artifacts/Org1MSPanchors.tx -channelID testchan -asOrg Org1MSP
2020-02-16 20:14:19.919 MSK [common.tools.configtxgen] main -> INFO 001 Loading configuration
2020-02-16 20:14:19.956 MSK [common.tools.configtxgen.localconfig] Load -> INFO 002 Loaded configuration: /home/pavel/workspace/myfabric/fabric-samples/first-network/configtx.yaml
2020-02-16 20:14:19.956 MSK [common.tools.configtxgen] doOutputAnchorPeersUpdate -> INFO 003 Generating anchor peer update
2020-02-16 20:14:19.961 MSK [common.tools.configtxgen] doOutputAnchorPeersUpdate -> INFO 004 Writing anchor peer update
+ res=0
+ set +x

#################################################################
#######    Generating anchor peer update for Org2MSP   ##########
#################################################################
+ configtxgen -profile TwoOrgsChannel -outputAnchorPeersUpdate ./channel-artifacts/Org2MSPanchors.tx -channelID testchan -asOrg Org2MSP
2020-02-16 20:14:19.999 MSK [common.tools.configtxgen] main -> INFO 001 Loading configuration
2020-02-16 20:14:20.036 MSK [common.tools.configtxgen.localconfig] Load -> INFO 002 Loaded configuration: /home/pavel/workspace/myfabric/fabric-samples/first-network/configtx.yaml
2020-02-16 20:14:20.036 MSK [common.tools.configtxgen] doOutputAnchorPeersUpdate -> INFO 003 Generating anchor peer update
2020-02-16 20:14:20.037 MSK [common.tools.configtxgen] doOutputAnchorPeersUpdate -> INFO 004 Writing anchor peer update
+ res=0
+ set +x
```

</details>

6\) В качестве очереди попробовал воспользоваться кафкой - неудачно. Видимо тестовый пример не предусматривает очередей. В качестве БД - диван (CouchDB), но можно присобачить MongoDB. [Да, можно](https://jira.hyperledger.org/browse/FAB-6263)

---
``` bash
./byfn.sh up -c testchan -s couchdb -o kafka
```
<details>
    <summary>log</summary>

```
pavel@Pavel-PC:~/workspace/myfabric/fabric-samples/first-network$ ./byfn.sh up -c testchan -s couchdb -o kafka
./byfn.sh: illegal option -- o
Usage: 
byfn.sh <mode> [-c <channel name>] [-t <timeout>] [-d <delay>] [-f <docker-compose-file>] [-s <dbtype>] [-l <language>] [-o <consensus-type>] [-i <imagetag>] [-a] [-n] [-v]
```

</details>

---
7\) Без кафки запустилось нормально
``` bash
./byfn.sh up -c testchan -s couchdb
```
8\) Я посмотрел на выполнение тестового контракта... И вырубил сеть
``` bash
./byfn.sh down
```
## А что же происходило в эти моменты под капотом??
* Пункт 5 генерируем Genesis Block
``` bash
cryptogen generate --config=./crypto-config.yaml
```
Что у нас там, в файле **crypto-config.yaml**
Просто описание организации, контракты выдающей (OrdererOrgs), и организаций, контракты выполняющие (PeerOrgs)
<br />
Далее идет генерация Genesis Block на основе **configtx.yaml**, в которой описаны учасники контракта (Section: Organizations), версия сервера (или канала) (SECTION: Capabilities).
Секция приложение(SECTION: Application) - все, что связанно с приложениями. Секция заказчика (SECTION: Orderer) является конфигурацией подключения к message queue/brocker. В данном примере используется etcd/raft -  библиотека для распределенных систем обеспечивающая их нормальное функционирование. Канал (CHANNEL) тут представлена настройка для каналов. Профиль (Profile) содержит переменные, необходимые для специфических параметров configtxgen
<br />
Далее создаем конфигурацию для транзакций в канале, затем создаем обновля2м 3 "Сервера" для приема сообщений от клиентов сети (peer)
<br />
Ура! Все артефакты созданы, можно поднимать!
см. пункт 7 <br />
До надписи старт мы долго нудно качали пакеты на GoLang`е и контейнеры
Подключились клиенты сети (2 штуки, 4 коннекта)
```
peer channel join -b testchan.block
```
На каждого из клиентов устанавливается кусок кода, т.н. chaicode, который и является смарт-контрактом и тянется [отсюда](https://github.com/hyperledger/fabric-samples/chaincode/abstore/go/)<br />
Проверяем, были ли подверждены контракты в каналах
```
Checking the commit readiness of the chaincode definition successful on peer0.org1 on channel 'testchan'
```
Далее, инициаизируем баланс на двух переменных
```
'{"Args":["Init","a","100","b","100"]}'
```
Пробуем предать 10 единиц от переменной a к переменой b
```
'{"Args":["invoke","a","b","10"]}'
```
Просим баланс a у клиента сети peer0.org1
```
'{"Args":["query","a"]}'
90

Query successful on peer0.org1 on channel 'testchan'
```

Просим баланс a у клиента сети peer1.org2
```
'{"Args":["query","a"]}'
90

Query successful on peer1.org2 on channel 'testchan'
```

Все! Тест завершен! <br />
Логи лежат в файле [logs.logs](logs.logs)<br />
В файле [docker-compose.yml](docker-compose.yml) находится прототип compose файла<br />
В файле [.env](.env) храняться переменные для compose файла докера<br />
Скрипт [templates_app.yaml](templates_app.yaml) для Ansible предполагался как удобное средство для "выпекания" конфигов из шаблонов jijnga2 : <br />-- [configtx.yaml.j2](templates/configtx.yaml.j2) <br />-- [core.yaml.j2](templates/core.yaml.j2)<br />
Да, согласен. Сдеано мало. Но запилить что-то не в офисе мне трудно :( (Жена с двухгодовалым ребенком не добавляют свободного времени)