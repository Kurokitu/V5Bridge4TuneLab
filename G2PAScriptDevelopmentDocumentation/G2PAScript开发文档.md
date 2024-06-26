# G2PAScript 格式编写说明

## 1. 语法

G2PAScript 的语法基于 Lua，通过 Lua 代码实现可编程定制的 G2PA 自定义功能。语法解释器版本为 LuaJIT (Lua 5.1)。

## 2. 安装

G2PAScript 的安装位置根据用法不同有两种，一种是私有部署，一种是公共部署。

- G2PAScript 后缀名是“.lua”
- 公共部署

  - 部署位置在 %USERPROFILE%\\.TuneLab\\VOCALOID5\\G2PAScripts 目录。
  - 适用于全部声库，默认可被全部声库使用。
  - 当文件名带有语言尾缀(\_JPN、\_ENG、\_ESP、\_KOR、\_CHN)时，G2PAScript 将限定只能用于指定语种的声库。
  - G2PAScript 的文件名称就是显示名称，但语言尾缀会被去除。

- 私有部署

  - 部署位置在声库的安装目录，与*.ddi 和*.ddb 文件处在同一文件夹。
  - 适用于指定用于某个声库作为其使用对象，被某个声库专用。
  - 私有 G2PAScript 通常只有一个，作为声库的默认语言解析器。
  - 当具备多个私有 G2PAScript 时，以文件名正序排列第一个 G2PAScript 为默认。其余将以文件名作为显示名称列表待选。

## 3. 歌词解析顺序

VOCALOID 的每个声库都有自身默认的语言解析器(G2PA)，G2PAScript 的歌词发音匹配优先于这个语言解析器。

通常情况下，TuneLab 宿主会优先尝试使用 G2PAScript 解析发音符号，如果解析失败则会调用语言解析器继续解析。

## 4. G2PAScript 文件格式

### 4.1 脚本标识

为了快速的判断 lua 文件是否是 G2PAScript 脚本，G2PAScript 文件开头顶格应有脚本标识字符串。标识字符串格式是:

```
--G2PAScript
```

字符串头尾和中间无空格，不区分大小写。

- 具备 G2PAScript 脚本标识的 G2PAScript 文件应当同时具有入口函数，否则将无法运行。

### 4.2 入口函数

G2PAScript 只有一个入口函数，函数原型是:

```
string do_g2pa(string lyric)
```

- 对于输入变量，内容是音符(Note)所输入的歌词，类型是 string，头尾无额外空格和换行。
- 对于输出变量，内容是以空格分割的发音符号(PhonemeSymbol)，类型是 string，头尾无额外空格和换行。

### 4.3 功能函数

G2PAScript 额外提供一个用来调用系统自带语言解析器的功能函数，函数原型是:

```
string get_lang_phoneme(int langid, string lyric)
```

- 输入变量 langid，类型为整形，值域为 0-4，具体数值定义如下：

  - 0.日语
  - 1.英语
  - 2.朝鲜语
  - 3.西班牙语
  - 4.中文普通话

- 对于输入变量 lyric，内容是音符(Note)所输入的歌词，类型是 string，头尾无额外空格和换行。
- 对于输出变量，内容是以空格分割的发音符号(PhonemeSymbol)，类型是 string，头尾无额外空格和换行。

此函数可以调用系统解析器解析默认发音符号，常用于跨语种的发音符号处理。

### 4.4 系统函数

出于安全性考虑，程序对部分系统函数功能进行了限制。主要限制如下：

- os 库被禁止使用。
- io 库中的 open 函数，只允许使用读取模式打开文件。

## 5. 示例代码

```
--G2PAScript

-- 定义字典
phns={}
phns[“chang”]=”ts_h AN”
phns[“chong”]=”ts_h UN”

-- 入口函数
function do_g2pa(lyric)
    -- 查看是否在字典，如果在则返回发音
    if phns[lyric] then
        return phns[lyric]
    end
    -- 不在字典，查询日语发音并返回(0代表日本语)
    return get_lang_phoneme(0,lyric)
end
```

## 6. 调试

- G2PAScript 使用 Lua 调试器调试，调试器下载地址：

[https://github.com/rjpcomputing/luaforwindows/blob/master/files/lua5.1.exe](https://github.com/rjpcomputing/luaforwindows/blob/master/files/lua5.1.exe)

- 使用如下命令启动调试(请将 g2pascript.lua 替换为你的脚本名)

```
lua5.1.exe -i g2pascript.lua
```

- 在交互式命令行中，执行歌词转换(请将 lyric 替换为你要测试的歌词)

```
print(do_g2pa('lyric'))
```

- 其他事宜
  - Lua 调试器只对脚本运行调试，行首“脚本标识”不在调试范围
  - 只要是 Lua 运行器都可以调试，不限于给定的调试器下载地址。但请尽可能使用 LuaJIT 或 5.1 版本的程序，因为不同的版本可能会导致语法差异。
  - 如果脚本使用到了 get_lang_phoneme，调试器无法调试这个函数，请自行模拟函数返回。
