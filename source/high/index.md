---
title: 一起嗨起来
date: 2016-01-13 18:46:30
categories: 嗨起来
comments: false

---

{% cq %} 无聊的时候，可以过来嗨一下 {% endcq %}
<a href="javascript:high()" class="button button-block button-rounded button-large">点我嗨起来</a>

<iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width=330 height=450 src="http://music.163.com/outchain/player?type=0&id=6556947&auto=1&height=430"></iframe>

<style type="text/css">
.button {
color: #666;
    background-color: #EEE;
    border-color: #EEE;
    font-weight: 300;
    font-size: 16px;
    font-family: "Helvetica Neue Light", "Helvetica Neue", Helvetica, Arial, "Lucida Grande", sans-serif;
    text-decoration: none;
    text-align: center;
    line-height: 40px;
height: 40px;
padding: 0 40px;
margin: 0;
display: inline-block;
appearance: none;
cursor: pointer;
border: none;
    -webkit-box-sizing: border-box;
    -moz-box-sizing: border-box;
    box-sizing: border-box;
    -webkit-transition-property: all;
    transition-property: all;
    -webkit-transition-duration: .3s;
    transition-duration: .3s;}
.button-block,
.button-stacked {
    display: block; }
.button-rounded {
    border-radius: 4px; }
.button-large {
    font-size: 20px;
    height: 50px;
    line-height: 50px;
    padding: 0 50px; }
</style>



<script>
function high() {
	function c() {
		var e = document.createElement("link");
		e.setAttribute("type", "text/css");
		e.setAttribute("rel", "stylesheet");
		e.setAttribute("href", f);
		e.setAttribute("class", l);
		document.body.appendChild(e)
    }
    
    function h() {
        var e = document.getElementsByClassName(l);
        for (var t = 0; t < e.length; t++) {
            document.body.removeChild(e[t])
        }
    }
    
    function p() {
        var e = document.createElement("div");
        e.setAttribute("class", a);
        document.body.appendChild(e);
        setTimeout(function() {
            document.body.removeChild(e)
        }, 100)
    }
 
    function d(e) {
        return {
            height : e.offsetHeight,
            width : e.offsetWidth
        }
    }
 
    function v(i) {
        var s = d(i);
        return s.height > e && s.height < n && s.width > t && s.width < r
    }
 
    function m(e) {
        var t = e;
        var n = 0;
        while (!!t) {
            n += t.offsetTop;
            t = t.offsetParent
        }
        return n
    }
 
    function g() {
        var e = document.documentElement;
        if (!!window.innerWidth) {
            return window.innerHeight
        } else if (e && !isNaN(e.clientHeight)) {
            return e.clientHeight
        }
        return 0
    }
 
    function y() {
        if (window.pageYOffset) {
            return window.pageYOffset
        }
        return Math.max(document.documentElement.scrollTop, document.body.scrollTop)
    }
 
    function E(e) {
        var t = m(e);
        return t >= w && t <= b + w
    }
 
    function S() {
        var e = document.getElementById("audio_element_id");
        if(e === null){
            e = document.createElement("audio");
            e.setAttribute("class", l);
            e.id = "audio_element_id";
            e.loop = false;
            e.addEventListener("canplay", function() {
            setTimeout(function() {
                x(k)
            }, 500);
            setTimeout(function() {
                N();
                p();
                for (var e = 0; e < O.length; e++) {
                    T(O[e])
                }
            }, 15500)
        }, true);
        e.addEventListener("ended", function() {
            N();
            h()
        }, true);
        e.innerHTML = " <p>If you are reading this, it is because your browser does not support the audio element. We recommend that you get a new browser.</p> <p>";
        document.body.appendChild(e);
        } else {
            N();
        }

        e.src = i;
        e.play()
    }
 
    function x(e) {
        e.className += " " + s + " " + o
    }
 
    function T(e) {
        e.className += " " + s + " " + u[Math.floor(Math.random() * u.length)]
    }
 
    function N() {
        var e = document.getElementsByClassName(s);
        var t = new RegExp("\\b" + s + "\\b");
        for (var n = 0; n < e.length; ) {
            e[n].className = e[n].className.replace(t, "")
        }
    }
    
    var e = 30;
    var t = 30;
    var n = 350;
    var r = 350;

    var number = Math.floor(Math.random()*2);
    if(number == 0)
      var i = "http://7sbydq.com1.z0.glb.clouddn.com/The XX - Intro - intro.mp3";
    else 
      var i = "http://rocko-blog.qiniudn.com/Music-outside.mp3";

    var s = "mw-harlem_shake_me";
    var o = "im_first";
    var u = ["im_drunk", "im_baked", "im_trippin", "im_blown"];
    var a = "mw-strobe_light";

    var f = "http://zhengxiaopeng.com/css/harlem-shake-style.css";
    
    var l = "mw_added_css";
    var b = g();
    var w = y();
    var C = document.getElementsByTagName("*");
    var k = null;
    for (var L = 0; L < C.length; L++) {
        var A = C[L];
        if (v(A)) {
            if (E(A)) {
                k = A;
                break
            }
        }
    }
    if (A === null) {
        console.warn("Could not find a node of the right size. Please try a different page.");
        return
    }
    c();
    S();
    var O = [];
    for (var L = 0; L < C.length; L++) {
        var A = C[L];
        if (v(A)) {
            O.push(A)
        }
    }
}
</script>

