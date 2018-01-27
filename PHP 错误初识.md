## 1.错误初识

## 2.运行时错误提示

//notice:通知类型（这类型错误不会影响代码执行）
//常见的几种notice：
    未定义变量（常量），数组元素未定义（下标）
    
    //未定义变量：
        echo $var;
    //延伸未定义变量：
        function display($name){
            echo $name;
            echo $age;
        }
        display('hanmeimei');
    //变量在PHP中有一种延伸：属性（类中的变量）； 
    //未定义常量：
        echo PI;
    //数组元素下标定义：
        echo $_POST['name']; //专门用来接收表单（用户浏览器）提交的数据post
        echo $arr['age'];
        
        
//strict     
//严格错误：软件升级过程中不可避免的出现了迭代产品
//常见的几种：构造方法，类访问非静态方法
//定义类（与函数一样属于结构：不调用就不运行）
class GirlFriend{
    //结构中所有内容都是类成员
    
    //构造方法
    public function __construct(){
        //资源初始化
        echo __METHOD__ . '<br/>';
    }
    
    //向下兼容构造方法：以类名作为方法名
    public function GirlFriend(){
        //资源初始化
        echo __METHOD__ . '<br/>';
    }
    
    public static function 
}

// 实例化:构造方法兼容错误
$g = new GirlFriend();

//类可以访问类成员的：一般访问静态成员（static关键字修饰）和类常量（const）
GirlFriend::GirlFriend();  //fatal error




//warning警告：告知用户存在错误，不处理也不会影响到代码后面的执行
//常见问题：包含文件不存在，默认时区，函数实参没有传入值
//包含文件
include_once fatalerror.php'; //不存在
//使用默认时区
echo date('Y-m-d', time());
//实参没有传值
function display($name){
    echo $name;
}
display();
//输出
echo 'hello world';



//fatal error 致命错误：会直接导致出错代码之后的其它代码不再执行
//常见问题：文件包含，使用未定义的结构（函数和类），访问私有成员，内存超出，时间过长（超过30秒）
//文件包含：require
require 'fatalerror.php'
//使用未定义结构（函数）
display();  //直接调用函数
//未定义类
$g = new GirlFriend();
//私有成员访问
class Girl{
    private $age;
    public $name = 'itcast';
}
$g = new Girl();
echo $g->namel
echo $g->age;
//内存超出：服务器内存不够运行（内存泄漏）
//递归
function dg(){
    echo 'a';
    //递归调用”自己在函数内部调用自己
    dg();
}
dg();


//解决方案 set_time_limit(90); 优化代码逻辑
//执行时间过长
$arr = array();
for($i=0;$i<100000;$i++){
    //构造一个有十万个元素的数组
    $arr[i]=mt_rand(l,100000);
}
//定义冒泡排序
function bubble_sort($arr){
    for($i=0,$len=count($arr);$i<$len;$i++){
        for($j=0;$j<$len-1;$j++){
            //冒泡原则：小的放前面，大的放后面
            if($arr[$i]>$arr[$j]){
                //交换
                $temp = $arr[$i];
                $arr[$i] = $arr[$j];
                $arr[$j] = $temp;
            }
        }
    }
    return $arr;
}
bubble_sort($arr);

echo 'hello world';


## 3.错误解决方案

//parse error:解析错误，代码在转变成机器码（编译）的过程中出错了
//常见错误：结构不完整，缺少语句结束符，关键字写错，定界符
//结构不完整
if(true)){
    echo 'true';
}

function display(){

}
}


//缺少语句结束符
echo 'hello world'

//定界符:结束的内容必须顶格
$str = <<<END
    hello world
END;






##解决
//错误分为两大类：编译（parse error)，运行时（runtime error)
/*
parse error:只能在书写代码的过程中，保证代码的基本结构正确
    1.注意书写格式（大部分错误都是因为格式原因）
    
runtime error:
    1.忽略式：notice, warning, strict 可以直接忽略：懒惰（肯定会出问题）
    2.搞定bug：出现任何问题，都用正确的代码方式处理（增加代码容错性）：绝对推荐
    3.错误抑制符@，不让错误出现
    4.PHP代码开发分为两种阶段：开发阶段（解决所有错误）、生产阶段（抑制所有错误）
        4.1为单独项目进行错误控制
        ini_set('error_reporting',E_ALL); //显示所有级别的错误
        ini_set('display_errors',1); //开发：把错误显示出来
        生产环境
        ini_set('error_reporting',E_ALL & ~E_NOTICE & ~E_STRICE & ~E_WARNING);
        ini_set('display_errors',0); //不显示错误

        4.2 整个服务器错误控制：在php_ini中进行配置 
        error_reporting 
        display_errors = off


*/




