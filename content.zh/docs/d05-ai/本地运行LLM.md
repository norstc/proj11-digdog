---
title: "本地运行LLM"
weight: 2
---

摘自 InfoWorld

本地运行LLM的5中方法

## 硬件要求

- DELL PC Inter I9 CPU, 64GB RAM, Nvidia GeForce 12GB GPU
- Mac M1 chip, 16GB RAM
- HP desktop, windows10

## 0: 使用ollama

下载：https://ollama.com/download/windows

安装 ，没有提示选项， 直接安装到c盘， 安装完后会自动运行

打开控制台

```shell
C:\Users\zhangjh>ollama
Usage:
  ollama [flags]
  ollama [command]

Available Commands:
  serve       Start ollama
  create      Create a model from a Modelfile
  show        Show information for a model
  run         Run a model
  pull        Pull a model from a registry
  push        Push a model to a registry
  list        List models
  ps          List running models
  cp          Copy a model
  rm          Remove a model
  help        Help about any command

Flags:
  -h, --help      help for ollama
  -v, --version   Show version information

Use "ollama [command] --help" for more information about a command.
```

浏览器打开 http://127.0.0.1:11434/

可以看到 Ollama is running

运行 llama3 模型： 参考：https://ollama.com/library/llama3

命令行直接运行， ollama会自动拉取最新的模型， 截至2024、5、17号，目前是基本的8b模型， 大小4.7GB

```shell
ollama run llama3
```

后台日志文件： C:\Users\zhangjh\AppData\Local\Ollama\server.log

安装完后提示：

```shell
C:\Users\zhangjh>ollama run llama3
pulling manifest
pulling 00e1317cbf74... 100% ▕████████████████████████████████████████████████████████▏ 4.7 GB
pulling 4fa551d4f938... 100% ▕████████████████████████████████████████████████████████▏  12 KB
pulling 8ab4849b038c... 100% ▕████████████████████████████████████████████████████████▏  254 B
pulling 577073ffcc6c... 100% ▕████████████████████████████████████████████████████████▏  110 B
pulling ad1518640c43... 100% ▕████████████████████████████████████████████████████████▏  483 B
verifying sha256 digest
writing manifest
removing any unused layers
success
>>> Send a message (/? for help)
```

此时可以输入对话， llama3 会自动响应， 比如 输入 Hello

```shell
C:\Users\zhangjh>ollama run llama3
pulling manifest
pulling 00e1317cbf74... 100% ▕████████████████████████████████████████████████████████▏ 4.7 GB
pulling 4fa551d4f938... 100% ▕████████████████████████████████████████████████████████▏  12 KB
pulling 8ab4849b038c... 100% ▕████████████████████████████████████████████████████████▏  254 B
pulling 577073ffcc6c... 100% ▕████████████████████████████████████████████████████████▏  110 B
pulling ad1518640c43... 100% ▕████████████████████████████████████████████████████████▏  483 B
verifying sha256 digest
writing manifest
removing any unused layers
success
Hello! It's nice to meet you. Is there something I can help you with, or would you like to chat?
```

由于系统没有显卡， 可以看到处理速度很慢

比如再问一下： do you know how to play witch says color

llama3 会返回：

```shell
>>> do you know how to play witch says color
What a fun game!

Yes, I do know how to play "Witch Says" (also known as "Simon Says"). Here's how:

1. One person (the "Witch") thinks of a color and says something like "Green is...".
2. If the Witch says a color correctly, the next person in line must also say the same color.
3. However, if the Witch doesn't say "Color" at the start of the sentence (e.g., just "Green..."), it's not a
valid move, and anyone who tries to repeat the color will be out of the game.
4. If someone repeats an invalid move, they're out too.
5. The game continues until only one person is left, who becomes the new Witch.

Want to play? I can start by saying a color!

>>> Send a message (/? for help)
```

帮助信息

```shell
>>> /?
Available Commands:
  /set            Set session variables
  /show           Show model information
  /load <model>   Load a session or model
  /save <model>   Save your current session
  /clear          Clear session context
  /bye            Exit
  /?, /help       Help for a command
  /? shortcuts    Help for keyboard shortcuts

Use """ to begin a multi-line message.

>>> Send a message (/? for help)
```

显示模型信息

```shell
>>> /show info
Model details:
Family              llama
Parameter Size      8B
Quantization Level  Q4_0

>>> Send a message (/? for help)
```

使用api

```shell
curl  -d '{"model":"llama3","prompt":"hello"}' http://127.0.0.1:11434/api/generate
```

退出 /bye

```shell
>>> /?
Available Commands:
  /set            Set session variables
  /show           Show model information
  /load <model>   Load a session or model
  /save <model>   Save your current session
  /clear          Clear session context
  /bye            Exit
  /?, /help       Help for a command
  /? shortcuts    Help for keyboard shortcuts

Use """ to begin a multi-line message.

>>> /bye

C:\Users\zhangjh>
```

## 1：使用GPT4ALL

## 2：使用LLM 命令行

## 3：使用h2oGPT

## 4：使用PrivateGPT

## 5：其他

### Jan

### Chat with RTX

### llmafile

### LocalGPT

### LM Studio

### LangChain
