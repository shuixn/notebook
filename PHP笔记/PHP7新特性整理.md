PHP7新特性整理
============

## 版本

本文基于PHP版本：7.0~7.1.x

## 新特性

### 全局

``define()`` 定义常量数组

```php
define('ANIMALS', [
    'dog',
    'cat',
    'bird'
]);

echo ANIMALS[1]; // 输出 "cat"
```

### 类

1. 匿名类：通过``new class``来实例化一个匿名类，可以创建一次性的简单对象

    ```php
    // PHP 7 之前的代码
    class Logger
    {
        public function log($msg)
        {
            echo $msg;
        }
    }
    
    $util->setLogger(new Logger());
    
    // 使用了 PHP 7+ 后的代码
    $util->setLogger(new class {
        public function log($msg)
        {
            echo $msg;
        }
    });
    ```
2. 常量可见性
   1. public（默认）
   2. protected
   3. private

    ```php
    class ConstDemo
    {
        const PUBLIC_CONST_A = 1;
        public const PUBLIC_CONST_B = 2;
        protected const PROTECTED_CONST = 3;
        private const PRIVATE_CONST = 4;
    }
    ```

### 函数

1. 类型声明

   参数

   | 标量类型         | 其他              |
   | ---------------- | ----------------- |
   | 字符串（string） | 类名（ClassName） |
   | 整型（int）      | 接口（interface） |
   | 浮点数（float）  | 数组（array）     |
   | 布尔值（bool）   | 回调（callable）  |
   | ----             | object            |
   | ----             | iterable伪类      |
   | ----             | Throwable         |

   返回值：支持上面的类型

   1. 引入void

2. 可为空类型Nullable（?）：返回特定类型或``NULL``
    ```php
    function testReturn(string $name, ?int $age): ?string {
        return $age ? "$name: $age" : $name;
    }
    ```
3. 新增函数
   1. 整数除法函数：intdiv
   2. 随机字符串：random_bytes
   3. 随机整数：random_int

4. 函数调整

    unserialize 提供过滤：这个特性旨在提供更安全的方式解包不可靠的数据。它通过白名单的方式来防止潜在的代码注入。
    
        ```php
        // 将所有的对象都转换为 __PHP_Incomplete_Class 对象
        $data = unserialize($foo, ["allowed_classes" => false]);
        
        // 将除 MyClass 和 MyClass2 之外的所有对象都转换为 __PHP_Incomplete_Class 对象
        $data = unserialize($foo, ["allowed_classes" => ["MyClass", "MyClass2"]);
        
        // 默认情况下所有的类都是可接受的，等同于省略第二个参数
        $data = unserialize($foo, ["allowed_classes" => true]);
        ```

### 运算符

1. null合并运算符（??）：如果变量存在且值不为**NULL**， 它就会返回自身的值，否则返回它的第二个操作数
    ```php
    // PHP5
    $name = isset($student['name']) && !empty($student['name']) ? $student['name'] : '';
    
    // PHP7
    $name = $student['name'] ?? '';
    ```
2. 太空船操作符（<=>）：用于比较两个表达式。当``$a``小于、等于或大于``$b``时它分别返回-1、0或1
    ```php
    // 整型
    print( 1 <=> 1);    // 0
    print( 1 <=> 2);    // -1
    print( 2 <=> 1);    // 1
    
    // 浮点型
    print( 1.5 <=> 1.5);    // 0
    print( 1.5 <=> 2.5);    // -1
    print( 2.5 <=> 1.5);    // 1
    
    // 字符串
    print( "a" <=> "a");    // 0
    print( "a" <=> "b");    // -1
    print( "b" <=> "a");    // 1
    ```

### 语言结构

- 生成器
   1. yield不再需要括号
    ```PHP
    $result = yield;
    $result = yield $v;
    $result = yield $k => $v;
    ```
   2. 返回表达式：``Generator::getReturn()``，只能在生成器完成产生工作以后调用一次
   ```php
   $gen = (function() {
       yield 1;
       yield 2;
    
       return 3;
   })();
    
   foreach ($gen as $val) {
       echo $val . ' ';
   }
    
   echo $gen->getReturn(), PHP_EOL;
    
   // 1 2 3
   ```
   3. 委派：只需在最外层生成其中使用 ``yield from``， 就可以把一个生成器自动委派给其他的生成器、traverable、array
   ```php
   function gen() {
       yield 1;
       yield 2;
    
       yield from gen2();
   }
    
   function gen2() {
       yield 3;
       yield 4;
       yield from [5, 6];
       yield from new ArrayIterator([7, 8]);
   }
    
   foreach (gen() as $val) {
       echo $val . ' ';
   }
   
   // 1 2 3 4 5 6 7 8
   ```
