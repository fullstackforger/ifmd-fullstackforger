# Installing MongoDB with MacPorts on OS-X 

This is step by step guide on how to locally set up MongoDB on OS-X

## Install mongodb
```
sudo port install mongodb
```

## Create directories
```
# Make data directory
sudo mkdir -p /opt/local/var/db/mongodb_data
# Make logs directory
sudo mkdir -p /opt/local/var/log/mongodb
# Make config directory
sudo mkdir -p /opt/local/etc/mongodb
```

## Create configuration file
```
sudo vim /opt/local/etc/mongodb/mongod.conf
```

Enter the following if the file is blank and hit ctrl+x and save the file.

```
# configuration file /opt/local/etc/mongodb/mongod.conf

# Store data alongside MongoDB instead of the default, /data/db/
dbpath = /opt/local/var/db/mongodb_data

# Only accept local connections
 bind_ip = 127.0.0.1

# Running as daemon
fork = true

# Take log
logpath = /opt/local/var/log/mongodb/mongodb.log
logappend = true
```

### Auto start Mongo upon machine start/restart

Mongo won't start after machine restart by default. 

If you want nginx auto start after machine reboot, first reate mongo launch daemon configuration downloading config gist `/Library/LaunchDaemons/org.mongodb.mongod.plist`

```
sudo wget -O /Library/LaunchDaemons/org.mongodb.mongod.plist  https://gist.githubusercontent.com/fullstackforger/b5ea73601cf61e6886c6570b6d3eb3bf/raw
```

Now use `launchctl` to load mongo jobs.
```
sudo launchctl load -w /Library/LaunchDaemons/org.mongodb.mongod.plist
sudo launchctl load -w /Library/LaunchDaemons/org.macports.mongodb.plist
```


## Create startup alias 

It will alow to run `mongostart` and `mongostop` to manually start and stop the mongodb instance.

`sudo vim ~/.profile` and append following script to the end of the file.

```
# Custom mongostart scripts starting mongo with configuration file
alias mongostart="sudo mongod -f /opt/local/etc/mongodb/mongod.conf"

# Custom mongostop alias killing mongo process
mongostop_func () {
   local mongopid=`less /opt/local/var/db/mongodb_data/mongod.lock`;
   if [[ $mongopid =~ [[:digit:]] ]]; then
       sudo kill -15 $mongopid;
       echo mongod process $mongopid terminated;
   else
       echo mongo process $mongopid not exist;
   fi
}
alias mongostop="mongostop_func"
```

Lastly execute `source ~/.profile` from the terminal


## Start MongoDB 

 - Use `mongostart` from the Terminal to **start** mongo 
 - Use `mongostop` to **stop** it

