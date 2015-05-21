# an Ember bug demo

## How to reproduce the bug
1. run this app with Python -m SimpleHTTPServer or something
2. go to the URL( e.g. http://localhost:8000) of index.html in Firefox( mine is version 38.0.1)
3. click link "Go to Foo"
4. click "OK" button on alert popup
5. open Firefox Web Console( Option-Command-K) and clean existing
   messages for better viewing the bug
6. click link "Go to Index"
7. click the browser back button to navigate back to FooRoute
8. click "OK" button on alert popup
9. see Web Console output

### Expected result
In Web Console, "start FooRoute#model" should be printed after "end
IndexRoute#willTransition"

### Actual result
In Web Console, "start FooRoute#model" is printed before "end
IndexRoute#willTransition"

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
