# code-runner


## Overview
code-runner is a command line application that reads code as a
json payload from stdin – compiles and runs the code – and writes
the result as json to stdout.
This is used by [glot-images](https://github.com/glotcode/glot-images) to run code on [glot.io](https://glot.io)
See the [overview](https://github.com/prasmussen/glot) on how everything is connected.

## Prerequisites
code-runner requires that the compiler / interpreter for the languages
you want to run is installed and is in PATH.

## Supported languages
- Assembly
- Ats
- Bash
- C
- Clojure
- Cobol
- CoffeeScript
- Cpp
- Crystal
- Csharp
- D
- Elixir
- Elm
- Erlang
- Fsharp
- Go
- Groovy
- Haskell
- Idris
- Java
- JavaScript
- Julia
- Kotlin
- Lua
- Mercury
- Nim
- Ocaml
- Perl
- Php
- Python
- Raku
- Ruby
- Rust
- Scala
- Swift
- TypeScript

## Input (stdin)
The input is required to be a json object containing the properties `language`
and `files`. `language` must be a lowecase string matching one of the supported
languages. `files` must be an array with at least one object containing the
properties `name` and `content`. `name` is the name of the file and can include
forward slashes to create the file in a subdirectory relative to the base
directory. All files are written into the same base directory under the OS's
temp dir.

In addition, one may optionally provide the `stdin` and `command` properties to
provide stdin data to the running code and to run the code with a custom command.
See examples below.

## Output (stdout)
The output is a json object containing the properties `stdout`, `stderr` and
`error`. `stdout` and `stderr` is captured from the output of the ran code.
`error` is popuplated if there is a compiler / interpreter error.
Note that the code-runner will exit with a non-zero code if invalid input is
given or if the files cannot be written to disk (permissions, disk space, etc).
No json will be written to stdout in those cases. Otherwise the exit code is 0.

## Examples

### Simple example
##### Input
```javascript
{
  "language": "python",
  "files": [
    {
      "name": "main.py",
      "content": "print(42)"
    }
  ]
}
```

##### Output
```javascript
{
  "stdout": "42\n",
  "stderr": "",
  "error": ""
}
```

### Read from stdin
##### Input
```javascript
{
  "language": "python",
  "stdin": "42",
  "files": [
    {
      "name": "main.py",
      "content": "print(input('Number from stdin: '))"
    }
  ]
}
```

##### Output
```javascript
{
  "stdout": "Number from stdin: 42\n",
  "stderr": "",
  "error": ""
}
```

### Custom run command
##### Input
```javascript
{
  "language": "bash",
  "command": "bash main.sh 42",
  "files": [
    {
      "name": "main.sh",
      "content": "echo Number from arg: $1"
    }
  ]
}
```

##### Output
```javascript
{
  "stdout": "Number from arg: 42\n",
  "stderr": "",
  "error": ""
}
```

### OJ example
##### Input
```javascript
{
  "language": "python",
  "stdin": "<parameters>",
  "fn_content": "<full code>",
  "class":"",
  "method":""
}
```

##### Output
输出以下信息：
- 函数的返回值，native code，输出至控制台，rust解析后  {"type":"fn_ret","content":""}
- 统计数据：内存、运行时间 ，来自native code /rust  {"type":"sta","content":"<json_string>"}
- 控制台输出，执行命令正常，控制台输出，需要过滤统计数据和函数返回 
- 运行错误(stderr)，运行执行命令出错，控制台的输出
- 编译错误(error)，运行编译命令出错，控制台的输出

```javascript
{
  "stdout": "42\n",
  "sta":"<time,mem>",
  "fn_result":"<result>",
  "stderr": "",
  "error": ""
}
```


## Todo

Goal: Not only can support the  running code, but also support online judges

**Feature**


- You can specify the method of running the function
- Convert stdin to function parameter
- Running data statistics, including running time
- The output result is the return value of the running function 





