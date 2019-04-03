# nodejs项目git workflow

## 背景
> nodejs项目的master是测试环境,release是发布环境,我们提release时可以正常地mr,不会有任何问题,但是master由于携带大量测试代码,无法rebase也没有必要rebase,因此通过mr查看merge信息+手动merge的方式来提测代码.这就要求我们对项目分支的管理更加严格,即在项目分支上严格执行rebase式合并.

## 

## 操作示例
> 以`nodejs`项目为例,组件化项目分支course_components
- 负责人确保该分支与release同步,在course_components分支下执行
  ```git
  git rebase release
  ```
  并解决conflict,然后push,若release分支出现过问题,如被强制push过,则course_components分支也需强制push.
- 各功能开发者在自己的子分支,如course_components_xt下执行
  ```git
  git fetch --all
  git rebase origin/course_components
  ```
  此时course_components最近的更新(包含负责人同步过来的release的更新)就会出现在自己的分支上,然后即可push到自己的远端分支.
- 当需要将自己代码提交到course_components时,在gitlab提交mr,并发到海贼团群,@项目组员x2+@朱杰杰+@大佬,让大家review.
  > 如果提交时自己的代码与course_components分支齐平,mr是可以直接点击merge按钮进行的,否则会出现rebase按钮,此时可以在自己本地再次rebase course_components并push,或直接点击rebase按钮,但别忘了这只是远端的rebase,需要你在本地pull来获取本次rebase到自己分支的代码.  
## 问题处理
理论上在自己分支rebase时不会出现conflict,但如果出现,可以用解决冲突的办法来处理,不过我建议直接干掉这个会冲突的分支,因为该处理的冲突负责人那里其实已经处理掉了,操作如下:
  ```git
  git log //查看course_components_xt分支最近的提交与course_components分支的差异,确定分歧点
  git reset [HEAD] // HEAD参数是刚才查到的course_components分支与自己分支最接近的一个commit的head,这一步是为了取回commit到自己分支却未同步到项目主分支的代码
  git stash save [NAME] // 为stash取一个name,将上一步的代码保存在临时的stash里
  git checkout course_components  // 此时你本地的course_components肯定与origin有冲突
  git reset --hard origin/course_components // 这一步之前一定要fetch --all,切记.
  git pull // 覆盖掉本地course_components分支
  git branch -D course_components_xt // 删除掉来自已被覆盖course_components的自己的子分支(有用的代码已经在第三步保存到stash了)
  git branch -b course_components_xt course_components // 从course_components新建自己的分支
  git push --set-upstream origin course_components_xt -f // 用新建的分支覆盖掉自己的远端分支
  git stash pop // 恢复自己的代码
  ```
  之后照常commit即可.
