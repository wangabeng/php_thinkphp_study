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
    <p>email地址：</p>
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
    __block__ @ThinkPHP 版权所有
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
我是默认email地址 @ThinkPHP 版权所有

## thinkphp核心—模板 __block__用法：见上 引用自身 __block__

## thinkphp核心——验证器之一
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

## thinkphp核心——验证器之二 批量验证
### 批量验证器 就是说比如定义了2个以上的验证规则，如果是非批量验证器，如果有2个以上验证不通过，则只会出现第一个报错的时候错误信息。而批量验证，则返回一个错误信息组。
1 定义验证器 application/index/validate/User.php
```
<?php

  namespace app\index\validate;

  use think\Validate;
  // 定义验证器类
  class User extends Validate
  {
      protected $rule = [
          'name'  =>  'require|max:25',
          'email' =>  'email',
      ];

  }
 ?>
```
2 使用验证器
application/index/controller/index/Index.php
```
<?
  namespace app\index\controller;
  use think\Controller;

  // userVliadate验证器
  use \app\index\validate\User as userValidate;
  
  class Index extends Controller {
    // 是否批量验证
    protected $batchValidate = true;
    
    public function testValidate () {

        $data = [
            'name'  => '',
            'email' => 'thinkphp.com',
        ];

        // $validate = new userValidate();
        // if(!($validate->check($data))) {
        //     return ($validate->getError());
        // }
        
        // 这里不可以用UserValidate
        $result = $this->validate($data, '\app\index\validate\User');

        if (true !== $result) {
            // 验证失败 输出错误信息
            return json($result);
        }

    }
  }
php>
```
3 测试 浏览器输入 http://www.tp5.com/index/index/testValidate
输入结果：
```
{"name":"name不能为空","email":"email格式不符"}
```

## thinkphp上线部署
1 配置nginx confi文件
/etc/nginx/conf.d/testphp.conf
参照：https://blog.csdn.net/woshihaiyong168/article/details/54973353
```
server {    
        listen       80;    
        server_name  zixi.benkid.cn;    
        charset utf-8;    
        access_log off;   
        root /data/www/tp5/public/;    
        index  index.html index.htm index.php;    
        location / {    
            if (!-e $request_filename) {    
                rewrite ^(.*)$ /index.php?s=$1 last;    
                break;    
            }    
        }    
        error_page   500 502 503 504  /50x.html;    
        location = /50x.html {    
            root   html;    
        }    
        location ~ \.php$ {    
           fastcgi_pass   127.0.0.1:9000;    
           fastcgi_index index.php;    
           include fastcgi_params;    
           set $real_script_name $fastcgi_script_name;    
           if ($fastcgi_script_name ~ "^(.+?\.php)(/.+)$") {    
               set $real_script_name $1;    
               set $path_info $2;    
           }    
           fastcgi_param SCRIPT_FILENAME $document_root$real_script_name;    
           fastcgi_param SCRIPT_NAME $real_script_name;    
           fastcgi_param PATH_INFO $path_info;    
        }    
        location ~ .*\.(gif|jpg|jpeg|png|bmp|swf|flv|ico)$ {    
            expires 30d;    
            access_log off;    
        }    
        location ~ .*\.(js|css)?$ {    
            expires 7d;    
            access_log off;    
       }    
} 
```
2 拷贝tp5项目文件夹至/data/www/目录下
/data/www/tp5
备注：
nginx配置为：
root /data/www/tp5/public/;

3 输入域名/hello  即成功
如果没有在路由中配置 则输入：
域名/模块名/控制器名/方法名


### composer在window系统中的安装和使用：
1 安装
下载地址 https://getcomposer.org/Composer-Setup.exe
下载后会自动安装在我的php的安装目录：C:\wamp\bin\php\php5.6.25

2 打开命令行工具 如git bash中输入 
```
composer
```
如果出现composer字样 就代表安装成功

3 使用 在项目文件夹的根目录新建 composer.json 文件
monolog(插件名)/monolog（作者名） 
```
{
    "require": {
        "monolog/monolog": "1.2.*"
    }
}
```
然后执行 comoser install （如果打开爬墙工具 会更快）

