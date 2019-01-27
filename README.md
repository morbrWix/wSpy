# wSpy:  
1. [Introduction](#introduction)  
2. [Getting Started](#getting-started)
3. [Usage](#usage)
4. [Debugging](#debugging)
5. [Troubleshooting](#troubleshooting)
6. [Contributing](#contributing)

## Introduction
Smart log for sync and async operation and operation queues with stack-traces for debugging.

wSpy also helps you debug non-debuggable interactions, like drag, long hovering etc.

## Getting Started:
1. Add wSpy to your project:  
    ```
    $ npm i --save wspy
    ```
    or:
    ```
    $ yarn add wspy
    ```
2. Require `wSpy` in your file:  
    ```
    const wSpy = require('wspy')
    ```
3. Add logs to main points in your code:
    ```
    wSpy.log(logName, [functionName, arguments])
    ```


## Usage

#### Logging system
With wSpy you can log wherever you want.
  
Each log gives you the following:
* Log name
* Log index  
* Memory usage
* Source (stacktrace)
* Time (seconds and ms)
* Record (you can log arguments for functions and view them in each log)

wSpy works with several platforms: Node, browser and worker.

#### wSpy settings:
Define the settings for wSpy config in your code. If not defined, wSpy will use it's own default config:
```
{
    includeLogs: '',             // Default logs for wSpy. Must be defined when initiating!
    moreLogs: '',                // Logs that will only work if defined in the wSpy param
    extraIgnoredEvents: [],      // Events not to log
    MAX_LOG_SIZE: 10000,         // Maximum log count. When reached, wSpy will start deleting each log to make room for the next ones
    DEFAULT_LOGS_COUNT: 300,     // Default count for grouped, purge and recent functionalities.
    GROUP_MIN_LEN: 5,            // Default min length for grouped functionality.
    stackFilter: /wSpy/i         // Stacktrace filter using regex. Recommended when working with minified libraries: react-dom, require.min.js etc.
}
```

In your code, define the settings that you wish to override, for example:
```
const wSpySettings = {
    includeLogs: 'mainPointSystem,actionRunner',
    stackFilter: /wSpy|react-dom|bundle.js|min.js/i
}
```
#### Browser:
wSpy would be activated if you have wSpy param in your url. The values are `moreLogs` from wSpy config above, for example:  

```
wSpy=moreLog1,moreLog2
```

The logs in the url 

You can also omit some logs with `-logName`:

```
wSpy=-log1,-log2
```
Initiate with:

```
const wSpy = require('wspy').initBrowserHost({ settings })
```

#### Node:
In node host there's no url for the wSpy param, so you must init wSpy by using an override param:
```
const wSpyOverrideParam = '&wSpy=moreLog1,-logName2'
```
Initiate wSpy:
```
const wSpy = require('wspy').initNodeHost({ settings, wSpyOverrideParam })
```

#### Logging
```
.
.
.
function mainPointSystem(arg1, arg2) {
    wSpy.log('mainPointSystem', ['mainPointDef', ...arguments])
    .
    .
    .
}
```
#### Actions logging:
In modern system there are queues and runners for sync / async functions and handlers. `wSpy` gives you a way to log each function registration and execution:
  
ActionQueue:
```
.
.
.
function actionQueueRegistration(action) {
    wSpy.logCallBackRegistration(action, 'registerAction', [action.name || action], 'ActionQueue')
    .
    .
    .
}

function actionQueueExecution(action) {
    wSpy.logCallBackExecution(action, 'runAction', [action.name || action], 'ActionQueue')
    .
    .
    .
}
```

## Troubleshooting
Whenever wSpy has failed loading, it returns `noopSpy` - and log on your production code won't do anything (either log or break).

Keep in mind for each new log you're adding, to add the logName to `moreLogs` in your wSpy settings config.

Log only when needed, making a lot of logs will slow your system down when debugging (of course not on production when wSpy isn't activated), and reviewing huge amount of logs isn't efficient.  

## Debugging
When you have finished adding logs in your system, you can run your code (add the url param if needed) and see your system logs. For example, on browser you can search for the wSpy variable in the console with `wSpy.logs`.

#### Review logs:

* Review your mainPointSystem logs: `wSpy.logs.mainPointSystem`
* Review whole wSpy logs: `wSpy.merged()`
* Most of the time you'll have alot of logs, and for this matter we have `search`:
  - `wSpy.search('layout')` - would return any logs with the mentioned layout (logNames, stacktraces)
  - `wSpy.search(/layout|render/)` - search also supports regexes
* Get recent `x` logs:
  - `wSpy.recent(50)` - return the last 50 logs.
* Get grouped logs:
  - `wSpy.grouped()` - 
* Get purged logs:
  - `wSpy.purge(50)` - 

#### Clear logs:
When you have alot of logs and want to only view logs from a certain point, use `wSpy.clear()` in order to clear any log in wSpy.


## Contributing
Contributing is always welcome.
- Add an issue to [issues](https://github.com/wix-incubator/wSpy/issues)
- Fork wSpy and make pull request for us
- Please add tests, the package should always remain 100% covered with tests
