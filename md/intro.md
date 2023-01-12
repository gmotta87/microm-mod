[![Build Status](https://travis-ci.org/zzarcon/microm.svg)](https://travis-ci.org/zzarcon/microm)
[![npm version](https://badge.fury.io/js/microm.svg)](https://badge.fury.io/js/microm)
[![Bower version](https://badge.fury.io/bo/microm.svg)](http://badge.fury.io/bo/microm)
[DEMO](http://zzarcon.github.io/microm)
# Microm

> Beautiful library to convert browser microphone to mp3 in Javascript

Microm it's just a wrapper of few audio converting libraries which exposes a fully **Promise** and **Event oriented** api. Microm goal it's to make trivial to **play and convert audio** in the browser.

# Installation
`$ npm install microm`

or

`$ bower install microm`

# Usage

**Recording and converting the user microphone**
```javascript
var microm = new Microm();
var mp3 = null;

start();
setTimeout(stop, 1500);

function start() {
  microm.startRecording().then(function() {
    console.log('recording...')
  }).catch(function() {
    console.log('error recording');
  });
}

function stop() {
  microm.stop().then(function(result) {
    mp3 = result;
    console.log(mp3.url, mp3.blob, mp3.buffer);

    play();
    download();
  });
}

function play() {
  microm.play();
}

function download() {
  var fileName = 'cat_voice';
  microm.download(fileName);
}
```

**Reacting to events**

```javascript
microm.on('timeupdate', updateCurrentTime);
microm.on('loadedmetadata', onLoaded);
microm.on('play', onPlayEvent);
microm.on('pause', onPauseEvent);
microm.on('ended', onEndEvent);

function onLoaded(time) {
  duration.innerHTML = time;
}

function updateCurrentTime(time) {
  currentTime.innerHTML = time;
}

function onPlayEvent() {
  status.innerHTML = 'Playing';
}

function onPauseEvent(time) {
  status.innerHTML = 'Paused';
}

function onEndEvent() {
  status.innerHTML = 'Ended';
}

```


**Upload mp3 to the server**

```javascript
microm.getBase64().then(function(base64string) {
  $.ajax({
    type: 'POST',
    contentType: 'application/octet-stream',
    mimeType: 'audio/mpeg',
    processData: false,
    url: 'server/upload_audio',
    data: base64string,
    success: onSuccess
  });
});

```

# Under the hood
  To achieve that, Microm uses the following libs:
  * [lamejs](https://github.com/zhuker/lamejs) mp3 encoder in javascript
  * [webrtc-adapter](https://github.com/webrtc/adapter) Shim to insulate apps from spec changes and prefix differences
  * [RecordRTC](https://github.com/muaz-khan/RecordRTC) record WebRTC audio/video media streams
  * [RSVP](https://github.com/tildeio/rsvp.js/) Provides tools for organizing asynchronous code
  
  #### How Microm uses that libraries?
  In order to get the user recorded data, we use **webrtc-adapter** + **RecordRTC** which provides some shims and tools to make it easy and without worry about crossbrowser issues.

  Later we use **lamejs** to convert the Wav to Mp3. We can say that the hard work happen in that lib <3.

  And finally to provide a Promise based Api we use **RSVP** which support the [Promises/A+](https://promisesaplus.com/) and have a great support.

# Browser support
  The library just work in **Chrome** and **Firefox** right now. More info:

  * [Caniuse Stream](http://caniuse.com/#feat=stream)
  * [Caniuse WebRTC](http://caniuse.com/#feat=rtcpeerconnection)
  * [navigator.getUserMedia deprecation](https://developer.mozilla.org/en/docs/Web/API/Navigator/getUserMedia)
  * [navigator.mediaDevices.getUserMedia](https://developer.mozilla.org/en-US/docs/Web/API/MediaDevices/getUserMedia)
  * [Safari is the new IE](https://news.ycombinator.com/item?id=9804533)
  
# Api reference
