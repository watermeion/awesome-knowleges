# sed 学习
## What's Sed

### Linux

```
NAME
       sed - stream editor for filtering and transforming text

SYNOPSIS
       sed [OPTION]... {script-only-if-no-other-script} [input-file]...

DESCRIPTION
       Sed  is a stream editor.  A stream editor is used to perform basic text transformations on an input stream (a
       file or input from a pipeline).  While in some ways similar to an editor which permits scripted  edits  (such
       as  ed),  sed works by making only one pass over the input(s), and is consequently more efficient.  But it is
       sed's ability to filter text in a pipeline which particularly distinguishes it from other types of editors.
```

### Mac

```
NAME
     sed -- stream editor

SYNOPSIS
     sed [-Ealn] command [file ...]
     sed [-Ealn] [-e command] [-f command_file] [-i extension] [file ...]
```
## sed 能做什么？
  1. 能够替换，交换，插入，删除，追加。
  2. 能进行正则匹配
  3. 能进行循环，条件循环。
  4. 提供Hold Space，类似于变换缓存的概念。
  5. 可以与外界的文件进行交互。
等等

## sed 概念

## sed 详解
### sed 兼容性参数

```
[All]: Command in mac are called Script in linux.  

-n

              [Linux]:suppress automatic printing of pattern space
              [Mac]:By default, each line of input is echoed to the standard output after all of the commands have
                been applied to it.  The -n option suppresses this behavior.
              [Example]: sed -n '1p' 1.out
              [All]: -n 和p function 结合在一起可以实现只打印替换后的行数
-e command    
              [Linux]:  add the script to the commands to be executed
              [Mac]:    :Append the editing commands specified by the command argument to the list of commands. 
              [Example]: sed  -e'1p' -e '2p' intellij-soapui-workspace.xml
-f command-file
              [Linux]: add the contents of script-file to the commands to be executed
              [Mac]: Append the editing commands found in the file command_file to the list of commands.  The edit-
                    ing commands should each be listed on a separate line.
-i extension   also -i[suffix] in linux
              [Mac]:  Edit files in-place, saving backups with the specified extension.  If a zero-length extension
             is given, no backup will be saved.  It is not recommended to give a zero-length extension when
             in-place editing files, as you risk corruption or partial content in situations where disk
             space is exhausted, etc.
              [Linux]: edit files in place (makes backup if SUFFIX supplied)
```

Extended Regular Expression 

```
[All]: Extended Regular Expression is supported by Linux And Mac in diferent way. 
[Mac]:  -E     Interpret regular expressions as extended (modern) regular expressions rather than basic regu-
             lar expressions (BRE's).  The re_format(7) manual page fully describes both formats.
[Linux]: -r   use extended regular expressions in the script.
              
```


## 实例

### 行拼接

#### 例子和实现
看一复杂示例，一复杂文本有大于20行的代码，需要11-20 追加到 1-10行，11行放到1行，12行放到2行以此类推，超过20行的追加到拼接好的文本后面，
如何只使用sed命令实现, 假如文件问1.txt

```bash
sed -n '1,${s/.*/~#\0/;:cat; $!{N; b cat;}; ${s/\(\([^\n]*\n\)\{10\}\)\(.*\)/\1#\3/;s/.*/\0\n/;s/\n\n/\n/; :redo; /##/!{s/\([^#]*\)#\([^\n]*\)\n\([^#]*#\)\([^\n]*\n\)\(**\)/\1\2\t\4#\3\5/; t redo;}; s/~//;s/##//p;q;}};' < 1.txt
```

#### 解读

```bash
sed -n '       //静默
1,${        //匹配所有行， 可以没有
 s/.*/~#\0/; //用户区分拼接后的结果，拼接前的结果
 :cat; //label   b 无条件跳转 t 有条件跳转
  $!{ //文件没结束
        N;  //读取下一行
        b cat; //重复执行，这个块的左右就是重复读取所有行
  };
   ${                     //文件结尾
        s/\(\([^\n]*\n\)\{10\}\)\(.*\)/\1#\3/;//分开前10行和后边的行
        s/.*/\0\n/;  //处理末尾的换行
        s/\n\n$/\n/g;  // 处理掉末尾所有多余的行数
        :redo; //label
        /##/!{ //标志处理结束
         s/\([^#]*\)#\([^\n]*\)\n\([^#]*#\)\([^\n]*\n\)\(**\)/\1\2\t\4#\3\5/;  一次合并行 
          t redo; //如果有交换，继续执行，否则不执行
       };  
       s/~//; //删除~
       s/##//p; //删除###
     }
};' < 1.txt
```
注：本脚本，适合学习使用，实际用途不大，尤其是在文本较大的时候，可能造成内存溢出， 一种有效的方法是，限制读取的行数，将1，$改成1，20， 这样做具体的限制， 然后下面的$!, $分别修改为，1，20和20。

### 实现计数功能

#### 思路

功能将文件中的第n个a替换为b

``` shell
 sed '/a/{x;s/^/./;/^.\{3\}$/{x;s/a/b/;b};x}' urfile
```

#### 详解
``` shell
 /a/{                 #匹配时，开始记数
                x                #交换pattern space与hold space
                s/^/./           #向hold space打一个 .
                /^.\{3\}$/{      #判断 . 的个数是否达到要求
                        x            #如果达到要求，交换hold space与pattern space
                        s/a/b/       #进行替换
                        b            #跳转到代码结束
                }                #
                x                #交换hold space与pattern space
        }                    #
```