4 使用 可以在https://packagist.org/中搜索这个包 查看如何使用
```
<?php 
  require 'vendor/autoload.php';
  use Monolog\Logger;
  use Monolog\Handler\StreamHandler;

  // create a log channel
  $log = new Logger('name');
  $log->pushHandler(new StreamHandler('monolog.log', Logger::WARNING));

  // add records to the log
  $log->warn('警告日志');
  $log->err('错误日志');

  echo 'ok'.$log->err('错误日志');

?>

```
5 总结用法
只需一个配置文件composer.json，一行指令composer install，代码中引入autoload.php，即可完美地使用第三方包。
更多用法见（需付费）：
https://www.jianshu.com/p/adcae6213e9b


### thinkphp 视图模块引用全局css js img文件
1 www\tp5\public\static 在static目录下新建css js img目录

2 用法 比如图片放在以上新建的img目录里
在视图index.html这样使用
```
<img src="/static/img/waiting.png" alt="">
```

### thinkphp关于大小写
比如 访问地址是
http://www.tp5.com/index（模块名）/item_detail（控制器名 类文件名）/itemdetail（方法名）
1 控制器名（类文件名 类名和类文件名保存一致）:驼峰命名 首字母大写（其他文件小写加下划线命名）
2 方法名 驼峰命名 首字母小写
application\index\controller\ItemDetail.php
```
<?php  
  namespace app\index\controller;

  class ItemDetail {
    public function itemDetail () {
      return 'item detail';
    }
  }

?>
```
在地址栏访问该方法时使用：http://www.tp5.com/index（模块名）/item_detail（控制器名 类文件名 在地址栏中小写加下划线）/itemdetail（方法名）

### thinkphp 获取URL地址传递的参数
1 页面的url
http://www.tp5.com/itemdetail/1
2 路由定义
Route::get('itemdetail/:index', 'index/item_detail/itemdetail');
3 获取参数
使用助手函数
use think\facade\Request;
```
<?php  
  namespace app\index\controller;

  use think\facade\Request;

  class ItemDetail {
    public function itemDetail () {
      $param = Request()->param('index');
      // 可以根据不同的参数 查询数据库
      return 'item detail'.$param;
    }
  }

?>
```

### thinkphp form表单提交及文件上传
1 html模版(如果涉及文件上传 enctype="multipart/form-data"这个必不可少)
```
    <form action="/index/index/testUpload" method="post" enctype="multipart/form-data">
      <input type="text" name='content'>
      <input type="file" name='image'>
      <input type="submit" value="提交">
    </form>
```
2 出来表单请求的方法
```
  // 图片上传测试
  public function testUpload () {
      // 获取参数2种方法
      $param = Request()->param('content');
      $file = Request()->file('image');
      // $info = $file->move( '../uploads');
      $info = $file->move( '../uploads');
      if($info){
          // 成功上传后 获取上传信息
          // 输出 jpg
          echo $info->getExtension();
          // 输出 20160820/42a79759f284b767dfcb2a0197904287.jpg
          echo $info->getSaveName();
          // 输出 42a79759f284b767dfcb2a0197904287.jpg
          echo $info->getFilename(); 
      }else{
          // 上传失败获取错误信息
          echo $file->getError();
      }
  }
```
3 注意的事项
文件存储路径：
$info = $file->move( '../uploads'); // 会自动在tp5根目录创建一个upload文件夹 上传的文件存储在 tp5根目录/uploads/20180822/XXXXXX.jpg
(会自动改名字)

4 获取参数
use think\facade\Request;
$param = Request()->param('content'); // 获取参数

### thinkphp ajax发送请求
1 html发送请求
```
 $.ajax({
    dataType : 'json',
    type : 'POST',
    url : '/index/index/testUpload',
    async : true,
    data : {
        "aoData" : 'aaaa'//测试数据 
    },
    success : function(data){
        console.log(data);
    },
    error : function(XMLHttpRequest, textStatus, errorThrown) {
        console.log(XMLHttpRequest.status + "," + textStatus);
    }
}); 
```
2 服务器端
application/index/index.php
```
  public function testUpload () {
      $aoData = Request()->param('aoData');
      return $aoData;
  }
```
TP5图片上传
https://www.jianshu.com/p/5cd73628da9d

