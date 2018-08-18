# thinkphp5.1核心

## thinkphp核心——模版
需求：
在一个html中 
引用公共header

公共footer

#### 步骤1 在application\index(index模块)\controller\Layout.php（创建Layout.php控制器）
Layout.php
```
<?php 
  namespace app\index\controller;
  use think\Controller; 

  class Layout extends Controller { // 要想使用渲染工具 必须继承Controller类
    public function test2 () {
      return $this->view->fetch(); // 可以传值
    }
  }
?>
```
在地址栏中访问：（已经配置好了虚拟主机）
www.tp5.com/index(应用模块名)/layout（控制器名）/test2(方法名)

#### 步骤2 在application\index\view（新建目录）\layout(新建目录 和Layout类名保持一致 不区分大小写)\test2.html(新建文件 和Layout类的方法名test2保持一致)
test2.html：
```
<h2>这是test2</h2>
```
此时访问
www.tp5.com/index(应用模块名)/layout（控制器名）/test2(方法名)
会自动渲染test2.html页面内容

#### 步骤3 创建模版
首先在 application\index\(MVC)view\public(新建public文件夹)，然后分别新建3个文件：
header.html
```
<title>[title]</title> <!-- 这个变量在使用的时候传入 -->
<h2>header这是header header</h2>
```

footer.html
```
<h2>footer这是脚部 footer这是脚部</h2>
```

base.html
```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>base html</title>
</head>
<body>
  <!-- 引用模版文件header.html -->
  {include file="public/header" title="传给header的title变量" /}

  <!-- 引用模版文件footer.html -->
  {include file="public/footer" /}

  <!-- 自定义默认显示内容 address ->
  {block name='address'}
    <div>我是默认的地址 浙江省杭州市西湖区</div>
  {/block}

<!-- 自定义默认显示内容 email ->
    {block name='email'}
    <p>我是默认email地址</p>
  {/block}
</body>
</html>
```

#### 步骤4 在test2.html中引用模版
```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>test2</title>
</head>
<body>
  <!-- 引用base.html文件 -->
  {extend name='public/base' /}

  <!-- 覆盖在base.html中定义的默认显示区address -->
  {block name='address'}
    <p>我要覆盖西湖区的地址 哈哈啊哈哈</p>
  {/block}
  
  <!-- 覆盖在base.html中定义的默认显示区email 如果在此没有定义 就会显示base.html中定义的默认设置 -->
  {block name='email'}
    <p>我是默认email地址</p>
  {/block}
</body>
</html>
```

#### 步骤5 测试 
在浏览器中输入
www.tp5.com/index(应用模块名)/layout（控制器名）/test2(方法名)

显示
header这是header header
footer这是脚部 footer这是脚部
我要覆盖西湖区的地址 哈哈啊哈哈
我是默认email地址

## thinkphp核心——验证器
#### 步骤1 在application\index(index模块)\controller\Index.php（创建Index.php控制器 控制器名称开头大写）
```
<?php
// 命名空间
namespace app\index\controller;

// 引用Controller类
use think\Controller;

// 引用自定义的userVliadate验证器类 （第二步来做）
use \app\index\validate\User as userValidate;

// Index类继承Controller类 如果只是使用验证器 可以不用继承Controller
class Index  extends Controller
{
    // protected $batchValidate = true;
    
    public function index()
    {
        $data = [
            'name'  => 'thinkphp',
            'email' => 'thinkphp@qq.com',
        ];

        // 创建验证器实例
        $validate = new userValidate();
        
        // 使用验证器 的check方法 验证$data
        var_dump($validate->check($data));

    }

}
```
#### 步骤2 定义验证器
在application\index(index模块)\validate（controller同级目录）\User.php（创建User.php控制器 控制器名称开头大写）
User.php:
```
<?php

  namespace app\index\validate;
  // 引用Validate类
  use think\Validate;
  // 定义验证器类 继承Validate类
  class User extends Validate
  { 
      // 定义验证规则 固定格式写法 protected $rule = ----
      protected $rule = [
          'name'  =>  'require|max:25',
          'email' =>  'email',
      ];

  }
 ?>
```
#### 步骤3 测试验证器
http://www.tp5.com/index/index/index
输出：
C:\wamp\www\tp5\application\index\controller\Index.php:23:boolean true
