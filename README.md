# Node.js Tutorial

[1. What is Node.js](Docs/01. What is Node.js.md)

# 2. Node.js Modules
* NPM
* GLOBALS
* FILE SYSTEM
* CALLBACKS
* EVENT
* HTTP

## 2.1 NPM (Node Package Manager)
* Provide online repositories for node.js packages/modules
* Provide command line utility to install Node.js package

```npm install```: Install all the modules as specified in package.json

```npm install <Module Name>```: Install Module using npm

```npm install <Module Name> -g```: Install dependency globally

## 2.2 Global Objects
These objects are available in all modules

* ```_dirname``` : specifies the name of the directory that currently contains the code
* ```_filename```: specifies the name of the file that currently contains the code

A timer in Node.js is an internal construct that calls a given function after a certain period of time

* ```setTimeout(callback, delay[,...args])```
* ```setInterval(callback, delay[,...args])```
* ```setImmediate(callback, delay[,...args])```

## 2.3 File System
File I/O provided by simple wrappers around standard POSIX function

Methods in File System Module

*  Opening a File
	* ```fs.open(path, flags[,mode],callback)```: Open file Asynchronously
	* ```fs.openSync(path, flags[,mode])```: Open file Synchronously
	* ```fs.close(fd, callback)```: Closing file
* Reading from a File
	* ```read(fd, buffer, offset, length, position, callback)```: Read Content of a File into Buffer
	* ```readFile(file[,options], callback)```: Read Files Asynchronously
	* ```readFileSync(file[, options])```: Read File Synchronously
* Writing in a File
	* ```writeFile(file, data[,options], callback)```: Write file Asynchronously
	* ```writeFileSync(file, data[,options])```: Write file Synchronously
	

## 2.4 Callback
The callback is an asynchronous equivalent for a function and is called at the completion of each task

For example, in

```javascript
var fs = require("fs);

// Asynchronous read
fs.readFile("text.txt", function(err, data){ });
```

The callback ```function(err, data)``` will execute after file read is complete, first return ```err```, then ```data```

## 2.5 Events
* Node.js follows the event-driven architecture
* Certain objects(emitters) periodically emit events which further invokes the listeners
* Node.js provides concurrency by using the concept of **events** and **callbacks**
* All objects that emit **events** are instances of the **EventEmitter** class

```javascript
var fs = require('fs');
var event = require('events');  // Import Events Module

const myEmitter = new event.EventEmitter();  // Creating object of EventEmitter

fs.readFile('test1.txt', (err, data) => {
    console.log(data.toString());
    myEmitter.emit('readFile'); // Emitting Event
});

myEmitter.on('readFile', ()=>{       // Registering Listener and defining an event handler
    console.log('\nRead Event Occurred!')
})
```

## 2.6 HTTP
* To use the HTTP server and client one must require('http')
* The HTTP interfaces in Node.js are designed to support many features of the protocol

```javascript
// Import required Modules
var http = require('http');
var fs = require('fs');
var url = require('url');

http.createServer(function (request, response) { // Creating Server
    var pathname = url.parse(request.url).pathname;     // Parse the fetched URL to get pathname
    console.log("Request for "+ pathname + " received.");

    fs.readFile(pathname.substr(1), function (err, data) {    // Request file to be read fom file system
        if (err){
            console.log(err);
            response.writeHead(404, {'Content-Type': 'text/html'});     // Creating Header with content type as text or HTML
        }else{
            response.writeHead(404, {'Content-Type': 'text/html'});     // Generating response
            response.write(data.toString());
        }
        response.end();
    });
}).listen(3000);    // Listening to port: 3000

console.log('Server running at localhost:3000')
```

## Hands on example 1
* Create a Directory: ```mkdir hands_on_example_1```
* ```cd hands_on_example_1```
* Create ```package.json```: ```npm init```
* At entry point: add ```app.js```
* Install dependency
	* package 1: ```npm install express --save```
	* package 2: ```npm install jade --save```
	* package 3: ```npm install nano --save```
	* package 4: ```npm install body-parser --save```
	* package 5: ```npm install errorhandler --save```
	* package 6: ```npm install url --save```
	* package 7: ```npm install serve-favicon --save```
	* package 8: ```npm install logger --save```
	* package 9: ```npm install json --save```
	* package 10: ```npm install express-session --save```
	* package 11: ```npm install method-override --save```
* ```npm install```


