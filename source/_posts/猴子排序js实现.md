---
title: 猴子排序js实现
date: 2017-11-27 10:05:41
tags: 前端
---


无限猴子定理 ：一只猴子随机敲打打字机键盘，如果时间足够长，总是能打出特定的文本，比如莎士比亚全集。

而猴子排序其实就是 不停地随机排序,直到产生有序数列为止
具体js实现如下

function bogoSort (arr) {

    var n = 0, ordered = false;

    function shuffle(array) {
        for (var i = 0,length = arr.length; i < length; i++) {
            var j = Math.floor(Math.random() * length);
            [arr[i], arr[j]]=[arr[j], arr[i]];
            n++;
        }
    }

    function isOrdered(array) {
        for (var i = 1,length = arr.length; i < length; i++) {
            if (arr[i] < arr[i - 1]) {
                return false;
            }
        }
        return true;
    }

    while (ordered == false) { // 效率低下的位置
        v = shuffle(arr);
        ordered = isOrdered(arr);
    }
    console.log('计算了'+n+'次')
    return arr;
}
测试效果

![image](http://i2.51cto.com/images/blog/201711/27/b665fc4016eb034ea6de1839accdcb7c.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)