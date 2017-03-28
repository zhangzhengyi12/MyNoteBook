# **能力检测**
> 能力检测是的目的是识别浏览器的能力而不是检测特定的浏览器类型。比如下面的代码


```
function getElementId(id) {
    if(document.getElementById){
        return document.getElementById(id);
    }else if(document.all){
        return document.all[id];
    }  
}
```
> 能力检测的核心概念
> 第一 先检测达成目的最常见的特效，简单来说就是比如getelelid 就更常见 大多数情况下并不需要检测第二个
> 第二 一个特性存在不代表另一个特性也存在，你不能检测特性A然后在检测成功的情况下随意的去动用特性B 他们两者可能并没有太大的关系。

## **更可靠的能力检测**
> 能力检测无法检测某个成员是不是你想要的（能力检测是能力而不是是否拥有）
> 比如下面的例子，无法知道检测到的sort究竟是一个属性还是一个函数。


```
function isSortable(object) {
    return !!object.sort; //双反让他转换成boolean
}

function isSortable(object) {
    return typeof object.sort == "function";
}//更好的方法 检测sort是否是一个函数
```
>  在一些老旧的IE 浏览器中 typeof检测进行出现不标准的情况，为此我们可以使用一个比较通用的函数。

```
function isHostMethod(Object,property){

    var t = typeof object[property];

    return t =='function' || (!!(t=='object'&&object[property])) ||

        t=='unknow';

}
```

## **能力检测，不是浏览器检测**
> 检测的重点是能力 而不是浏览器的类型，况且你通过某些个属性也很难去确定一个浏览器，况且浏览的实现随着版本的跟新可能会不断的改变。
> 如果你需要一些能力 你完全可以把一些能力组合起来一起检测

```
var hasNSPlughins = !!(navigator.plugins && navigator.plugins.length);
//是否支持插件
var hasDOM1 = !!(document.createElement && document.getElementById && document.getElementsByTagName);
//是否支持DOM一级
```

---

# **怪癖检测**
> 怪癖就是浏览器的bug，

```
var hasDontEnumQuirk = function(){
        
            var o = { toString : function(){} };
            for (var prop in o){
                if (prop == "toString"){
                    return false;
                }
            }
        
            return true;
        }();

        var hasEnumShadowsQuirk = function(){
        
            var o = { toString : function(){} };
            var count = 0;
            for (var prop in o){
                if (prop == "toString"){
                    count++;
                }
            }
        
            return (count > 1);
        }();

        alert(hasDontEnumQuirk);
        alert(hasEnumShadowsQuirk);
```
以上为两个非常常见的怪癖
第一个出现在IE8及以前的 某个实例的属性名字与原型中特性为Enumberable 标记为false的属性同名 那么这个实例属性不会在for in 中被便利。

第二个出现在Safari3版本以前遍历被隐藏

#**用户代理检测**
> 简单来说就是历史原因导致了`navigator.userAgent`的表现不够规范化

