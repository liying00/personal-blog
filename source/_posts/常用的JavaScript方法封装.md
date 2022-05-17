---
title: 常用的JavaScript方法封装
---

time: 2020-05-17

1、输入一个值，返回其数据类型**

<pre>function type(para) {
    return Object.prototype.toString.call(para)
}</pre>
2、数组去重
<pre>function unique1(arr) {
    return [...new Set(arr)]
}

function unique2(arr) {
    var obj = {};
    return arr.filter(ele => {
        if (!obj[ele]) {
            obj[ele] = true;
            return true;
        }
    })
}

function unique3(arr) {
    var result = [];
    arr.forEach(ele => {
        if (result.indexOf(ele) == -1) {
            result.push(ele)
        }
    })
    return result;
}</pre>
3、字符串去重
<pre>String.prototype.unique = function () {
    var obj = {},
        str = '',
        len = this.length;
    for (var i = 0; i < len; i++) {
        if (!obj[this[i]]) {
            str += this[i];
            obj[this[i]] = true;
        }
    }
    return str;
}

//去除连续的字符串 
function uniq(str) {
    return str.replace(/(\w)\1+/g, '$1')
}</pre>
4、深拷贝 浅拷贝
//深克隆（深克隆不考虑函数）
<pre>function deepClone(obj, result) {
    var result = result || {};
    for (var prop in obj) {
        if (obj.hasOwnProperty(prop)) {
            if (typeof obj[prop] == 'object' && obj[prop] !== null) {
                // 引用值(obj/array)且不为null
                if (Object.prototype.toString.call(obj[prop]) == '[object Object]') {
                    // 对象
                    result[prop] = {};
                } else {
                    // 数组
                    result[prop] = [];
                }
                deepClone(obj[prop], result[prop])
    } else {
        // 原始值或func
        result[prop] = obj[prop]
    }
  }
}
return result;
}

