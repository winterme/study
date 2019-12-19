# shell基本了解



    #! /bin/bash

    # echo 用于输出
    echo "你好！"
    # 输出环境变量
    echo "${PATH}"

    # 输出某个文件夹下的所有文件目录
    for f in `ls /usr/local/`; do
        echo "*****************${f}*********************"
    done

    #定义变量
    JAVA_HOME="/usr/local/jdk1.8"

    # 输出变量
    echo "我的java_home 地址是："$JAVA_HOME"!现在你知道了吗？"

    # 获取字符串长度
    echo "我得java_home 长度是："${#JAVA_HOME}

    # 获取输出参数, $0 就是执行的文件名称 第一个就是 $1 , 第二个就是 $2

    echo "现在执行的是文件："$0

    echo "-- \$* 演示 ---"
    for i in "$*"; do
        echo "<===>"$i"<===>"
    done

    echo "-----------------------------"
    echo $*
    echo "-----------------------------"

    echo "-- \$@ 演示 ---"
    for i in "$@"; do
        echo "<===>"$i"<===>"
    done


    # 数组
    echo "数组演示"

    NAME_ARRAY=(	"张子强"	"李春梦"	“张三李四”)

    # echo ${NAME_ARRAY[1]}
    echo ${NAME_ARRAY[1]}

    for i in ${NAME_ARRAY[*]} ; do
        echo ${i}
    done

    # 流程控制

    if [ -n "$1" ]
    then 
        echo "第一个输入参数不为空，值是："$1"<=========="
    else
        echo "第一个参数为空！"
    fi


    #  case shell case语句
    case $1 in
    "zzq")
            echo "第一个输入值是zzq"
            ;;
    "licm")
            echo "第一个输入的值是licm"
            ;;
    *)
            echo "没有输入第一个参数!"
    esac


