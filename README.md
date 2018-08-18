# thinkphp核心

## 模版
需求：
在一个html中 
引用公共header

公共footer

步骤1 在application\index(index模块)\controller\Layout.php（创建Layout.php控制器）
Layout.php
```
<?php 
  namespace app\index\controller;
  use think\Controller;

  class Layout extends Controller {
    public function test2 () {
      return $this->view->fetch();
    }
  }
?>
```
在地址栏中访问：（已经配置好了虚拟主机）
www.tp5.com/index(应用模块名)/layout（控制器名）/test2(方法名)

步骤2 在application\index\view（新建目录）\layout(新建目录)\


