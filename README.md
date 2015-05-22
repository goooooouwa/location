## Firefox bug demo
See this bug in action on JSBin: http://jsbin.com/cijonu

Code to reproduce the bug in Firefox( you might need to run it **several** times):
```javascript
console.log('processing: task #1');
setTimeout(function(){
  console.log('processing: task #3');
},0);
alert('See console logs');
console.log('processing: task #2');
```
### Expected result
console output:
```
"processing: task #1"
"processing: task #2"
"processing: task #3"
```
### Actual result
console output:
```
"processing: task #1"
"processing: task #3"
"processing: task #2"
```
## How this bug causes the below bug
1. After user hit broswer back button, the URL is changed, the browser history is then changed, which triggers a `PopStateEvent`, and Ember then handles this event with `onUpdateURL()` callback
1. As the callback, Ember starts a transition by calling `this._doURLTransition('handleURL', url);`
1. Inside the transition, a Promise is created to determine the resolution of this transition. Ember schedules promises in runloop by calling `run.backburner.schedule('actions', function(){...})`, since no runloop is created, an `autorun` [will be created](http://guides.emberjs.com/v1.10.0/understanding-ember/run-loop/) by calling `Backburner.createAutoRun()`:
```javascript
    function createAutorun(backburner) {
      backburner.begin();
      backburner._autorun = global.setTimeout(function() {
        backburner._autorun = null; 
        backburner.end();
      });   
    }
```
before the auto-created runloop ends, the following code is executed:
```javascript
willTransition: function(transition){
  console.log('-------------- 1. start IndexRoute#willTransition -------------- ');
  alert('See console logs');
  console.log('-------------- 2. end IndexRoute#willTransition -------------- ');
}
```
when the runloop ends, the flush process begins, and the following code is executed:
```javascript
model: function() {
  console.log('-------------- 3. start FooRoute#model -------------- ');
  return [];
}
```
The above code is essencailly the same as:
```javascript
console.log('processing: task #1');
setTimeout(function(){
  console.log('processing: task #3');
},0);
alert('See console logs');
console.log('processing: task #2');
```

Above is the Firefox bug, which causes the below Ember bug.

------

# Ember bug demo
See this bug in action **with Firefox**( version 12+ on OS X, version 7+ on Windows) on JSBin: http://emberjs.jsbin.com/tefoka/
## How to reproduce the bug
3. click link "Go to Foo"
4. click "OK" button on alert popup
5. open Firefox Web Console
6. click link "Go to Tefoka"
7. click the browser back button to navigate back to FooRoute
8. click "OK" button on alert popup
9. see Web Console output

### Expected result
In Web Console, "start FooRoute#model" should be printed after "end
TefokaRoute#willTransition"

### Actual result
In Web Console, "start FooRoute#model" is printed before "end
TefokaRoute#willTransition"

## What I've found

### Observation
This bug depends on:
- Firefox window.alert/confirm/prompt
- Ember location: 'history'

### Guesses
This bug might be caused by:
- Firefox alert/confirm/prompt window
- Firefox History API
- Ember Location API
