# 环境变量
go env 查看环境变量
## GOROOT
go程序的安装目录

## GOPATH
go源码的位置（工作空间），可以有多个。window环境下用“；”隔开，mac和linux环境中用“：”隔开
gopath下面有3个目录
### src
### pkg
### bin

## GOBIN

# 权限控制
- 程序体的大小写来区分能不能被其它包引用
- 1.5以及后面的版本中通过创建internal代码包让一些程序实体仅能被当前包引用（模块级私有）
