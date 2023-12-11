---
title: Chess Game
summary: A game of chess with runtime geometry generation.
teaser: chess/teaser.jpg
github: Chess
date: "2019-10-05"
slug: chess
---

This is a straightforward game of chess.
Use these sliders to change the horizontal and vertical geometry resolution:

<div style="display: grid; grid-template-columns: auto 1fr; gap: 1em">
  Horizontal:
  <input
    style="width: 100%"
    type="range"
    min="3"
    max="50"
    value="50"
    class="slider"
    oninput="Module.Config.geo_resolution_horizontal=Number(this.value); Module.Config.geo_changed=true"/>
  Vertical:
  <td style="width: 100%">
  <input
    style="width: 100%"
    type="range"
    min="3"
    max="50"
    value="50"
    class="slider"
    oninput="Module.Config.geo_resolution_vertical=Number(this.value); Module.Config.geo_changed=true"/>
</div>

You will be playing with the white pieces. Simply click them to see valid moves.
Click on one of the highlighted fields to move your piece to that position.
If you would like to play with a friend, or take this to the next level and beat
yourself, you can disable the "AI" with this checkbox:

<label>
  <input style="width: 1em; height: 1em" type="checkbox" checked="true" oninput="Module.Config.ai=this.checked"/>
  Enable AI
</label>

<div id="container" style="position: relative">
  <img src="/images/chess/placeholder.jpg" style="display: block; z-index: -2"></img>
  <canvas class="emscripten" id="canvas" oncontextmenu="event.preventDefault()" tabindex=-1 style="position: absolute; left: 0; top: 0; touch-action: none"></canvas>
  <div id="progress" style="position:absolute; top: 0; right: 0; height: 100%; width: 100%; background: black; opacity: .25"></div>
  <div style="position: absolute; bottom: 0; right: 0">
    <button class="icon-button" title="Fullscreen" onclick="Module.requestFullscreen(false, true)">
      <i class="material-icons">fullscreen</i>
    </button>
  </div>
  <div style="position: absolute; bottom: 0; left: 0">
    <button class="icon-button" title="Watch Demo (d)" onclick="Module.Config.demo=true">
      <i class="material-icons">play_arrow</i>
    </button>
    <button class="icon-button" title="Restart Game (n)" onclick="Module.Config.new_game=true">
      <i class="material-icons">stop</i>
    </button>
    <button class="icon-button" title="Undo Move (z)" onclick="Module.Config.undo_turn=true">
      <i class="material-icons">undo</i>
    </button>
  </div>
</div>

If you would like to look around the scene simply drag across the game with the left mouse button pressed.
You can also have a closer look at the pieces or take a step back by zooming with the mouse wheel.

Just in case you feel lazy there is a demo mode ;)
Simply press the play button and watch a legendary game between [Kasparov and Topalov](http://www.chessgames.com/perl/chessgame?gid=1011478).
You can always reset the game and start over with the stop button.

Thanks for checking out the game!
You can check out the source on [GitHub](https://github.com/flostellbrink/Chess).

<script type='text/javascript'>
  var containerElement = document.getElementById ("container");
  var canvasElement = document.getElementById('canvas');
  var progressElement = document.getElementById('progress');

  function resize() {
    var width = containerElement.parentElement.clientWidth;
    Module.setCanvasSize(width, width);
  }

  var canvasAddEventListener = canvasElement.addEventListener;
  canvas.addEventListener = function(event, handler, capturing) {
    if (event !== 'wheel' && event !== 'mousewheel')
      return canvasAddEventListener(event, handler, capturing);

      wrapper = function(event) {
        return handler({
          type: 'wheel',
          deltaX: 0,
          deltaY: Browser.getMouseWheelDelta(event),
          deltaMode: 1,
          target: event.target,
          preventDefault: function() { event.preventDefault(); }
        })
      };

      return canvasAddEventListener(event, wrapper, { passive: false });
  };

  var Module = {
    preRun: [],
    postRun: [],
    locateFile: function(path, prefix) {
      return "https://flostellbrink.github.io/Chess/" + path;
    },
    print: (function() {
      return function(text) {
        if (arguments.length > 1) text = Array.prototype.slice.call(arguments).join(' ');
        console.log(text);
      };
    })(),
    printErr: function(text) {
      if (arguments.length > 1) text = Array.prototype.slice.call(arguments).join(' ');
      console.error(text);
      Module.print(text);
    },
    canvas: (function() {
      return canvasElement;
    })(),
    setStatus: function(text) {
      Module.print(text);
      resize();
      var m = text.match(/(Downloading data\.\.\.) \((\d+(\.\d+)?)\/(\d+)\)/);
      if (m) {
        progressElement.style.width = (100 - parseInt(m[2]) / parseInt(m[4]) * 100) + '%';
      }
    },
    totalDependencies: 0,
    monitorRunDependencies: function(left) {
      this.totalDependencies = Math.max(this.totalDependencies, left);
      Module.setStatus(left ? 'Preparing... (' + (this.totalDependencies-left) + '/' + this.totalDependencies + ')' : 'All downloads complete.');
    }
  };

  window.onerror = function(event) {
    Module.setStatus('Exception thrown, see JavaScript console');
    Module.setStatus = function(text) {
      if (text) Module.printErr('[post-exception status] ' + text);
    };
  };

  window.addEventListener("resize", resize);
  window.addEventListener("fullscreenchange", resize);
</script>
<script async type="text/javascript" src="https://flostellbrink.github.io/Chess/index.js"></script>
