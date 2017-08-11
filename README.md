# MongoLearning

### Create Mongo as a Service    

##### 1. Create a config file with your options:
```bash
mongo.cfg
	port=|Port|
	dbpath=|DatabasePath EX: "C:\data\db"|
	logpath=|LogPath EX: "C:\data\dbConf\mongo.log" (the file cannot exist)|
	serviceDisplayName=|ServiceDisplayName EX: "MongoDbService"|
	serviceName=|ServiceName EX: "MongoDbService"|
```
> The annotation of | ... | is for variables

##### 2. Execute the command bellow to create and start the service
```bash
mongod --config "|ConfigPath|\mongo.cfg" --install
net start -|ServiceName|
```

### Mongo Auth  

##### 1. Connect in mongo
```bash
mongo --port |Port|
```

##### 2. You'll need to use admin
```bash
use admin
```

##### 3. Create a user with high permissions
```bash
db.addUser({ user:"|UserName|", pwd:"|UserPassword|", roles:[{ role:"userAdminAnyDatabase", db:"admin" }] })
```

##### 4. List the user to see the value of "Credentials", if appears "MONGODB-CR", you can jump the step 4, otherwise you'll need to follow it
```bash
db.system.users.find({}).pretty()
```

##### 4.1. Get the auth schema version
```bash
var v = db.system.version.findOne({ "_id": "authSchema" })
v.currentVersion
```

##### 4.2. If the "currentVersion" is not 3, we need to put it to 3
```bash
v.currentVersion = 3
db.system.version.save(v)
```

##### 4.3. Drop the user
```bash
db.dropUser("|UserName|")
```

##### 4.4. Create the user again (same as step 3)
```bash
db.addUser({ user:"|UserName|", pwd:"|UserPassword|", roles:[{ role:"userAdminAnyDatabase", db:"admin" }] })
```

##### 4.5. List the user again and verify if the "Credentials" now it's "MONGODB-CR".
```bash
db.system.users.find({}).pretty() 
```
> If don't appears "MONGODB-CR", the only solution is pray to some God (or...see the mongo documentation :] )

##### 5. Reconnect in mongo
```bash
*CTRL+C
mongo --port |Port|
```

##### 6. Try to list the users, you'll see the message that you are "Unauthorized"
```bash
user admin
db.system.users.find({}).pretty()
```

##### 7 Reconnect again, but now sending the credentials and try the step 5 again
```bash
mongo --port |Port| -u |UserName| -p |UserPassword| --authenticationDatabase admin
```

### Mongo Permission Database   

##### 7. Changing the access permission of a Database
```bash
mongo --port |Port| -u |UserName| -p |UserPassword| --authenticationDatabase admin
use admin
db.grantRolesToUser("~UserName~", [{ role: "readWrite", db: "|DatabaseName|" }]) 
```