- 允许在克隆表达式上访问对象成员：``(clone $foo)->bar()``
- ``list``短数组语法（[]），可嵌套：``[$id, $name] = $student;``
    ```php
    $student = [1, 'bob'];

    [$id, $name] = $student;
    
    var_dump($id, $name);
    
    // int(1)
    // string(3) "bob"
    ```
    一个有趣的例子：交换两个变量
    ```php
    function swap( &$a, &$b ): void {
        [ $a, $b ] = [ $b, $a ];
    }
    
    $a = 1;
    $b = 2;
    
    swap($a, $b);
    
    echo $a . ' ' . $b; // 2 1
    ```
- ``list``元素顺序
    ```php
    $info = array('coffee', 'brown', 'caffeine');

    list($a[0], $a[1], $a[2]) = $info;
    
    var_dump($a);
    
    // php7
    array(3) {
      [0]=>
      string(6) "coffee"
      [1]=>
      string(5) "brown"
      [2]=>
      string(8) "caffeine"
    }
    
    // php5
    array(3) {
      [2]=>
      string(8) "caffeine"
      [1]=>
      string(5) "brown"
      [0]=>
      string(6) "coffee"
    }
    ```
- 带键的``list``
    ```php
    $data = [
        ["id" => 1, "name" => 'Tom'],
        ["id" => 2, "name" => 'Fred'],
    ];
    foreach ($data as ["name" => $name, "id" => $id]) {
        echo "id: $id, name: $name\n";
    }
    
    [1 => $second, 3 => $fourth] = [1, 2, 3, 4];
    
    echo "$second, $fourth\n";
    
    // id: 1, name: Tom
    // id: 2, name: Fred
    // 2, 4
    ```
- 断言：不符合预期将抛异常
    ```php
    ini_set('assert.exception', 1);

    class CustomError extends AssertionError {}
    
    assert(false, new CustomError('Some error message'));
    ```
- 命名空间：从同一 namespace 导入的类、函数和常量现在可以通过单个use 语句一次性导入
    ```php
    use some\namespace\{ClassA, ClassB, ClassC as C};
    use function_some\namespace\{fn_a, fn_b, fn_c};
    use const_some\namespace\{ConstA, ConstB, ConstC};
    ```

### 会话

lazy_write：默认开启

它的作用是控制 PHP 只有在会话中的数据发生变化的时候才写入会话存储文件，如果会话中的数据没有发生改变，那么 PHP 会在读取完会话数据之后， 立即关闭会话存储文件，不做任何修改，可以通过设置 ``read_and_close`` 来实现。

