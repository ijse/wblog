---
ID: 51
post_title: Rime 输入法定制五笔
author: ijse
post_date: 2016-12-04 20:11:15
post_excerpt: ""
layout: post
permalink: >
  https://ijser.cn/config-rime-input-method/
published: true
---
Rime 是一款Hacker级的输入法，有非常强大的定制性及不错的性能，输入法家族的神一般的存在。
Rime 支持不同Win, Mac, Linux三大操作平台，并且分别有不一样的名字：中州韵，小狼毫，鼠须管。
之前主要用Ubuntu时，Rime是相比iBus, Fcitx 更好用的输入法，现在用Mac之后，也试过其它一些五笔输入法，都有一些不太方便的地方。

Rime没有图形化的配置界面，需要手动修改yml配置文件，但这基本不影响它的简洁强大。

### 主要目标：
 - [x] 五笔输入，编码反查
 - [x] 支持临时用拼音
 - [x] 中英文切换
 - [x] 输入特殊符号
 - [x] Shift编码直接上屏
 - [x] 输入Emoji表情
 - [x] 可以创建自定义词组
 - [x] 配置和字库同步

### 五笔输入， 编码反查
默认安装完Rime后是没有五笔输入法的，但添加非常简单：

修改`~/Library/Rime/default.custom.yml` 文件：

```yml 
patch:
    schema_list:
      - schema: wubi_pinyin
```

把其它无用的schema都删掉了。　　
这里也可以使用`wubi86`， 如果你只想输入五笔。
默认支持编码反查。

### 临时用拼音
因为用了wubi_pinyin 这套方案，所以是可以五笔拼音混输的。
另外，还发现Rime输入法是支持五笔整行输入的，只要不重码，可以一直输入完整段话。

### 输入特殊符号
在`symbols.yaml`文件中有定义一些特殊符号，同样可以像配置Emoji一样,将不同词库结合起来.
修改`wubi_pinyin.custom.yaml`文件，添加如下两行配置：
```yaml
    &#039;punctuator/import_preset&#039;: symbols
    &#039;recognizer/patterns/punct&#039;: &quot;^/([a-z]+|[0-9])$&quot;
```
比如，可以输入`/dn`（dian nao)，   ❖ ⌘
 
### Shift编码直接上屏

```
patch:
    ascii_composer/switch_key:
        Shift_L: commit_code
        Shift_R: commit_text
``` 

### 输入Emoji表情
默认输入Emoji是需要按`Ctrl + ~`进行切换的, 比较麻烦. Rime 的强大之处在于可以组合词库.

由于我们用的是wubi_pinyin这个词库, 因此创建一个`wubi_pinyin.custom.yaml`文件,在里面填入:

```yml
patch:
    schema/dependencies:
        - emoji
        - symbols
    reverse_lookup:
        dictionary: emoji
        enable_completion: false
        prefix: "`"
        tips: [emoji]
```

然后重新Deploy. 即可直接输入Emoji了, 非常方便. 😊

### 可以创建自定义词组
Rime 默认会自己学习词组, 像其它一些现代输入法一样, 陌生词只要输入一次, 下次就可以用简码输入了.

### 配置和字库同步

Rime 有简单的本地文件夹同步, 可以把它配置到iCloud上面.

修改`installation.yaml`文件, 添加:
```yaml
    sync_dir: "/Users/ijse/Library/Mobile Documents/com~apple~CloudDocs/Rime"
```

然后Deploy即可.