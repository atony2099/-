# 版本回退：

Head: 当前分支最近的一个提交

index: staging area 

working copy:当前工作区





git reset 的实质：

git reset head :重置当前head 到另一个commit



![](https://images0.cnblogs.com/blog2015/737565/201505/182225251979460.png)

TO:

![](https://images0.cnblogs.com/blog2015/737565/201505/182229495415187.png)

reset-- soft:head 回退了 index 和 working copy 没有回退

![](https://images0.cnblogs.com/blog2015/737565/201505/182237338854646.png)



reset -- hard：重置  --hard :全部回退了

重置到另个一个commit ,并放弃本地的所有修改 ，会使得数据丢失

![](https://images0.cnblogs.com/blog2015/737565/201505/182238305101646.png)



reset -- mixed(defualt) head 和 index 回退了 。working 没有回退 

![](https://images0.cnblogs.com/blog2015/737565/201505/182238202609380.png)



补救措施：

(git relog 查看所有已经提交的, 再git reset)





# git submodules 

子仓库







