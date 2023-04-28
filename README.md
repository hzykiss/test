
没有说明的说明文件！

#!/bin/bash
#sheel脚本语言实现tree功能，增加文件的颜色属性,增加文件统计

branch_vline="│   "     #垂直线分割线
branch_null="    "      #null分支分隔
middle_branch_end="├── "  #中间文件分割线
last_branch_end="└── "    #末尾文件分割线
branch_sum=""
filecount=0
directorycount=0

tree()
{
    #定义局部变量，实现每个文件下的文件数，记录文件数,
    #并注意这个变量定义的位置
    local num=0
    for file in *;
    do
        #计算此文件的目录文件的数量
        thelastfile=`ls |wc -l`
        num=$((num+1))
        ##当文件属于该目录中的最后一个文件时，输出将添加到$last_branch_end
        if [[ $thelastfile -eq $num ]]; then
                if [ -f "$file" ]; then
                    filecount=$((filecount+1))
                    #当文件可执行时，显示文件为绿色
                    if [ -x "$file" ];then
                        echo -e "${branch_sum}$last_branch_end\033[1;32m$file\033[0m"
                    else
                        echo -e "${branch_sum}$last_branch_end$file"
                    fi
                fi
                if [ -d "$file" ]; then
                    directorycount=$((directorycount+1))
                    echo -e "${branch_sum}$last_branch_end\033[1;34m$file\033[0m"
                    branch_sum=${branch_sum}${branch_null}
                    cd "$file"
                    tree
                    cd ..
                    branch_sum=${branch_sum%${branch_null}}
                fi
        else
            if [ -f "$file" ]; then
                    filecount=$((filecount+1))
                    if [ -x "$file" ];then
                        echo -e "${branch_sum}$middle_branch_end\033[1;32m$file\033[0m"
                    else
                        echo "${branch_sum}${middle_branch_end}$file"
                    fi
            fi
            if [ -d "$file" ]; then
                    directorycount=$((directorycount+1))
                    echo -e "${branch_sum}${middle_branch_end}\033[1;34m$file\033[0m"
                    branch_sum=${branch_sum}${branch_vline}
                    cd "$file"
                    tree
                    cd ..
                    branch_sum=${branch_sum%${branch_vline}}
            fi
        fi
    done
}

if [ -z "$1" ]; then
    echo -e "\033[1;34m.\033[0m"
else
    echo -e "\033[1;34m$1\033[0m"
    cd $1
fi
if [[ $? -eq 0 ]]; then
    tree
    echo
    echo "$directorycount directories,  $filecount files"
fi