[cache_limiter](https://www.php.net/manual/zh/function.session-cache-limiter.php)指定会话页面所使用的缓冲控制方法（none/nocache/private/private_no_expire/public）。默认为 ``nocache``

```php
session_start([
    'cache_limiter' => 'private', // 允许客户端缓存， 但是不允许代理服务器缓存内容
    'read_and_close' => true,
]);
```

### 异常

1. 多异常捕获：``catch (FirstException | SecondException $e)``，用来处理不同类的不同异常
    ```php
    try {
        // some code
    } catch (FirstException | SecondException $e) {
        // handle first and second exceptions
    }
    ```
2. 异常捕获高级抽象（同时可捕获Error或Exception）：Throwable
    ```vi
    interface Throwable
    |- Exception implements Throwable
        |- ...
    |- Error implements Throwable
        |- TypeError extends Error
        |- ParseError extends Error
        |- ArithmeticError extends Error
            |- DivisionByZeroError extends ArithmeticError
        |- AssertionError extends Error
    ```

### 字符串

负数偏移：``"abcdef"[-2]``

```php
var_dump("abcdef"[-2]); // string (1) "e"
```

### PCNTL

``pcntl_async_signals()``，用于启用无需 ticks （这会带来很多额外的开销）的异步信号处理

#### PHP5

Tick（时钟周期）是一个在 declare 代码段中解释器每执行 N 条可计时的低级语句就会发生的事件。
 
不是所有语句都可计时。通常条件表达式和参数表达式都不可计时。

```php
# PHP5
declare(ticks = 1);

function sig_handler($sig){
    echo "SIGHUP\n";
}

pcntl_signal(SIGHUP,  "sig_handler");

posix_kill(posix_getpid(), SIGHUP);
```

这段代码在执行``pcntl_signal``前，先加入了``declare(ticks = 1)``。因为PHP的函数无法直接注册到操作系统信号设置中，所以pcntl信号需要依赖``ticks``机制。

``pcntl_signal``的实现原理是，触发信号后先将信号加入一个队列中。然后在PHP的``ticks``回调函数中不断检查是否有信号，如果有信号就执行PHP中指定的回调函数，如果没有则跳出函数。

也就是说PHP解释器每执行N条可计时的语句（低级指令）就会触发一个``ticks``事件。PHP中这种``ticks``触发信号处理函数的机制导致了PHP在对信号处理时有很大的缺陷，如果PHP中有造成阻塞的语句，由于语句无法执行结束，无法触发tick事件，信号处理函数也就不会被回调。

#### PHP7

```php
# PHP7
pcntl_async_signals(true); // turn on async signals

pcntl_signal(SIGHUP,  function($sig) {
    echo "SIGHUP\n";
});

posix_kill(posix_getpid(), SIGHUP);
```

### Socket

禁用 [TCP NAGLE](http://blog.chinaunix.net/uid-28387257-id-3766565.html) 算法：``tcp_nodelay=true``

当我们通过 TCP socket 分多次发送较少的数据时，对端可能会很长时间收不到数据，导致本端应用程序认为超时报错，这时可能是受到了``TCP NAGLE``算法的影响。

## 内核

### 变量

#### zval改动

PHP5-zval

```c
struct _zval_struct {
     union {
          long lval;
          double dval;
          struct {
               char *val;
               int len;
          } str;
          HashTable *ht;
          zend_object_value obj;
          zend_ast *ast;
     } value;
     zend_uint refcount__gc;
     zend_uchar type;
     zend_uchar is_ref__gc;
};
```

问题：

1. 这个结构体的大小是(在64位系统)24个字节, zend_object_value导致整个value需要16个字节。
2. 没有预留字段，如想存储一些zval相关的信息时，无法扩展使用。
3. zval大部分类型是按值传递，写时复制（``refcount__gc``）。而对象和资源类型是按引用传递，所以需要在全局加一个引用计数才能保证内存回收。
4. 大量重复的字符串计算无法存储起来。
5. 变量引用造成写时复制，在一些大数组拷贝的时候容易导致性能问题。
6. 临时变量在堆上分配zval（MAKE_STD_ZVAL/ALLOC_ZVAL），分配效率低，且对缓存不够友好。

PHP7-zval

```c
//zend_types.h
typedef struct _zval_struct     zval;

typedef union _zend_value {
    zend_long         lval;    //int整形
    double            dval;    //浮点型
    zend_refcounted  *counted;
    zend_string      *str;     //string字符串
    zend_array       *arr;     //array数组
    zend_object      *obj;     //object对象
    zend_resource    *res;     //resource资源类型
    zend_reference   *ref;     //引用类型，通过&$var_name定义的
    zend_ast_ref     *ast;     //下面几个都是内核使用的value
    zval             *zv;
    void             *ptr;
    zend_class_entry *ce;
    zend_function    *func;
    struct {
        uint32_t w1;
        uint32_t w2;
    } ww;
} zend_value;

struct _zval_struct {
    zend_value        value; //变量实际的value
    union {
        struct {
            ZEND_ENDIAN_LOHI_4( //这个是为了兼容大小字节序，小字节序就是下面的顺序，大字节序则下面4个顺序翻转
                zend_uchar    type,         //变量类型
                zend_uchar    type_flags,  //类型掩码，不同的类型会有不同的几种属性，内存管理会用到
                zend_uchar    const_flags,
                zend_uchar    reserved)     //call info，zend执行流程会用到
        } v;
        uint32_t type_info; //上面4个值的组合值，可以直接根据type_info取到4个对应位置的值
    } u1;
    union {
        uint32_t     var_flags;
        uint32_t     next;                 //哈希表中解决哈希冲突时用到
        uint32_t     cache_slot;           /* literal cache slot */
        uint32_t     lineno;               /* line number (for ast nodes) */
        uint32_t     num_args;             /* arguments number for EX(This) */
        uint32_t     fe_pos;               /* foreach position */
        uint32_t     fe_iter_idx;          /* foreach iterator index */
    } u2; //一些辅助值
};
```

64位环境下，现在只需要16个字节
1. value
2. 扩展字段
    - u1: 类型相关信息
    - u2：辅助字段

改动：

1. 能直接保存的值，不再进行引用计数，而是直接拷贝赋值，如``IS_LONG``。
2. 无值的类型，也不需要引用计数，如``IS_NULL``、``IS_TRUE``。
3. 需要引用计数的类型，如数组、对象，则通过另外的结构体保存，zval仅保存指针。
4. 引用计数信息保存在``zend_refcounted_h``结构。
5. zval预先内存分配不再从堆上申请，函数内部使用的zval要么来自外面输入, 要么使用在栈上分配的临时zval。

### ZendVM

>对语法的评价要基于其优点，而不是基于技术限制。

#### AST (Abstract Syntax Tree)

将 PHP 文件转换为 opcodes 的过程现在包括三个阶段。

1. 编码：从源代码中生成一个token流。
2. 解析：从token流生成抽象语法树。
3. 编译：从抽象语法树生成op_array。

### 线程安全

问题：尽可能的减少线程storage的检索

PHP5处理方式：**层层传递**。只检索一次，把已获取的storage指针传给接下来调用的函数用，其它函数再一级级往下传，这样一来各函数如果发现storage通过参数传进来了就直接用，无需再检索了。（TSRMLS_DC、TSRMLS_CC）

在函数参数中必须加上这两个宏，容易遗漏，较为丑陋。

#### TLS (Thread Local Storage)

通过GCC的内置``__thread``定义，这样各线程更新这个变量就不会冲突

可以用于修饰全局变量，函数内的静态变量，不能修饰函数的局部变量或者``class``的普通成员变量，且``__thread``变量值只能初始化为编译器常量。

### 内存管理

- chunk：2MB
- page: 4KB
- slot：8,16,24,32,...,3072B

#### HugePage

```c
static void *zend_mm_mmap(size_t size)
{
    ...

//hugepage支持
#ifdef MAP_HUGETLB
        if (zend_mm_use_huge_pages && size == ZEND_MM_CHUNK_SIZE) {
            ptr = mmap(NULL, size, PROT_READ | PROT_WRITE, MAP_PRIVATE | MAP_ANON | MAP_HUGETLB, -1, 0);
            if (ptr != MAP_FAILED) {
                return ptr;
            }
        }
#endif

    ptr = mmap(NULL, size, PROT_READ | PROT_WRITE, MAP_PRIVATE | MAP_ANON, -1, 0);

    if (ptr == MAP_FAILED) {
#if ZEND_MM_ERROR
        fprintf(stderr, "\nmmap() failed: [%d] %s\n", errno, strerror(errno));
#endif  
        return NULL;
    }
    return ptr;
}
```

>关于Hugepage是啥，简单的说下就是默认的内存是以``4KB``分页的，而虚拟地址和内存地址是需要转换的， 而这个转换是要查表的，CPU为了加速这个查表过程都会内建``TLB（Translation Lookaside Buffer）``， 显而易见如果虚拟页越小，表里的条目数也就越多，而TLB大小是有限的，条目数越多TLB的Cache Miss也就会越高， 所以如果我们能启用大内存页就能间接降低这个``TLB Cache Miss``。

## 参考

1. [从PHP 5.6.x 移植到 PHP 7.0.x](https://www.php.net/manual/zh/migration70.php)
2. [从PHP 7.0.x 移植到 PHP 7.1.x](https://www.php.net/manual/zh/migration71.php)
3. [PHP 7 错误处理](https://www.php.net/manual/zh/language.errors.php7.php)
4. [Throwable Exceptions and Errors in PHP 7](https://trowski.com/2015/06/24/throwable-exceptions-and-errors-in-php7/)
5. [PHP RFC: Abstract syntax tree](https://wiki.php.net/rfc/abstract_syntax_tree)
6. [PHP RFC: Native TLS](https://wiki.php.net/rfc/native-tls)
7. [深入理解PHP7内核之zval](https://www.laruence.com/2018/04/08/3170.html)
8. [让你的PHP7更快之Hugepage](http://www.laruence.com/2015/10/02/3069.html)
9. [《PHP7内核剖析》](https://github.com/pangudashu/php7-internal)