```
var client = function(){

    //呈现引擎
    var engine = {
        ie: 0,
        gecko: 0,
        webkit: 0,
        khtml: 0,
        opera: 0,

        //完全的版本
        ver: null
    };

    //浏览器
    var browser = {

        //browsers
        ie: 0,
        firefox: 0,
        safari: 0,
        konq: 0,
        opera: 0,
        chrome: 0,

        //特殊版本
        ver: null
    };


    //平台设备系统
    var system = {
        win: false,
        mac: false,
        x11: false,

        //移动平台
        iphone: false,
        ipod: false,
        ipad: false,
        ios: false,
        android: false,
        nokiaN: false,
        winMobile: false,

        //游戏主机
        wii: false,
        ps: false
    };

    //检测引擎和浏览器
    var ua = navigator.userAgent;
    if (window.opera){
        engine.ver = browser.ver = window.opera.version();
        engine.opera = browser.opera = parseFloat(engine.ver);
    } else if (/AppleWebKit\/(\S+)/.test(ua)){
        engine.ver = RegExp["$1"];
        engine.webkit = parseFloat(engine.ver);

        //检测是chrome还是safari
        if (/Chrome\/(\S+)/.test(ua)){
            browser.ver = RegExp["$1"];
            browser.chrome = parseFloat(browser.ver);
        } else if (/Version\/(\S+)/.test(ua)){
            browser.ver = RegExp["$1"];
            browser.safari = parseFloat(browser.ver);
        } else {
            //低版本的safari
            var safariVersion = 1;
            if (engine.webkit < 100){
                safariVersion = 1;
            } else if (engine.webkit < 312){
                safariVersion = 1.2;
            } else if (engine.webkit < 412){
                safariVersion = 1.3;
            } else {
                safariVersion = 2;
            }

            browser.safari = browser.ver = safariVersion;
        }
    } else if (/KHTML\/(\S+)/.test(ua) || /Konqueror\/([^;]+)/.test(ua)){
        engine.ver = browser.ver = RegExp["$1"];
        engine.khtml = browser.konq = parseFloat(engine.ver);
    } else if (/rv:([^\)]+)\) Gecko\/\d{8}/.test(ua)){
        engine.ver = RegExp["$1"];
        engine.gecko = parseFloat(engine.ver);

        //determine if it's Firefox
        if (/Firefox\/(\S+)/.test(ua)){
            browser.ver = RegExp["$1"];
            browser.firefox = parseFloat(browser.ver);
        }
    } else if (/MSIE ([^;]+)/.test(ua)){
        engine.ver = browser.ver = RegExp["$1"];
        engine.ie = browser.ie = parseFloat(engine.ver);
    }

    //detect browsers
    browser.ie = engine.ie;
    browser.opera = engine.opera;


    //detect platform
    var p = navigator.platform;
    system.win = p.indexOf("Win") == 0;
    system.mac = p.indexOf("Mac") == 0;
    system.x11 = (p == "X11") || (p.indexOf("Linux") == 0);

    //detect windows operating systems
    if (system.win){
        if (/Win(?:dows )?([^do]{2})\s?(\d+\.\d+)?/.test(ua)){
            if (RegExp["$1"] == "NT"){
                switch(RegExp["$2"]){
                    case "5.0":
                        system.win = "2000";
                        break;
                    case "5.1":
                        system.win = "XP";
                        break;
                    case "6.0":
                        system.win = "Vista";
                        break;
                    case "6.1":
                        system.win = "7";
                        break;
                    case "10.0":
                        system.win = "10";
                        break;
                    default:
                        system.win = "NT";
                        break;
                }
            } else if (RegExp["$1"] == "9x"){
                system.win = "ME";
            } else {
                system.win = RegExp["$1"];
            }
        }
    }

    //mobile devices
    system.iphone = ua.indexOf("iPhone") > -1;
    system.ipod = ua.indexOf("iPod") > -1;
    system.ipad = ua.indexOf("iPad") > -1;
    system.nokiaN = ua.indexOf("NokiaN") > -1;

    //windows mobile
    if (system.win == "CE"){
        system.winMobile = system.win;
    } else if (system.win == "Ph"){
        if(/Windows Phone OS (\d+.\d+)/.test(ua)){;
            system.win = "Phone";
            system.winMobile = parseFloat(RegExp["$1"]);
        }
    }


    //determine iOS version
    if (system.mac && ua.indexOf("Mobile") > -1){
        if (/CPU (?:iPhone )?OS (\d+_\d+)/.test(ua)){
            system.ios = parseFloat(RegExp.$1.replace("_", "."));
        } else {
            system.ios = 2;  //can't really detect - so guess
        }
    }

    //determine Android version
    if (/Android (\d+\.\d+)/.test(ua)){
        system.android = parseFloat(RegExp.$1);
    }

    //gaming systems
    system.wii = ua.indexOf("Wii") > -1;
    system.ps = /playstation/i.test(ua);

    //return it
    return {
        engine:     engine,
        browser:    browser,
        system:     system
    };

}();
```
用户代理是客户端检测的最后一个选择，以下情况下可以考虑动用用户代理检测

- 不能准确的使用能力或者怪癖 
- 同一款浏览器在不同的平台下有不同的特性
- 为了分析用户的浏览器数据
