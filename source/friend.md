---
title: 朋友们
layout: friends
cover: https://picxyxsw.oss-cn-hangzhou.aliyuncs.com/illust_82448502_20220605_001853.jpg
---

- # [L0tus](https://l0tus.vip)
- # [Ma5hr00m](http://ma5hr00m.top/)
- # [BaiMeow](https://baimeow.cn/)
- # [AsakiRain](https://irain.cc/)
- # [MarleneJ](http://www.marlenej.com/)
- # [Esonhugh](https://eson.ninja/)
- # [t0hka](https://blog.t0hka.top/)
- # [daidr](https://daidr.me/)

<style>
  ul li{
    list-style: none;
    font-size: 1.5em;
    font-family: consolas;
    /* width: 600px; */
    line-height: 50px;
    text-align: center;
    height: 50px;
    position: relative;
    color: rgb(168, 168, 168);
    transition:color 0.1s, font-size 0.1s,transform 0.1s;
  }
  ul li:hover{
      color: rgb(255, 255, 255);
      font-size: 1.8em;
      transform: rotate(-0.5deg);
  }
  ul li::before{
      content: "";
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      background: rgba(230, 114, 73,0.6);
      border-radius: 25px;
      z-index: -1;
      transform: scaleY(0);
      transition: transform 0.1s;
      transform-origin: bottom;
  }
  ul li:hover::before{
      transform: scaleY(1);
      transition: transform 0.1s;
      transform-origin: top;
  }
  ul li::after{
      content: "";
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      background: rgba(230, 114, 73,0.4);
      border-radius: 25px;
      z-index: -2;
      transform: scaleY(0);
      transition: transform 0.1s;
      transform-origin: bottom;
      filter: blur(40px);
  }
  ul li:hover::after{
      transform: scaleY(1);
      transition: transform 0.1s;
      transform-origin: top;
  }
</style>