### thinkphp图片上传后获取上传图片的相关信息
1 html
```
<form action="/index/index/testUpload" method="post" enctype="multipart/form-data">

  <input type="text" name='content'>
  <input type="file" name='image'>
  <input type="submit" value="提交">
</form>
```

2 在php处理
```
// 图片上传测试
public function testUpload () {
    // 获取参数2种方法
    $param = Request()->param('content');
    $file = Request()->file('image');
    // $info = $file->move( '../uploads');
    $info = $file->move('uploads');
    if($info){
        // 成功上传后 获取上传信息
        var_dump($info);
        echo 'ROOT_PATH'.Config::get('root_path').$info->getPathName();
        // 输出 jpg
        echo $info->getExtension();
        // 输出 20160820/42a79759f284b767dfcb2a0197904287.jpg
        echo $info->getSaveName();
        // 输出 42a79759f284b767dfcb2a0197904287.jpg
        echo $info->getFilename(); 
    }else{
        // 上传失败获取错误信息
        echo $file->getError();
    }
    // $aoData = Request()->param('aoData');
    // return $aoData;
  }
```
2-1 获取上传文件的相关信息
$info = $file->move('uploads') 是在public根目录下创建uploads文件夹 然后按照日期保存文件var_dump($info);
获取上传文件的路径名
```
echo $info->getPathName(); // uploads\20180825\cc61cdcaf48583ea61870c796e925c10.jpg
```
2-2 获取网站的名称
首先在tp5根目录下设置app.php
```
// 自定义app网址
'root_path' => 'http://www.tp5.com/',
```
首先引入Env类
```
use think\facade\Config;
```
然后
```
Config::get('root_path'); // 获取网站地址

```
2-3 把文件网站地址及路径名拼接起来
```
'ROOT_PATH'.Config::get('root_path').$info->getPathName();  // http://www.tp5.com/uploads/20180825/160761a1048b07a4678d54ff297a6ae2.jpg 即是改文件地址
```

### ajax异步上传图片 无刷新页面实现图片预览 使用场景：form表单提交上传图片 实现无刷新预览
#### 原理：在前端创建一个new FormData()对象 然后把要上传的表单信息放到这个对象中 把这个对象作为data值提交给后端
1 前端
```
<form action="" id="form">
  用户名：<input type="text" name="user"/></br>
  密码：<input type="password" name="pass" /></br>
  性别：<input type="radio" name="sex" value="男"/>男
  <input type="radio" name="sex" value="女"/>女
  头像：<input type="file" id="file" name="file"/></br>
  <button id="btn" type="button">提交</button>
</form>
<div class="con"></div>
```
2 创建完成后，首先我们要先拿到用户从本上传的图片的信息，代码如下
```
var imgs=[];//存储图片链接
 //为文件上传添加change事件
 var fileM=document.querySelector("#file");
 $("#file").on("change",function(){
  console.log(fileM.files);
  //获取文件对象，files是文件选取控件的属性，存储的是文件选取控件选取的文件对象，类型是一个数组
  var fileObj=fileM.files[0];
  //创建formdata对象，formData用来存储表单的数据，表单数据时以键值对形式存储的。
  var formData=new FormData();
  formData.append('file',fileObj);
```
3 这里的formData就是我们现在要的存储文件信息的对象，然后我们需要把它用ajax请求提交给后台：
```
//创建ajax对象
 var ajax=new XMLHttpRequest();
 //发送POST请求
 ajax.open("POST","http://localhost/phpClass/file-upload/move_file.php",true);
 ajax.send(formData);
 ajax.onreadystatechange=function(){
 if (ajax.readyState == 4) {
  if (ajax.status>=200 &&ajax.status<300||ajax.status==304) {
  console.log(ajax.responseText);
  var obj=JSON.parse(ajax.responseText);
  alert(obj.msg);
  if(obj.err == 0){、
   //上传成功后自动动创建img标签放在指定位置
   var img =$("<img src='"+obj.msg+"' alt='' />");
   $(".con").append(img);
   imgs.push(obj.msg);
  }else{
   alert(obj.msg);
  }
  }
 }
 }
});
```
4 然后我们请求成功后，后台肯定要做出相应的处理，并且把图片存到指定的文件夹里，所以相应的PHP应该完成这些操作
```
<?php
//解决跨域问题
header("Access-Control-Allow-Origin:*");
//说明向前台返回的数据类型为JSON
header("Content-type:text/json");
//$_FILES超全局变量存储是文件数据，是一个关联数组
 $fileObj=$_FILES['file'];
 var_dump($fileObj);
 if($fileObj["error"]==0){
 //判断文件是否合法
 $types=["jpg","jpeg","png","gif"];
 $type = explode("/", $fileObj["type"])[1];
 if(in_array($type, $types)){
  $time = time();//获取时间戳 返回一个整形
  //获取文件详细路径
  $filePath="http://localhost/phpClass/image1".$time.".".$type;
  echo $filePath;
  //移动文件
  $res=move_uploaded_file($fileObj["tmp_name"],"../image1/".$time.".".$type);
  if($res){
  $infor=array("err"=>0,"msg"=>"文件移动成功");
  }else{
  $infor=array("err"=>1,"msg"=>"文件移动失败");
  }
 }else{
  $infor=array("err"=>1,"msg"=>"文件格式不合法");
 }
 echo json_encode($infor);
 }
?>
```