```javascript
var express = require('express');
var routes = require('./routes');
var http = require('http');
var path = require('path');
var urlencoded = require('url');
var bodyParser = require('body-parser');
var json = require('json');
var logger = require('logger');
var methodOverride = require('method-override');

var nano = require('nano')('http://localhost:5984');

var db = nano.use('address');
var app = express;

app.set('port', process.env.PORT || 3000);
app.set('views', path.join(_dirname, 'views'));
app.set('view engine', 'jade');

app.use(bodyParser.json());
app.use(bodyParser.urlencoded());
app.use(methodOverride());
app.use(exports.static(path.join(_dirname,'public')));

app.get('/', routes.index);

app.post('/createdb', function (req, res) {
    nano.db.create(req.body.dbname, function(err){
        if(err){
            res.send("Error creating Database" + req.body.db_name);
            return;
        }
        res.send("Database" + req.body.db_name + "create successfully");
    });
});

app.post('/new_contact', function (req, res) {
    var name = req.body.name;
    var phone = req.body.phone;

    db.insert({name:name, phone:phone, crazt:true}, phone, function (err, body, header) {
        if(err){
            res.send("Error creating contact");
            return;
        }
        res.send("Contact created successfully");
    });
});

app.post('/view_contact', function (req, res) {
    var alldoc = "Following are the contatcs";
    db.get(req.body.phone, {revs_info:true}, function (err, body) {
        if(!err){
            console.log(body);
        }

        if(body){
            alldoc += "Name: " + body.name + "<br/>Phone Number" + body.phone;
        }else{
            alldoc = "No records found";
        }
        res.sned(alldoc);
    });
});

app.post('/delete_contact', function (req, res) {
    db.get(req.body.phone, {revs_info:true}, function (err, body) {
        if(!err){
            db.destroy(req.body.phone, body._rev, function (err, body) {
                if(err){
                    res.send("error deleting contact");
                }
            })
            res.send("Contacts deleted successfully");
        }
    });
});

http.createServer(app).listen(app.get('port'), function () {
    console.log("Express server listening on port" + app.get('port'))

})
```

* ```node app.js```

## Hands on example 2
* Create a Directory: ```mkdir hands_on_example_2```
* ```cd hands_on_example_2```
* Create ```package.json```: ```npm init```
* Create app.js

#### Example 1:
```javascript
var first_name = "Jie";

console.log(first_name);
```

Output:

```
Jie
```

#### Example 2a:
```javascript
var age1 = 30;
var age2 = 30;

var result = age1 == age2;

console.log(result);
```

Output:

```
true
```

#### Example 2b:
```javascript
var age1 = 30;
var age2 = '30';

var result = age1 == age2;

console.log(result);
```

Output:

```
true
```

#### Example 2c:
```javascript
var age1 = 30;
var age2 = '30';

var result = age1 === age2;

console.log(result);
```

Output:

```
false
```

#### Example 3a:
```javascript
function sayHello(name) {
    return 'Hello ' + name + '!!!';
}

console.log(sayHello('Jie'));
```

Output:

```
Hello Jie!!!
```

#### Example 3b:
```javascript
var sayHello = function (name) {
    return 'Hello ' + name + '!!!';
}

console.log(sayHello('Jie'))
```

Output:

```
Hello Jie!!!
```

#### Example 4:
```javascript
var student = {
    name : 'Jie',
    email: 'jie@gail.com'
}

console.log(student.name);
```

Output:

```
Jie
```

#### Example 5:
```javascript
var http = require('http');
var events = require('events');

var eventEmitter = new events.EventEmitter();

var server = http.createServer(function (req, res) {
    eventEmitter.emit('someone requested', "TEST");     // Event Trigger
    res.end('Server works!!!');
});

eventEmitter.on('someone requested', function (data) {
    console.log('A request has been done on the server!', data);
});   //Event listener

server.listen(3000, 'localhost', function () {
    console.log('Server started on port : 3000');
});

```

Output:

```
Server started on port : 3000

A request has been done on the server! TEST
```

#### Example 6:
```npm i express```

```javascript
var express = require('express');
var http = require('http');
var fs = require('fs');

var app = express();
var server = http.createServer(app);

app.get('/', function (req, res) {
    res.send('<h1>Express Works!</h1>');
});

app.get('/tasks', function (req, res) {
    fs.readFile('./db.json', function (err, data) {
        var tasks = JSON.parse(data.toString()).tasks;
        res.json(tasks);
    });
});

server.listen(3000, function () {
    console.log('Server listening to port 3000');
});
```





Reference:

[Node JS Full Course - Learn Node.js in 7 Hours | Node.js Tutorial for Beginners | Edureka](https://www.youtube.com/watch?v=JnvKXcSI7yk)
