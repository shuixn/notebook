这是一篇翻译，原文[Modding Minecraft with PHP – Buildings from Code!](https://www.sitepoint.com/modding-minecraft-with-php-buildings-from-code/)

## 正文

我一直想改造《我的世界》，遗憾的是，对于重新学习Java（这款游戏用Java写的），我一点兴趣也没有，直到最近，尽管这看起来是必须的。

![](/images/并发编程/使用PHP修改《我的世界》——代码篇/20161028143452958)

多亏了顽强的毅力，在没有真正熟知Java的情况下，我发现了一种修改《我的世界》的方法。有一些技巧和注意事项可以让我们使用高效简单的PHP去实现所有我们想要的修改。

这只是此次探险之旅的一半，在另一篇文章中[《Building a JavaScript 3D Minecraft Editor》](https://www.sitepoint.com/javascript-3d-minecraft-editor/)，我们将看到一个整洁的立体（3D）的使用JavaScript构造的《我的世界》编辑器。如果这听起来像是你想要学习的东西，一定要看看这个帖子。

本教程中大部分代码可以在[tutorial-php-minecraft-mod](https://github.com/assertchris-tutorials/tutorial-php-minecraft-mod)中找到，我已经在最新版本的Chrome测试了所有的JavaScript代码，和在PHP 7.0中测试了所有的PHP代码。我不能保证它会在其他浏览器看起来完全相同，或在其他版本的PHP中同样奏效，但是核心的概念是一样的。

## 准备工作

正如你将看到的，我们将在PHP和《我的世界》服务器之间传递数据包。我们需要一个脚本来运行我们需要的插件功能。我们可以使用传统的while循环来实现。

```
while (true) {
    // listen for player requests
    // make changes to the game

    sleep(1);
}
```

...或许我们可以做一些更有趣的事情。

我非常喜欢AMPHP。这是一个异步的PHP库的集合，包括HTTP服务器，客户端和一个事件循环。别担心，如果你不熟悉这些，我们慢慢来。

首先创建一个文件，这个文件包含一个事件循环和一个监听事件的函数。在此之前，我们需要安装事件循环和文件系统依赖库：

```bash
composer require amphp/amp
composer require amphp/file
```

然后，启动一个事件循环，并检查以确保按预期运行：

```php
require __DIR__ . "/vendor/autoload.php";

Amp\run(function() {
    Amp\repeat(function() {
        // listen for player requests
        // make changes to the game
    }, 1000);
});
```

这类似于无限循环，除了一点，它是非阻塞的。这意味着我们能执行更多的并发操作，在等待操作的时候，通常会阻塞这个进程。

## 谈谈Promises

除了这个包装器代码，AMPHP还提供了一个简洁的基于promise的接口。你可能已经熟悉这个概念（来自JavaScript），但这里有一个简单的例子：

```php
$eventually = asyncOperation();

$eventually
    ->then(function($data) {
        // do something with $data
    })
    ->catch(function(Exception $e) {
        // oops, something went wrong!
    });
```

Promises是一种表示我们还没有的数据的方式 - 最终值。它可能是一些比较慢的操作（如文件系统操作或HTTP请求）。

关键在于，我们不能立即获得最终值。取代在前台等待结果（一般来说会阻塞进程）的方式是，我们在后台等待它。当在后台等待时，我们可以在前台进行其他有意义的工作。

在AMPHP中应用Promises的进阶是，使用[生成器](https://medium.com/async-php/co-operative-php-multitasking-ce4ef52858a0#.xuc45ucsk)。在我看来这是一个有点过激的解释，请原谅我。

生成器是迭代器的语法简化。也就是说，它们减少了我们需要写入的代码量，以便对数组中尚未定义的值进行迭代。此外，它们使得可以将数据发送到生成这些值的函数（在生成它们时）。对这个模式有点感觉没？

生成器允许我们根据需要构建下一个数组条目。Promises代表最终值。 因此，我们可以重新使用生成器来生成步骤（或行为）的列表，这些步骤根据需要执行。

通过看一些代码可能更容易理解：

```php
use Amp\File\Driver;

function getContents(Driver $files, $path, $previous) {
    $next = yield $files->mtime($path);

    if ($previous !== $next) {
        return yield $files->get($path);
    }

    return null;
}
```

让我们考虑一下如何在同步执行中工作：

1. 调用 getContents
2. 调用 \$files->mtime($path) (想象这只是一个对filemtime的代理)
3. 等待filemtime返回
4. 调用 \$files->get($path) (想象这只是一个对file_get_contents的代理)
5. 等待 file_get_contents 返回

有了Promises，我们可以避免阻塞，代价是几个新的闭包：

```php
function getContents($files, $path, $previous) {
    $files->mtime($path)->then(
        function($next) use ($previous) {
            if ($previous !== $next) {
                $files->get($path)->then(
                    function($data) {
                        // do something with $data
                    }
                )
            }

            // do something with null
        }
    );
}
```

由于promises是可链式调用的，我们可以再简化一下：

```php
function getContents($files, $path, $previous) {
    $files->mtime($path)->then(
        function($next) use ($previous) {
            if ($previous !== $next) {
                return $files->get($path);
            }

            // do something with null
        }
    )->then(
        function($data) {
            // do something with data
        }
    );
}
```

我不知道你怎么看，但这似乎对我来说很乱。那么生成器如何适应这种情况？那么，AMPHP使用yield关键字来鉴定promise。让我们再看看getContents函数：

```php
function getContents(Driver $files, $path, $previous) {
    $next = yield $files->mtime($path);

    if ($previous !== $next) {
        return yield $files->get($path);
    }

    return null;
}
```

\$files-> mtime（\$path）返回一个promise。而不是等待查找完成，函数停止运行，因为它遇到yield关键字。过一段时间后，通知AMPHP启动操作完成，并恢复该功能。

然后，如果时间戳不匹配，\$files->get（\$path）获取内容。这是另一个阻塞操作，因此yield会再次暂停该函数。当读取文件时，AMPHP将再次启动此函数（返回文件内容）。

此代码看起来类似于同步替代，但是（显式地）使用promises和生成器使其无阻塞。

AMPHP与Promises A +规范略有不同，因为AMPHP Promises不支持then方法。其他PHP实现方法有，如React / Promise和Guzzle Promises。 重要的是理解promises的核心原理，以及它们如何与生成器对接，以支持这种简洁的异步语法。

## 监听日志

上次我写了关于《我的世界》的文章，是关于使用《我的世界》房子的门来触发现实世界的警报。在这篇文章中，我们简要地介绍了从《我的世界》服务器中获取数据的过程，并将其导入PHP。

这一次我们花了一点时间来完成，但我们基本上是在做同样的事情。让我们来看看识别玩家命令的代码:

```php
define("LOG_PATH", "/path/to/logs/latest.log");

$files = Amp\File\filesystem();

// get reference data

$commands = [];
$timestamp = yield $filesystem->mtime(LOG_PATH);

// listen for player requests

Amp\repeat(function() use ($files, &$commands, &$timestamp) {
    $contents = yield from getContents(
        $files, LOG_PATH, $timestamp
    );

    if (!empty($contents)) {
        $lines = array_reverse(explode(PHP_EOL, $contents));

        foreach ($lines as $line) {
            $isCommand = stristr($line, "> >") !== false;
            $isNotRepeat = !in_array($line, $commands);

            if ($isCommand && $isNotRepeat) {
                // execute mod command

                array_push($commands, $line);

                print "executing: " . $line . PHP_EOL;
                break;
            }
        }
    }
}, 500);
```

我们从获取引用文件的时间戳开始。我们用它来计算出文件是否发生了变化（在``getContents``函数中）。我们还创建了一个空列表，在这个列表中存储所有我们已经执行过的命令。这个列表可以帮助我们避免重复执行同样的命令。

您需要将``/path/to/logs/latest.log``替换为您的《我的世界》服务器的日志文件的路径。我建议独立运行《我的世界》服务器，它应该把``logs/latest.log``放在根目录下。

我们已经告诉``Amp\repeat``每隔``500``毫秒运行一次这个闭包。在这段时间内，我们检查文件的变化。如果时间戳发生了变化，我们会将日志文件的行拆分成一个数组，并将数组反序（这样我们就可以首先读取最新的日志内容）。

如果一行中包含">>"（就像玩家输入">某条命令"一样），我们假设该行中包含一条命令指令。

![](/images/并发编程/使用PHP修改《我的世界》——代码篇/20161028144548441.jpg)

## 创建蓝图

《我的世界》中最耗时的事情之一是建造大型结构。如果我可以将它们规划出来 (使用一些华丽的 3D JavaScript 建构器)，然后用一个特殊的命令将它们放置在世界中，那就容易多了。

我们可以使用一个稍作修改的[版本](https://codepen.io/assertchris/full/dpVQdY)，我在前面提到的其他帖子中提到的构建器，生成一个自定义块放置的列表：

![](/images/并发编程/使用PHP修改《我的世界》——代码篇/20161028144623285.jpg)

目前，这个构建器只允许放置土块。它生成的数组结构是每个放置的土块的x、y、z坐标（初始场景渲染后）。我们可以将其复制到我们一直在研究的PHP脚本中。我们还应该弄清楚如何标识一个确切的命令来构建我们设计的结构。

```php
$isCommand = stristr($line, "> >") !== false;
$isNotRepeat = !in_array($line, $commands);

if ($isCommand && $isNotRepeat) {
    array_push($commands, $line);
    executeCommand($line);
    break;
}

// ...later

function executeCommand($raw) {
    $command = trim(
        substr($raw, stripos($raw, "> >") + 3)
    );

    if ($command === "build") {
        $blocks = [
            // ...from the 3D builder
        ];

        foreach ($block as $block) {
            // ... place each block
        }
    }
}
```

每次接收到一个命令，我们可以将其传递给``executeCommand``函数。在那里，我们从第二个``>``提取到行的末尾。此刻我们只需要识别构建命令就可以了。

## 服务器通信

监听日志是一回事，但是我们如何与服务器通信？独立服务器启动管理聊天服务器（称为RCON）。这是相同的管理其他游戏的聊天服务器，，如反恐精英。

结果有人已经建立了一个RCON客户端（虽然阻塞），最近我写了一个很好的包装。 我们可以安装：

监听日志是一回事，但我们如何与服务器进行通信回传呢？单机服务器启动一个管理员聊天服务器（称为RCON）。与其他游戏中使用``MODs``的管理员聊天服务器类似，比如``Counter-Strike``。

原来已经有人建立了一个RCON客户端（尽管是阻塞的），最近我为此写了一个不错的包装器。我们可以用它来安装。

```bash
composer require theory/builder
```

这个库很大，我很抱歉。我附上了一个版本的《我的世界》单机版服务器，这样我就可以为这个库建立自动测试了。真是太匆忙了.....

我们需要配置我们的独立服务器，以便我们可以对其进行RCON连接。在``server.properties``文件中添加以下内容，放在与服务器jar相同的文件夹中。

```vim
enable-query=true
enable-rcon=true
query.port=25565
rcon.port=25575
rcon.password=password
```

重新启动后，我们应该能够使用类似于以下代码连接到服务器：

```php
$builder = new Client("127.0.0.1", 25575, "password");
$builder->exec("/say hello world");
```

我们可以改进我们的``execute Command``函数来构建一个完整的结构：

```php

function executeCommand($builder, $raw) {
    $command = trim(
        substr($raw, stripos($raw, "> >") + 3)
    );

    if (stripos($command, "build") === 0) {
        $parts = explode(" ", $command);

        if (count($parts) < 4) {
            print "invalid coordinates";
            return;
        }

        $x = $parts[1];
        $y = $parts[2];
        $z = $parts[3];

        $blocks = [
            // ...from the 3D builder
        ];

        $builder->exec("/say building...");

        foreach ($blocks as $block) {
            $dx = $block[0] + $x;
            $dy = $block[1] + $y;
            $dz = $block[2] + $z;

            $builder->exec(
                "/setblock {$dx} {$dy} {$dz} dirt"
            );

            usleep(500000);
        }
    }
}
```

新改进后的``executeCommand``函数，会检查输入命令（类似于``<player_name>> > build``的消息）是否以 "build"开头。

如果builder是无阻塞的，那么使用``yield new Amp\Pause(500)``，而不是``usleep(500000)``会更好。我们还需要把``executeCommand``作为一个生成函数来对待（在调用它的地方），也就是使用``yield executeCommand(...)``。

如果这样写，那么这个命令就会被空格分割，得到设计应该建立的位置的``x、y、z``的坐标。然后，它会获取我们从设计器中生成的数组，最好把每个块放在世界中。

![](/images/并发编程/使用PHP修改《我的世界》——代码篇/20161028144649786.jpg)

## 启发

你可以想象的到，可能会有许多有趣的扩展将从这个我们刚刚创建的简单的类似的脚本中诞生。设计者可以自行扩展，创建由许多不同种类和配置块组成的架构。

这个插件脚本可以扩展为通过JSON API接收更新，以便设计人员可以提交自己命名的设计，并且构建命令可以精确指定玩家想要构建的设计。