### thinkphp验证码使用
1 安装
切换到tp5根目录 执行命令行工具安装think-captcha
```
composer require topthink/think-captcha
```

2 html及js
在html模版中(验证码图片文件也可以这样写 <img src="{:captcha_src()}" alt="captcha" />)
```
    <form action="/index/index/testUpload" method="post" enctype="multipart/form-data">
      
      <!-- 测试验证码 -->
      <input type="text" name="verifyCode" id='verifyCode' class="pass-text-input " placeholder="请输入验证码">
      <div class='test-code'>{:captcha_img()}</div>
      
      <input id='submitBtn' type="button" value="提交">
    </form>
```
在js中发送ajax请求
请求后端验证 可以在输入验证码input框失去焦点的时候通过ajax请求无刷新验证，本例采用按钮提交ajax无刷新验证
```
    // 刷新验证码
    $('.test-code img').on('click', function () {
      $(this).attr('src', "{:captcha_src('')}");
    });
    
    // 发送验证码验证请求
    $('#submitBtn').on('click', function () {
      // 发送ajax请求
      $.ajax({
        dataType : 'json',
        type : 'POST',
        url : '/index/index/testUpload',
        async : true,
        data : {
            "aoData" : $('#verifyCode').val()//测试数据 
        },
        success : function(data){
            console.log(data);
        },
        error : function(XMLHttpRequest, textStatus, errorThrown) {
            console.log(XMLHttpRequest.status + "," + textStatus);
        }
      });      
    });
```
3 在服务器端得到数据并验证，然后返回处理结果给前端。必须要引入Captcha类 才能使用Captcha的check方法
```
    use think\facade\Request;
    use think\captcha\Captcha;

    public function testUpload () {
        
        // 获取ajax参数
        $paramData = Request()->param('aoData');
        // 创建Captcha对象
        $captcha = new Captcha();
        // 执行验证
        $sum = $captcha->check($paramData);
        
        // 返回验证结果
        if (!$sum){
            return json("验证码错误");
        } else {
            return json('ok');
        }

    }
```
4 附：更改验证码设置
配置文件位置
tp5\vendor\topthink\think-captcha\src\Captcha.php
如更改验证码长度 length为4.详细配置见 https://www.kancloud.cn/manual/thinkphp5_1/354122

### thinkphp 设置session（见 https://www.kancloud.cn/manual/thinkphp5_1/354117）
1 引用Session类
```
use think\facade\Session;
```
2 基础用法
```
// 赋值（当前作用域）
Session::set('name','thinkphp');
// 判断（当前作用域）是否赋值
Session::has('name');
// 取值（当前作用域）
Session::get('name');
// 删除（当前作用域）
Session::delete('name');
// 删除（当前作用域）
Session::delete('name');
```

