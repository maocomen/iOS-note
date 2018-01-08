# #iOS 状态栏知识点

![3FFB2110-DE8D-4348-BA5C-18E414641343](https://github.com/maocomen/PrivateFolder/blob/master/状态栏知识点/images/status_image_one.png?raw=true)

先来说一下这个属性，这个属性是控制状态栏是基于viewController还是基于application,xCode8默认这个属性为yes，也就是基于viewController来决定是否隐藏statusBar，statusBar的颜色，隐藏动画等。![65D42D2C-13A8-460C-88C5-F733D0B14255](https://github.com/maocomen/PrivateFolder/blob/master/状态栏知识点/images/status_image_two.png?raw=true)

这三个就是基于viewController的三个设置状态栏的方法



注意的地方:

1. 如果当前viewController的navigationController存在，那么当前viewController的preferredStatusBarStyle这个方法就不会走了，而是会走navigationController的

   ​