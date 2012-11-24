jsRocket
========
Let's you use <a href="https://github.com/kusma/rocket">GNU Rocket</a> with your JavaScript / Browser demo.
Currently depends on <a href="https://github.com/kanaka/websockify">Websockify</a>, which acts as a connector between the browser and GNU Rocket.

Rocket?
-------
To sync stuff to music, or just change values over time, you might currently hardcode them somewhere in your demo. Rocket gives you a tracker alike interface, which might make your musician feel right at home.

js?
---
You work in two modes:

**edit mode**
- - - - - - -
Rocket takes control over your demo, and you're able to set various values, interpolate between them - and even move them around.
For how to use <a href="https://github.com/kusma/rocket/">Rocket</a> check their <a href="https://github.com/kusma/rocket/blob/master/README">description</a>

```js
//This is the API you'll use
var syncDevice = new JSRocket.SyncDevice(),

    //Beats per minute of your demo tune
    BPM = 170,

    //The resolution between two beats, four is usually fine eight adds a bit more finer control
    ROWS_PER_BEAT = 8,

    //we calculate this now, so we can translate between rows and seconds later on
    ROW_RATE = BPM / 60 * ROWS_PER_BEAT,

    //your variable that needs tuning in Rocket
    awesomeness,

    //the current row we're on
    row;

//ws://localhost:8080 sucks?
//syncDevice.setConfig({socketURL:"ws://lolcathost:1337"});

//initialize the connection, default URL is ws://localhost:8080
syncDevice.init();

//-- set up all the things --
//this is also triggered when the Rocket XML is done, so make sure your ogg is ready
syncDevice.on('ready', onSyncReady);
//whenever you change the row, a value or interpolation mode this will get called
syncDevice.on('update', onSyncUpdate);
//[Spacebar] in Rocket calls one of those
syncDevice.on('play', onPlay);
syncDevice.on('pause', onPause);

function onSyncReady(){
  //jsRocket is done getting all the info you already have in Rocket, or is done parsing the .rocket file

  //this either adds a track to Rocket, or gets it for you
  awesomeness = _syncDevice.getTrack('AmountOfAwesome');
}

function onSyncUpdate(newRow){
  //row is only given if you navigate, or change a value on the row in Rocket
  //on interpolation change (hit [i]) no row value is sent, as the current there is the upper row of your block
  if(!isNaN(row)){
    row = newRow;
  }

  //update your view
  render(row);
}

function onPlay(){
  //you could also set tune.currentTime here
  console.log("[onPlay] time in seconds", row / ROW_RATE);
}

function onPause(){
  //pause your tune
  console.log("[onPause] time in seconds", row / ROW_RATE);
}

function render(row){
  //change your index of an array, rotation or amount of awesomeness here
  console.log("[render] amount of awesome is now", awesomeness.getValue(row));
}
```
Looks like a fuckton of code, but imagine what hassle you're save tweening your way around with jQuery (which also adds >30kb), sans sockets jsRocket is under 5kb additional to your sync rocket file.
Jump back and forward in the demo, fine tune all the things, or just hand it to somebody who does all the graphics :3

**demo mode**
- - - - - - -
So, you're done with syncing, saved the rocket file and ready to win the compo.

```js
//instead of connecting to the socket, you specify the rocket file (getConfig() works btw)
syncDevice.setConfig({rocketXML:"cube.rocket"});

//you're about to demo! Make sure that pants are dropped.
syncDevice.init("demo");
```

jsRocket will now parse the XML, and call //onSyncReady//.
There you either start the demo by playing your tune, or you load the ogg there.

Good luck with the compo :)

If you need a bit more space you can delete, or uncomment, the following:
```js
onSyncUpdate
onPlay
onPause
```

Additional Notes
----------------

```js
//if you need to nuke/nerf/remove the listeners on the callback functions do this:
syncDevice.on('_HANDLER_', function(){});
```

** Rocket **
Listens on port 1338 on your localhost, which is hardcoded - so be sure to bend your websocket port to that port in the end.

** Websockify **
I found it a bit of a pain to get it to run, however I'm not a Python person either - so it might just be me.

For me Python 2.7 went smoother, as I was able to build an exe with it - an exe with a lot of extra files and combined is 24MB.
I've not tried any other websocket bender, as this worked out of the box, they do have <a href="https://github.com/kanaka/websockify/wiki/Compiling-Websockify-as-Windows-Executable">documentation</a>
Compiled I got a ~20mb folder with a exe.

If you need help, let them know.


For the standard config use these:
```cli
websockify.exe 8080 localhost:1338
```

HALP
----
The websocket bender seems to be the biggest hurdle, if you find something easier let me know or pull :)