sh
=======

Tiny lit module to manage shell commands (Modified from [zserge/luash](https://github.com/zserge/luash)).

### Install

```sh
$ lit install james2doyle/sh
```

### Usage

Unlike [zserge/luash](https://github.com/zserge/luash), we do not expose anything to `_G`. Instead this module just uses the `sh.command` function directly.

**Simple Usage**

The `sh.command` function returns a function. Calling the returning function of `sh.command`, runs the command.

```lua
local sh = require('sh')

local e = sh.command('echo', 'hello world')
print(e()) -- prints "hello world"
```

You can nest commands inside each other.

```lua
local sh = require('sh')

local sedecho = sh.command('sed', 's/world/Lua/g')(sh.command('echo', 'hello', 'world')())
print('output:', sedecho) -- prints "output: hello Lua"
```

**Full Examples**

```lua
local sh = require('sh')

-- any shell command can be called as a function
print('User:', sh.command('whoami')())
print('Current directory:', sh.command('pwd')())

-- commands can be grouped into the pipeline as nested functions
local bin = sh.command('wc')(sh.command('ls', '-l', '/bin')())
print('Files in /bin:', bin)
local usrbin = sh.command('wc')(sh.command('ls', '-l', '/usr/bin')())
print('Files in /usr/bin:', usrbin)
local bothbins = sh.command('wc')(sh.command('ls', '-l', '/usr/bin')(), sh.command('ls', '-l', '/bin')())
print('files in both /usr/bin and /bin:', bothbins)

-- intermediate output in the pipeline can be stored into variables
local sedecho = sh.command('sed', 's/world/Lua/g')(sh.command('echo', 'hello', 'world')())
print('output:', sedecho)
print('exit code:', sedecho.__exitcode)
local res = sh.command('tr', '[[:lower:]]', '[[:upper:]]')(sedecho)
print('output+tr:', res)

-- command functions can be created dynamically. Optionally, some arguments
-- can be prepended (like partially applied functions)
local e = sh.command('echo')
local greet = sh.command('echo', 'hello')
print(e('this', 'is', 'some', 'output'))
print(greet('world'))
print(greet('foo'))
```
