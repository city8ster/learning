#php7
# w3techs.com


php
asp.net
java
静态文件

qps

HHVM


结合运算符
$a = 3;
$b = 5;
%c = %a<=>$b;
var_dump($c);

等价于：
if($a>$b){
    echo 1;
}else{
    echo -1;
}else{
    echo 0;
}

$c = ($a>$b)?1:($a==$b?0:-1);

空合并运算符
$a = $_GET['a']??'empty';
var_dump($a);
等价于 isset($_GET['a'])?$_GET['a']:'empty';

foreach不再使用内部的迭代器
$arr = [1,2,4];
foreach($arr as $k=>$v){
    $arr[] = $value;   
    echo current($arr).'<br/>';      //1 1 1   (php7)    2 2 2 (php5)
}


foreach($arr as &$v){
    echo current($arr).'<br/>';
}

$b = $arr;
foreac($arr as &$v){
    echo $v.key($arr).current($arr);
}

返回值类型
function f():string{
    return 'hello';
}
echo f();


标量数据类型约束
function f(int $a){
    return 1;
}


强制类型开关
declare(strict_types=1);//要写在文件的开头部
function f(float $a){
    var_dump($a);
}
f('1.2');

匿名类
$obj2 = new class{};
$obj3 = new class extends a{};

十六进制字符串不在转换为数字
$a = '0x10' == '16';
var_dump($a); //false

function f($a,$b){
    print_r(func_get_args());
}
f(1,2);

上下文敏感的词法分析
class demo{
    public function for(){}
    public function demo(){}
    public function function(){}
    public function class(){}
    
}


统一变量解析
class demo{
    public function f(){
        return ['name'=>'xukun'];
    }
}
$obj = new demo();
echo $obj->f()['name'];

$baz = ['bar'=>'f'];
class Foo{
    p
}


整型使用8个字节
var_dump(PHP_INT_MAX);


命名空间新语法



