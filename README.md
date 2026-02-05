# eos-ansible


## QuarkDB restart

```shell
systemctl stop eos@qdb

rm -rf /var/lib/qdb

chown -R daemon:daemon /var/log/xrootd
chown -R daemon:daemon /var/eos/
chown -R daemon:daemon /etc/eos.* # + must have permissions 400 !
chown -R daemon:daemon /var/run/xrootd
chown -R daemon:daemon /var/lib/qdb
chown -R daemon:daemon /var/spool/xrootd

UUID=eosdev-$(uuidgen); echo $UUID; sudo runuser root -s /bin/bash -c "quarkdb-create --path /var/lib/qdb/ --clusterID $UUID --nodes $(hostname):7777"

chown -R daemon:daemon /var/lib/qdb

systemctl restart eos@qdb

tail -f /var/log/eos/qdb/xrdlog.qdb
```

## MGM

```shell
systemctl restart eos@mgm

tail -f /var/log/eos/mgm/xrdlog.mgm
```

## Stop all

```shell
systemctl stop eos@qdb
systemctl stop eos@mgm
systemctl stop eos@fst.1
systemctl stop eos@fst.2
systemctl stop eos@fst.3
```

```shell
systemctl restart eos@qdb
systemctl restart eos@mgm
systemctl restart eos@fst.1
systemctl restart eos@fst.2
systemctl restart eos@fst.3
```

## FST

```shell
eos space define default 6 24
eos space set default on
eos space status default
```

```shell
FST_NAME=fst.3
# rm -rf /data/$FST_NAME
mkdir -p /data/$FST_NAME
mkdir -p /data/$FST_NAME/01
mkdir -p /data/$FST_NAME/02
mkdir -p /data/$FST_NAME/03
mkdir -p /data/$FST_NAME/04
mkdir -p /data/$FST_NAME/05
chown -R daemon:daemon /data/$FST_NAME
```

```shell
# this needs more setup in order to work, create metadata files etc.
# eos fs add $(uuidgen) lobisapa-dev-al9.cern.ch:1191 /data/fst.1/01 default.1 rw

# we move the file systems to groups that would not be picked up by the script, then we move them back to the correct group otherwise script fails (is there a better way?)

# fst.1

FST_NAME=fst.1
FST_PORT=1191

eosfstregister -i -r localhost /data/$FST_NAME/ default:5 --port $FST_PORT

TARGET_GROUP=default.6
eos fs mv --force 1 $TARGET_GROUP
eos fs mv --force 2 $TARGET_GROUP
eos fs mv --force 3 $TARGET_GROUP
eos fs mv --force 4 $TARGET_GROUP
eos fs mv --force 5 $TARGET_GROUP

# fst.2

FST_NAME=fst.2
FST_PORT=1192

eosfstregister -i -r localhost /data/$FST_NAME/ default:5 --port $FST_PORT

TARGET_GROUP=default.7
eos fs mv --force 6 $TARGET_GROUP
eos fs mv --force 7 $TARGET_GROUP
eos fs mv --force 8 $TARGET_GROUP
eos fs mv --force 9 $TARGET_GROUP
eos fs mv --force 10 $TARGET_GROUP

# fst.3

FST_NAME=fst.3
FST_PORT=1193

eosfstregister -i -r localhost /data/$FST_NAME/ default:5 --port $FST_PORT

TARGET_GROUP=default.8
eos fs mv --force 11 $TARGET_GROUP
eos fs mv --force 12 $TARGET_GROUP
eos fs mv --force 13 $TARGET_GROUP
eos fs mv --force 14 $TARGET_GROUP
eos fs mv --force 15 $TARGET_GROUP

# move all fs to the proper groups

TARGET_GROUP=default.1
eos fs mv --force 1 $TARGET_GROUP
eos fs mv --force 2 $TARGET_GROUP
eos fs mv --force 3 $TARGET_GROUP
eos fs mv --force 4 $TARGET_GROUP
eos fs mv --force 5 $TARGET_GROUP
TARGET_GROUP=default.2
eos fs mv --force 6 $TARGET_GROUP
eos fs mv --force 7 $TARGET_GROUP
eos fs mv --force 8 $TARGET_GROUP
eos fs mv --force 9 $TARGET_GROUP
eos fs mv --force 10 $TARGET_GROUP
TARGET_GROUP=default.3
eos fs mv --force 11 $TARGET_GROUP
eos fs mv --force 12 $TARGET_GROUP
eos fs mv --force 13 $TARGET_GROUP
eos fs mv --force 14 $TARGET_GROUP
eos fs mv --force 15 $TARGET_GROUP

eos group set default.1 on
eos group set default.2 on
eos group set default.3 on
eos group ls

```