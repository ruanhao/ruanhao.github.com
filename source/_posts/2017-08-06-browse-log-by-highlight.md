---
layout: post
title: "在终端下分析日志文件"
date: 2017-08-06 19:59:36 +0800
comments: true
categories: utils
---

在桌面环境下，分析日志文件一般使用 TextEdit ，Sublime 等文本编辑工具，当需要查找特定的关键词时，Ctrl-F 或者 Command-F 即可。
可是这种方式多多少少有点低效，因为还是有很多内容需要用肉眼过滤，很容易看花眼。

习惯在 Linux 上进行开发的人，一般会使用 grep 过滤出感兴趣的内容，大大缓解了眼疲劳。比如：\\
1. 过滤某个关键词可以使用 `grep -i <keyword> xxx.log` ，{%highlight -i %} 表示忽略大小写\\
2. 过滤多个关键词可以使用 `grep '<kw1>\|<kw2>' xxx.log`\\
3. 反向过滤可以使用 {%highlight -v %} 选项\\
4. 将空行过滤掉可以使用 `grep . xxx.log`

总之，grep 的功能还是很强的，它也是我最喜欢的终端命令之一。\\
{%highlight 建议 %}：尽可能使用高亮模式 `grep --color=auto`，这样能更快的找到关键词，提升视觉享受。

很多时候分析日志文件时，需要查找某一行同时出现多个关键词，\\
这种应用场景在日志分析中出现的概率挺高的，比如需要找出等级为 INFO ，且同时出现 "exception" 关键词的日志，
当然，可以这样搞：

`grep INFO xxx.log | grep exception`

但这样关键词多起来，就要写很多管道了，看上去比较丑陋。可以写个脚本简化下：（如果不嫌弃，可以 source 我的偷懒脚本，内容附于文末）

`m -p INFO -p exception xxx.log`

这些功能，桌面环境并不常见，我觉得分析日志还是在终端下会舒服些，再配合 less ，emacs/vim ，事半功倍。

再安利个小脚本，还是上面那个例子，当搜索多个关键词时，如果能分别高亮那就再好不过了，对关键信息的过滤可以做到一目了然：

`m -p INFO -p exception xxx.log | h INFO exception`

效果如下：

{% img /images/examples/browse-log-under-term.png %}

如果你比我还懒的话，可以再搞个脚本把 m 和 p 的功能结合起来，又能少打很多字母了 {%gemoji smile %} 

## multi-search: 

``` sh
function m () { 
    function _usage () 
    { 
        echo "usage: COMMAND [-inHRp] -p<pattern1> -p<pattern2> <filename>";
        echo "-i : ignore case";
        echo "-n : show line number";
        echo "-H : show filename";
        echo "-h : show header";
        echo "-p : specify pattern";
        echo "-R : search recursively"
    };
    declare -a patterns;
    local ignorecase_flag recursive_flag filename linum header_flag colon result OPTIND;
    while getopts "iHhnRp:" opt; do
        case $opt in 
            i)
                ignorecase_flag=true
            ;;
            H)
                filename="FILENAME,"
            ;;
            n)
                linum="NR,"
            ;;
            p)
                patterns+=("$OPTARG")
            ;;
            h)
                header_flag=true
            ;;
            R)
                recursive_flag=true
            ;;
            \?)
                _usage;
                return
            ;;
        esac;
    done;
    if [[ -n $filename || -n $linum ]]; then
        colon="\":\",";
    fi;
    shift $(( $OPTIND - 1 ));
    if [[ $ignorecase_flag == true ]]; then
        for s in "${patterns[@]}";
        do
            result+=" && s~/${s,,}/";
        done;
        result=${result# && };
        result="{s=tolower(\$0)} $result";
    else
        for s in "${patterns[@]}";
        do
            result="$result && /$s/";
        done;
        result=${result# && };
    fi;
    result+=" { print "$filename$linum$colon"\$0 }";
    if [[ ! -t 0 ]]; then
        cat - | awk "${result}";
    else
        for f in "$@";
        do
            if [[ -d $f ]]; then
                if [[ $recursive_flag == true ]]; then
                    find -L $f -not -path '*/\.*' -type f -print0 2> /dev/null | while IFS= read -r -d '' ff; do
                        if file "$ff" | grep --color=auto --line-buffered -q "text"; then
                            [[ $header_flag == true ]] && echo "########## processing $ff ##########";
                            awk "${result}" "$ff";
                        fi;
                    done;
                fi;
            else
                if file "$f" | grep --color=auto --line-buffered -q "text"; then
                    [[ $header_flag == true ]] && echo "########## processing $f ##########";
                    awk "${result}" "$f";
                fi;
            fi;
        done;
    fi
}
```

## highlight: （需要先安装 [ack](https://beyondgrep.com/) )

``` sh
function h () { 
    function _usage () 
    { 
        echo "usage: YOUR_COMMAND | h [-idn] args...
	-i : ignore case
	-d : disable regexp
	-n : invert colors"
    };
    local _OPTS OPTIND;
    if test -t 0; then
        _usage;
        return;
    fi;
    while getopts ":idnQ" opt; do
        case $opt in 
            i)
                _OPTS+=" -i "
            ;;
            d)
                _OPTS+=" -Q "
            ;;
            n)
                n_flag=true
            ;;
            Q)
                _OPTS+=" -Q "
            ;;
            \?)
                _usage;
                return
            ;;
        esac;
    done;
    shift $(($OPTIND - 1));
    if (( ${#@} > 12)); then
        echo "Too many terms. h supports a maximum of 12 groups. Consider relying on regular expression supported patterns like \"word1\\|word2\"";
        exit -1;
    fi;
    [[ -n $ZSH_VERSION ]] && setopt localoptions && setopt ksharrays && setopt ignorebraces;
    local _i=0;
    if [ -z $n_flag ]; then
        _COLORS=("underline bold red" "underline bold green" "underline bold yellow" "underline bold blue" "underline bold magenta" "underline bold cyan" "bold on_red" "bold on_green" "bold black on_yellow" "bold on_blue" "bold on_cyan" "bold on_magenta");
    else
        _COLORS=("bold on_red" "bold on_green" "bold black on_yellow" "bold on_blue" "bold on_magenta" "bold on_cyan" "bold black on_white" "underline bold red" "underline bold green" "underline bold yellow" "underline bold blue" "underline bold magenta");
    fi;
    for keyword in "$@";
    do
        local _COMMAND=$_COMMAND"ack $_OPTS --noenv --flush --passthru --color --color-match=\"${_COLORS[$_i]}\" '$keyword' |";
        _i=$_i+1;
    done;
    _COMMAND=${_COMMAND%?};
    cat - | eval $_COMMAND
}
```


