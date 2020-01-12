# Node-Express-Socket.IO Project (WebRTC Signalling Server)   ![Version][version-image]

![Linux Build][linuxbuild-image]
![Windows Build][windowsbuild-image]
![NSP Status][nspstatus-image]
![Test Coverage][coverage-image]
![Dependency Status][dependency-image]
![devDependencies Status][devdependency-image]

The quickest way to get start with WebRTC (Web Real-Time Communication) Signalling Server using Node.Js, Express & Socket.IO, just clone the project:

```bash
$ git clone https://github.com/arjunkhetia/Node.Js-Express-WebRTC-Project.git
```

Install dependencies:

```bash
$ npm install
```

Start Express.js app at `http://localhost:3000/`:

```bash
$ npm start
```

You can clone the Front-end for this project with Angular - 8.2.8 & Socket.IO - 2.3.0 at :

```bash
$ git clone https://github.com/arjunkhetia/Angular-WebRTC-Project.git
```

Install dependencies:

```bash
$ npm install
```

Start the Angular app at `http://localhost:4200/`:

```bash
$ npm start
```

# Socket.IO

Socket.IO enables real-time bidirectional event-based communication.

```js
var clients = [];
var clientId = 0;

socketio.on('connection', function(socket) {

  clientId++;
  var client = {
    clientId: clientId,
    socketId: socket.id
  }
  clients.push(client);
  console.log(socket.id + ' - client connected');
  socketio.emit('socketid', client);

  socket.on('clients', () => {
    socketio.emit('clients', clients);
  });

  socket.on('offer', (offer) => {
    clients.forEach((client) => {
      if (offer['to'] == client['clientId']) {
        socket.broadcast.to(client['socketId']).emit('offer', offer);
      }
    });
  });

  socket.on('answer', (answer) => {
    clients.forEach((client) => {
      if (answer['to'] == client['clientId']) {
        socket.broadcast.to(client['socketId']).emit('answer', answer);
      }
    });
  });

  socket.on('icecandidate', (candidate) => {
    clients.forEach((client) => {
      if (candidate['to'] == client['clientId']) {
        socket.broadcast.to(client['socketId']).emit('icecandidate', candidate);
      }
    });
  });

  socket.on('file', (file) => {
    clients.forEach((client) => {
      if (file['to'] == client['clientId']) {
        socket.broadcast.to(client['socketId']).emit('file', file);
      }
    });
  });

  socket.on('disconnect', function() {
    clients.forEach((client) => {
      if (client['socketId'] == socket.id) {
        var socketIndex = clients.indexOf(client);
        clients.splice(socketIndex, 1);
        console.log(socket.id + ' - client disconnected');
        socketio.emit('clients', clients);
      }
    });
  });

});
```

# Nodemon

Nodemon will watch the files in the directory in which nodemon was started, and if any files change, nodemon will automatically restart your node application.

Start Express.js app with nodemon at `http://localhost:3000/`:

```bash
$ nodemon bin/www
```

# Node PortFinder

Node PortFinder is a tool to find an open port or domain socket on the machine.

```js
var portfinder = require('portfinder');
var port = 3000;
var portSpan = 999;
portfinder.getPort({
  port: port,    // minimum port number
  stopPort: port + portSpan // maximum port number
}, function (err, openPort) {
  if (err) throw err;
  port = openPort;
});
```

# Nodejs Cluster

Node.js runs in a single process, by default. Ideally, we want one process for each CPU core, so we can distribute the workload across all the cores. Hence improving the scalability of web apps handling HTTP requests and performance in general. In addition to this, if one worker crashes, the others are still available to handle requests.

```js
var cluster = require('cluster');
var workers = process.env.WORKERS || require('os').cpus().length;

if (cluster.isMaster) {
  console.log('Master cluster is running on %s with %s workers', process.pid, workers);
  for (var i = 0; i < workers; ++i) {
    var worker = cluster.fork().process;
    console.log('worker %s on %s started', i+1, worker.pid);
  }
  cluster.on('exit', function(worker, code, signal) {
    console.log('worker %s died. restarting...', worker.process.pid);
    cluster.fork();
  });
}

if (cluster.isWorker) {
  // Server code
}
```

# Logger - Morgan & Winston

Morgan - HTTP request logger middleware for node.js:

```js
var logger = require('morgan');
app.use(logger('dev'));
app.use(logger(':remote-addr :remote-user :datetime :req[header] :method :url HTTP/:http-version :status :res[content-length] :res[header] :response-time[digits] :referrer :user-agent', {
    stream: accessLogStream
}));
```

Winston - is designed to be a simple and universal logging library with support for multiple transports:

```js
var winston = require('winston');
var logger = winston.createLogger({
  format: winston.format.combine(
    winston.format.colorize({
        all: true
    }),
    winston.format.printf(
        data => `${data.level} : ${data.message}`
    )
  ),
  transports: [
    new winston.transports.Console({
      level: 'silly'
    }),
    new winston.transports.File({
      level: 'silly',
      filename: './log/ServerData.log'
    })
  ]
});
```

# Rotating File Stream

To provide an automated rotation of Express/Connect logs or anything else that writes to a log on a regular basis that needs to be rotated based on date.

```js
var rfs    = require('rotating-file-stream');
var accessLogStream = rfs('file.log', {
    size:     '10M', // rotate every 10 MegaBytes written
    interval: '1d', // rotate daily
    compress: 'gzip' // compress rotated files
    path: 'log' // folder path for log files
});
```

# Server Status Monitor

Express Status Monitor is simple, self-hosted module based on Socket.io and Chart.js to report realtime server metrics for Express-based ode servers.

```js
app.use(require('express-status-monitor')({
  title: 'Server Status', // title for status screen
  path: '/status', // path for server status invokation
  spans: [{
    interval: 1, // every second
    retention: 60 // keep 60 datapoints in memory
  }],
  chartVisibility: {
    cpu: true, // enable CPU Usage
    mem: true, // enable Memory Usage
    load: true, // enable One Minute Load Avg
    responseTime: true, // enable Response Time
    rps: true, // enable Requests per Second
    statusCodes: true // enable Status Codes
  },
  healthChecks: [{
    protocol: 'http', // protocol
    host: 'localhost' // server host name
    path: '/users', // endpoint to check status
    port: '3000' // server port
  }] // health check will be considered successful if the endpoint returns a 200 status code
}));
```

![Monitoring Page](https://github.com/arjunkhetia/Node.Js-Express-WebRTC-Project/blob/master/public/status-monitor.png "Monitoring Page")

[version-image]: https://img.shields.io/badge/Version-1.0.0-orange.svg
[linuxbuild-image]: https://img.shields.io/badge/Linux-passing-brightgreen.svg
[windowsbuild-image]: https://img.shields.io/badge/Windows-passing-brightgreen.svg
[nspstatus-image]: https://img.shields.io/badge/nsp-no_known_vulns-blue.svg
[coverage-image]: https://img.shields.io/coveralls/expressjs/express/master.svg
[dependency-image]: https://img.shields.io/badge/dependencies-up_to_date-brightgreen.svg
[devdependency-image]: https://img.shields.io/badge/devdependencies-up_to_date-yellow.svg
