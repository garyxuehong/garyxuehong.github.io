---
layout: post
title: "Learning NodeJS - Streaming"
author: Gary
tags: ["NodeJS"]
date: "2019-03-09T22:10:00.000Z"
image: ./01-stream.jpg
draft: false
---

Streaming is a very important part of NodeJS (or any programming language). Given NodeJS is running on single thread (I haven't learn worker thread yet, so I guess I will stick with single thread for now), processing data is stream on async io will be very interesting and important.

I learng this part basically from the [api doc](https://nodejs.org/api/stream.html). Also watch some of the video from [James Halliday - Networking and Streams](https://frontendmasters.com/courses/networking-streams/).


I'm just going the put some summary (which is just some of my idea of whats important) of the stream module.

### Types of Streams
> #
> 
> There are four fundamental stream types within Node.js:
> 
>     Writable - streams to which data can be written (for example, `fs.createWriteStream()`).
>     Readable - streams from which data can be read (for example, `fs.createReadStream()`).
>     Duplex - streams that are both Readable and Writable (for example, `net.Socket`).
>     Transform - Duplex streams that can modify or transform the data as it is written and read (for example, `zlib.createDeflate()`).
> 
> Additionally, this module includes the utility functions pipeline and finished.

### API for Stream Consumers
> Writable streams (such as res in the example) expose methods such as `write()` and `end()` that are used to write data onto the stream.
> 
> Readable streams use the EventEmitter API for notifying application code when data is available to be read off the stream. That available data can be read from the stream in multiple ways.
> 
> Both Writable and Readable streams use the EventEmitter API in various ways to communicate the current state of the stream.
> 
> Duplex and Transform streams are both Writable and Readable.

### Writable Stream
> All Writable streams implement the interface defined by the `stream.Writable` class.
> 
> While specific instances of Writable streams may differ in various ways, all Writable streams follow the same fundamental usage pattern as illustrated in the example below:
> ```javascript
> const myStream = getWritableStreamSomehow();
> myStream.write('some data');
> myStream.write('some more data');
> myStream.end('done writing data');
> ```

### Readable Stream
> Two Reading Modes

> Readable streams effectively operate in one of two modes: flowing and paused. These modes are separate from object mode. A Readable stream can be in object mode or not, regardless of whether it is in flowing mode or paused mode.
> 
>     In flowing mode, data is read from the underlying system automatically and provided to an application as quickly as possible using events via the EventEmitter interface.
> 
>     In paused mode, the stream.read() method must be called explicitly to read chunks of data from the stream.
> 

### Choose One API Style

> The Readable stream API evolved across multiple Node.js versions and provides multiple methods of consuming stream data. In general, developers should choose one of the methods of consuming data and should never use multiple methods to consume data from a single stream. Specifically, using a combination of `on('data')`, `on('readable')`, `pipe()`, or `async iterators` could lead to unintuitive behavior.
> 
> Use of the `readable.pipe()` method is recommended for most users as it has been implemented to provide the easiest way of consuming stream data. Developers that require more fine-grained control over the transfer and generation of data can use the `EventEmitter` and `readable.on('readable')/readable.read()` or the `readable.pause()/readable.resume()` APIs.
> 

### API for Stream Implementers

> ```javascript
> const { Writable } = require('stream');
> 
> class MyWritable extends Writable {
>   constructor(options) {
>     super(options);
>     // ...
>   }
> }
> ```
> 
> The new stream class must then implement one or more specific methods, depending on the type of stream being created, as detailed in the chart below:

   Use-case | Class	Method(s) to implement
   ---- | ----
   Reading only |	Readable	_read
   Writing only |	Writable	_write, _writev, _final
   Reading and writing	Duplex	| _read, _write, _writev, _final
   Operate on written data, then read the result	| Transform	_transform, _flush, _final

### Simplified Construction
> 
> ```javascript
> const { Writable } = require('stream');
> 
> const myWritable = new Writable({
>   write(chunk, encoding, callback) {
>     // ...
>   }
> });
> ```

### Errors While Writing

> ```javascript
> const { Writable } = require('stream');
> 
> const myWritable = new Writable({
>   write(chunk, encoding, callback) {
>     if (chunk.toString().indexOf('a') >= 0) {
>       callback(new Error('chunk is invalid'));
>     } else {
>       callback();
>     }
>   }
> });
> ```

### Errors While Reading

> ```javascript
> const { Readable } = require('stream');
> 
> const myReadable = new Readable({
>   read(size) {
>     if (checkSomeErrorCondition()) {
>       process.nextTick(() => this.emit('error', err));
>       return;
>     }
>     // do some work
>   }
> });
> ```

### Implementing a Transform Stream

> ```javascript
> const { Transform } = require('stream');
> 
> class MyTransform extends Transform {
>   constructor(options) {
>     super(options);
>     // ...
>   }
> }
> 
> MyTransform.prototype._transform = function(data, encoding, callback) {
>   this.push(data);
>   callback();
> };
> ```



