# MySQL


<div style="text-align: left" >
<img src="https://github.com/WAGO/mysql/blob/master/images/mysql-on-pfc.png?raw=true" alt="mysql on pfc" width="300"/>
</div>

# How to run MySQL container

## Prerequisites for tutorial
- Preinstalled SSH Client (e.g. https://www.putty.org/)
- Wago PFC with Docker (see https://github.com/WAGO/docker-ipk)

## WAGO Device Login
Start SSH Client e.g. Putty 
 ```bash
login as `root`
password `wago`
 ```

 ## Check docker installation

```bash
docker info
docker ps # to see all running container (no container should run)
docker images # to see all preinstalled images
 ```

 ## Get prebuild MySQL image
```bash
docker pull wagoautomation/mysql 
 ```

 ## Start a MySQL server instance
 ```bash
 
docker run -d --name mysql -e MYSQL_ROOT_PASSWORD=my-secret-pw wagoautomation/mysql
                            or
docker run -d --name mysql -e MYSQL_ROOT_PASSWORD=my-secret-pw -e MYSQL_DATABASE=wago_db wagoautomation/mysql 
```

## Environment Variables
When you start the mysql image, you can adjust the configuration of the MySQL instance by passing one or more environment variables on the docker run command line.
For a complete documentation see: https://hub.docker.com/_/mysql


## How to access MySQL container.

There are several ways to access the database contianer. Here are some possibilities. 

### E-Cockpit "WagoAppSQL_MySQL"
Use an exsample Project to access MySQL database. 
see https://github.com/WAGO/mysql/raw/master/misc/WagoAppMySQL_Example.ecp


### Adminer
Any database management tool can be used for testing see (e.g https://hub.docker.com/_/adminer)

1. Start adminer container 
 ```bash
docker run --link mysql:db -p 8080:8080 adminer
```
2. Open your browser on http://host:8080/
3. Login and test MySQL db.
 
### Node-Red
Or use Wago-Node-Red containers with node-red-node-mysql nodes. 
The flows assume a database called "wago_db" with table "wago" and following schema with 3 columns, data1 (int), data2 (int) and data3 (double).
The 3 values are fed into the 'Format data' template node via msg.data1, msg.data2 & msg.data3.

1. Start Node-Red Container see https://hub.docker.com/r/wagoautomation/node-red-iot
2. Install node-red-node-mysql node.
2. Copy and deploy Node-Red Flow.
```bash
[{"id":"69170fdf.7839d","type":"tab","label":"Flow 1","disabled":false,"info":""},{"id":"185c73ab.42c4ac","type":"inject","z":"69170fdf.7839d","name":"","topic":"","payload":"{\"data1\":10,\"data2\":40,\"data3\":\"1601554064887\"}","payloadType":"json","repeat":"","crontab":"","once":false,"onceDelay":0.1,"x":140,"y":140,"wires":[["7195cebf.71a57"]]},{"id":"39362f0a.dfdb6","type":"mysql","z":"69170fdf.7839d","mydb":"a4668a75.c4e7d8","name":"MYSQL","x":520,"y":260,"wires":[["9c3b3067.0184"]]},{"id":"9c3b3067.0184","type":"debug","z":"69170fdf.7839d","name":"","active":true,"console":"false","complete":"false","x":710,"y":260,"wires":[]},{"id":"3850414.f7c6fbe","type":"inject","z":"69170fdf.7839d","name":"Timestamp","topic":"","payload":"","payloadType":"date","repeat":"","crontab":"","once":false,"onceDelay":"","x":140,"y":260,"wires":[["2b2d1e2c.8684d2"]]},{"id":"927d481b.f3ea18","type":"comment","z":"69170fdf.7839d","name":"Flow to query database and format for chart","info":"","x":230,"y":220,"wires":[]},{"id":"bc990c5b.0578","type":"mysql","z":"69170fdf.7839d","mydb":"a4668a75.c4e7d8","name":"MYSQL","x":520,"y":140,"wires":[["4f6897ef.f11978"]]},{"id":"7195cebf.71a57","type":"template","z":"69170fdf.7839d","name":"Format data","field":"topic","fieldType":"msg","format":"handlebars","syntax":"mustache","template":"INSERT INTO wago (data1, data2, data3) VALUES ({{payload.data1}},{{payload.data2}},{{payload.data3}})\n","output":"str","x":310,"y":140,"wires":[["bc990c5b.0578"]]},{"id":"4f601705.8a06d8","type":"comment","z":"69170fdf.7839d","name":"Flow to insert data into the database","info":"","x":200,"y":100,"wires":[]},{"id":"2b2d1e2c.8684d2","type":"template","z":"69170fdf.7839d","name":"Format query 1","field":"topic","fieldType":"msg","format":"handlebars","syntax":"mustache","template":"SELECT * FROM wago ","output":"str","x":320,"y":260,"wires":[["39362f0a.dfdb6"]]},{"id":"4f6897ef.f11978","type":"debug","z":"69170fdf.7839d","name":"","active":true,"tosidebar":true,"console":false,"tostatus":false,"complete":"true","targetType":"full","x":670,"y":140,"wires":[]},{"id":"a4668a75.c4e7d8","type":"MySQLdatabase","z":"","name":"db","host":"192.168.42.97","port":"3306","db":"wago_db","tz":""}]
```
3. Configure database credentials "User", "Password" and "Database"
4. Deploy Flow.

