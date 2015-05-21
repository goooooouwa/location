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

## Guesses
This bug might be caused by:
- Firefox alert/confirm/prompt window
- Firefox History API
- Ember Location API
