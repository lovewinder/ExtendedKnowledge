# 如何使用git中的tag标签控制版本
1. 打开git repositories视图
2. 创建tag
  1. 创建新的tag
    * 右键tag
    * create tag
    * 输入tag名
  2. 更新tag
    * 右键tag
    * create tag
    * 选中Existing tags中的tag
    * 勾选Force replace existing tag
    * push tag,勾选Force overwriter of tags when they already exist on remote
3. 修改tag
  * 创建tag的分支,修改完执行更新tag步骤
  * 同步测试版和正式版
      
      会有这样一种情况:
      
      当我们根据某个正式版来开发新版本时,会创建一个测试版beat,但是这个时候正式版进行了修改.为了同步beat的基础内容和正式版的内容,会分别创建正式版和beat版的分支,由beat版的分支来同步正式版的分支
      (实际上这种情况只在需求总是莫名其妙改动的时候出现)
