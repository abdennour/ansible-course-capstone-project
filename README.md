# Databasee

## Install:

**Install MongoDB**

```sh
# Ubuntu 18.04 LTS
sudo apt-get install gnupg

wget -qO - https://www.mongodb.org/static/pgp/server-4.2.asc | sudo apt-key add -

echo "deb http://repo.mongodb.org/apt/debian buster/mongodb-org/4.2 main" | sudo tee /etc/apt/sources.list.d/mongodb.list


sudo apt install mongodb-org

sudo systemctl enable mongod
sudo systemctl start mongod 

# whitelist IPs : https://www.digitalocean.com/community/tutorials/how-to-install-mongodb-on-ubuntu-18-04

# Validate: mongo --eval 'db.runCommand({ connectionStatus: 1})'
```

## Configure DB
**Admin config**

```sh
#1) disable security (/etc/mongod.conf) --> Restart Mongodb
#> security:
#>   authorization: "disabled"

#2) drop admin user if exist
mongo admin --eval 'db.dropUser("superadmin")'
#3) create admin user
mongo admin --eval 'db.createUser( { user: "superadmin", pwd: "MyPass1234", roles: [ { role: "clusterAdmin", db: "admin" }, { role: "userAdminAnyDatabase", db: "admin" } ] } )'
#4) enable security (/etc/mongod.conf) --> Restart Mongodb
#> security:
#>   authorization: "enabled"
```
**App user config**

```sh
mongo admin --eval 'db.createUser( { user: "todo", pwd: "todo", roles: [ { role: "readWrite", db: "test" } ] } )'
```
**Network config**

- Replace the value of bindIp in /etc/mongod.conf
```sh
sed -i 's/127.0.0.1/0.0.0.0/g' /etc/mongod.conf
```


# Backend

## Prebuild:

- Install go

## Build
- Checkout source code
- Run `go build`

## Run Manually

**Check connectivity with DB**

```sh
nc -zv <db-hostname> 27017
```
**From source code**

```sh
export DB_CONNECTION=mongodb://user:pass@localhost:27017 \
       DB_NAME=test
go run main.go
```

**Or From build artifact**

```sh
export DB_CONNECTION=mongodb://user:pass@localhost:27017 \
       DB_NAME=test
/artifact-build-file
```

## Run Production grade

1 - Create Systemd Service Todo which populates (2) & manages (3)
      -> notify: `systemctl reload-daemon`

2 - Put env vars in seperated file ( `/etc/todo.env`)

3 - Move build file from `/tmp/todo` to a standard place (`/usr/local/bin/todo`)
      -> notify: `systemctl restart todo`

## Deploy

- systemd service
