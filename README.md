# Node.js Tutorial

## 1. What is Node.js
* Node.js is an open source runtime environment for **server-side** and networking applications and is **single threaded**
* Uses Google Javascript V8 Engine to execute code
* It is **cross platform environment** and can run on OS X, Microsoft windows, Linux and FreeBSD
* Provides an **event driven** architecture and **non blocking I/O** that is optimized and scalable

#### Node.js First Example
* Create a Directory: ```mkdir node_examples```
* Create a File: first_example.js
* Go to node_examples directory: ```cd node_examples```
* Execute the java script file: ```node first_example.js```


#### Blocking vs Non-Blocking
* Non-Blocking I/O situation, waitress keep receiving the orders, does not wait for the chef to prepare food.
* Blocking: The waitress receives an order and delivers it to the chef. The waitress will wait until the food is ready and deliver back to the customer. Then only she can take new order. No new order is coming in unless the previous is delivered.

**Blocking** is when the execution of additional JavaScript in the Node.js process must wait until a non-JavaScript operation completes.

Eg 1 Blocking I/O:

```javascript
var fs = require("fs);

// Synchronous read
var data = fs.readFileSync('text.txt');
console.log(data.toString());
console.log('End here');
```
More methods will be blocked until the read method is not executed

Output:

```
Hello World

End here
```

Eg 2 Non-Blocking I/O:

```javascript
var fs = require("fs);

// Asynchronous read
fs.readFile("text.txt", function(err, data){
	if(err){
		console.log(err);
	}
	setTimeout(()=>{
		console.log('Display after 2 second');
	}, 2000);
});
console.log('Start here');
```
More methods will execute asynchronously

Output:

```
Start here

Display after 2 second
```


## 2. Node.js Modules











Ref:

[Node JS Full Course - Learn Node.js in 7 Hours | Node.js Tutorial for Beginners | Edureka](https://www.youtube.com/watch?v=JnvKXcSI7yk)