// 深浅克隆是针对引用值
function deepClone(target) {
    if (typeof (target) !== 'object') {
        return target;
    }
    var result;
    if (Object.prototype.toString.call(target) == '[object Array]') {
        // 数组
        result = []
    } else {
        // 对象
        result = {};
    }
    for (var prop in target) {
        if (target.hasOwnProperty(prop)) {
            result[prop] = deepClone(target[prop])
        }
    }
    return result;
}
// 无法复制函数
var o1 = jsON.parse(jsON.stringify(obj1));</pre>
5、reverse底层原理和扩展
// 改变原数组
<pre>Array.prototype.myReverse = function () {
    var len = this.length;
    for (var i = 0; i < len; i++) {
        var temp = this[i];
        this[i] = this[len - 1 - i];
        this[len - 1 - i] = temp;
    }
    return this;
}</pre>
6、圣杯模式的继承
<pre>function inherit(Target, Origin) {
    function F() {};
    F.prototype = Origin.prototype;
    Target.prototype = new F();
    Target.prototype.constructor = Target;
    // 最终的原型指向
    Target.prop.uber = Origin.prototype;
}</pre>
7、找出字符串中第一次只出现一次的字母
<pre>String.prototype.firstAppear = function () {
    var obj = {},
        len = this.length;
    for (var i = 0; i < len; i++) {
        if (obj[this[i]]) {
            obj[this[i]]++;
        } else {
            obj[this[i]] = 1;
        }
    }
    for (var prop in obj) {
       if (obj[prop] == 1) {
         return prop;
       }
    }
}</pre>
8、找元素的第n级父元素
<pre>function parents(ele, n) {
    while (ele && n) {
        ele = ele.parentElement ? ele.parentElement : ele.parentNode;
        n--;
    }
    return ele;
}</pre>
9、 返回元素的第n个兄弟节点
<pre>function retSibling(e, n) {
    while (e && n) {
        if (n > 0) {
            if (e.nextElementSibling) {
                e = e.nextElementSibling;
            } else {
                for (e = e.nextSibling; e && e.nodeType !== 1; e = e.nextSibling);
            }
            n--;
        } else {
            if (e.previousElementSibling) {
                e = e.previousElementSibling;
            } else {
                for (e = e.previousElementSibling; e && e.nodeType !== 1; e = e.previousElementSibling);
            }
            n++;
        }
    }
    return e;
}</pre>
10、封装mychildren，解决浏览器的兼容问题
<pre>function myChildren(e) {
    var children = e.childNodes,
        arr = [],
        len = children.length;
    for (var i = 0; i < len; i++) {
        if (children[i].nodeType === 1) {
            arr.push(children[i])
        }
    }
    return arr;
}</pre>
11、判断元素有没有子元素
<pre>function hasChildren(e) {
    var children = e.childNodes,
        len = children.length;
    for (var i = 0; i < len; i++) {
        if (children[i].nodeType === 1) {
            return true;
        }
    }
    return false;
}</pre>
12、我一个元素插入到另一个元素的后面
<pre>Element.prototype.insertAfter = function (target, elen) {
    var nextElen = elen.nextElenmentSibling;
    if (nextElen == null) {
        this.appendChild(target);
    } else {
        this.insertBefore(target, nextElen);
    }
}</pre>
13、返回当前的时间（年月日时分秒）
<pre>function getDateTime() {
    var date = new Date(),
        year = date.getFullYear(),
        month = date.getMonth() + 1,
        day = date.getDate(),
        hour = date.getHours() + 1,
        minute = date.getMinutes(),
        second = date.getSeconds();
        month = checkTime(month);
        day = checkTime(day);
        hour = checkTime(hour);
        minute = checkTime(minute);
        second = checkTime(second);
     function checkTime(i) {
        if (i < 10) {
                i = "0" + i;
       }
      return i;
    }
    return "" + year + "年" + month + "月" + day + "日" + hour + "时" + minute + "分" + second + "秒"
}</pre>
14、获得滚动条的滚动距离
<pre>function getScrollOffset() {
    if (window.pageXOffset) {
        return {
            x: window.pageXOffset,
            y: window.pageYOffset
        }
    } else {
        return {
            x: document.body.scrollLeft + document.documentElement.scrollLeft,
            y: document.body.scrollTop + document.documentElement.scrollTop
        }
    }
}</pre>
15、获得视口的尺寸
<pre>function getViewportOffset() {
    if (window.innerWidth) {
        return {
            w: window.innerWidth,
            h: window.innerHeight
        }
    } else {
        // ie8及其以下
        if (document.compatMode === "BackCompat") {
            // 怪异模式
            return {
                w: document.body.clientWidth,
                h: document.body.clientHeight
            }
        } else {
            // 标准模式
            return {
                w: document.documentElement.clientWidth,
                h: document.documentElement.clientHeight
            }
        }
    }
}</pre>
16、获取任一元素的任意属性
<pre>function getStyle(elem, prop) {
    return window.getComputedStyle ? window.getComputedStyle(elem, null)[prop] : elem.currentStyle[prop]
}</pre>
17、绑定事件的兼容代码
<pre>function addEvent(elem, type, handle) {
    if (elem.addEventListener) { //非ie和非ie9
        elem.addEventListener(type, handle, false);
    } else if (elem.attachEvent) { //ie6到ie8
        elem.attachEvent('on' + type, function () {
            handle.call(elem);
        })
    } else {
        elem['on' + type] = handle;
    }
}</pre>
18、解绑事件
<pre>function removeEvent(elem, type, handle) {
    if (elem.removeEventListener) { //非ie和非ie9
        elem.removeEventListener(type, handle, false);
    } else if (elem.detachEvent) { //ie6到ie8
        elem.detachEvent('on' + type, handle);
    } else {
        elem['on' + type] = null;
    }
}</pre>
19、取消冒泡的兼容代码
<pre>function stopBubble(e) {
    if (e && e.stopPropagation) {
        e.stopPropagation();
    } else {
        window.event.cancelBubble = true;
    }
}</pre>
20、检验字符串是否是回文
<pre>function isPalina(str) {
    if (Object.prototype.toString.call(str) !== '[object String]') {
        return false;
    }
    var len = str.length;
    for (var i = 0; i < len / 2; i++) {
        if (str[i] != str[len - 1 - i]) {
            return false;
        }
    }
    return true;
}</pre>
21、检验字符串是否是回文
<pre>function isPalindrome(str) {
    str = str.replace(/\W/g, '').toLowerCase();
    console.log(str)
    return (str == str.split('').reverse().join(''))
}</pre>
