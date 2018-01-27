# 后盾向军 require.js

houdunren.com
git.oschina.com/houdunwang/video
font-awesome.io

jquery.js, bootstrap.js, angular.js, util.js lodash.js, common.js, video.js

kindeditor, ckeditor, 百度编辑器

组件化

0.前端模块化思想与AMD与require.js介绍
1.目录结构与 main.js 配置文件说明
2.paths 与 baseUrl 配置项说明
3.解决页面加载时模块失败的3种解决方法
4.使用 define 自定义模块
5.多个模块间的依赖关系实例讲解
6.处理非标准化的模块

2/
    1.html
        配置荐需要提前设置完才能用 require() ，如果配置与 require() 同时进行则不行
        <script data-main='../resource/main.js', src='../resource/require.js'></script>
        <button onclick='test()'>test</button>
        <script>
            function test()
            {
                require(['jquery'], function ($){
                    $('body').css({'backgroundColor': 'red'});
                });
                //require(['jquery', 'angular'], function ($, angular){
                //    $('body').css({'backgroundColor': 'red'});
                //});            
            }
            
            //直接读取 require.js 不走配置项
            //不是走模块（而是直接走文件的，所以系统无法赋值给 $, angular)
            //这种方法不太可取，因为没办法管理依赖
            //require(['lib/jquery.min', 'lib/angular.min'], function (){
            //    $('body').css({'backgroundColor': 'red'});
            //});

        </script>
    2.html
        通过浏览器按顺序解析 require.js, main.js 以及下述的 require() 可以生效
        <script src='../resource/require.js'></script>
        <script src='../resource/main.js'></script>
        <button>test</button>
        <script>
            require(['jquery', 'angular'], function ($, angular) {
                $('body').css({'backgroundColor': 'red'});
            });
            
            //require(['../app/util'], function (hd) {
            //   hd.show();
            // });
            require(['util'], function (hd) { 
            // 将 ../resource/app 定义成 baseUrl,  paths 里面的 jquery: ../lib/jquery.min, angular: ../lib/angular.min
            // util 中的 json 对象被赋值给 hd
                hd.show();
                hd.change();
            });
            require(['jquery'], function ($) {
                
            });
            require(['bootstrap'], function () { // bootstrap 依赖 jquery, bootstrap.css
                
            });
            require(['hd'], function (a) {
                // a(); 不在 shim 中定义 exports 的话会报错（针对非标准化） 针对 hd 中只有一个方法
                a.success()
            });
            </script>
/resource
    app/
        util.js
            define(['jquery'], function ($) {
                return {
                    show: function () {
                        alert('后盾人');
                    },
                    message: function () {
                        alert('houdunren.com');
                    },
                    change: function () {
                        $('body').css({'backgroundColor': 'red'});
                    },
                    change2: function () {
                        requre(['jquery'], function ($) {
                            $('body').css({'backgroundColor': 'blue'});
                        });
                    }
                }
            });
        hd.js
            // 老的库， 一般都是函数式， 非标准化（标准化都是以 define 来定义的）， 函数式编辑
            function modal() {
                alert('houdunren');
            }
            function success() {
                alert('yeah!');
            }
    css/
        bootstrap.min.css
        font-awesome.min.css
    fonts/
    js/
        special.js
    lib/
        angular.js
        bootstrap.min.js
        css.min.js
        jquery.min.js
        underscore-min.js
    require.js
    main.js 配置文件
        require.config({
            paths:{
                'jquery': 'lib/jquery.min.js',
                'angular': 'lib/angular.min'
            }
        });
        //require.config({
        //    baseUrl: '../resource/lib/',  // 基准路径，最后斜杠不加也可以 ../resource/lib
        //    paths:{
        //        'jquery': 'jquery.min.js', 
        //        'angular': 'angular.min',
        //        'special': '../js/special' // 参照基准路径去找
        //    }
        //});
        
         // 这块代码在上述配置项之后执行即可
         //require(['jquery', 'angular'], function ($, angular){
         //    $('body').css({'backgroundColor': 'red'});
         //});

        //require.config({
        //    baseUrl: '../resource/app/'
        //    paths:{
        //        'css': '../lib/css.min', // 该库用于解决 css 的依赖问题
        //        'jquery': '../lib/jquery.min.js',
        //        'angular': '../lib/angular.min',
        //        'bootstrap': '../lib/bootstrap.min',
        //        'hd': 'hd' // 不写也行 require() 的时候会自动在基准路径下找
        //    },
        //    shim: { // 定义依赖关系
        //       'hd': {
        //          // exports: 'modal' 针对 hd 文件中只有一个方法
        //          init: function () {
        //              return {
        //                  modal: modal,
        //                  success: success
        //              }
        //          }
        //       }
        //       'bootstrap': {
        //          'deps': ['jquery', 'css!../css/bootstrap.min.css', 'css!../css/font-awesome.min.css'] // css 的依赖文件需要写上文件后缀
        //        }
        //    }
        //});
        
        
        
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    

