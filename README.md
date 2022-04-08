身处上海，抢菜很难。更难的是，每天 5:50 起床还抢不到菜，搞得人身心俱疲。今天发现了一个可以在安卓机上运行脚本的工具——[Auto.js](https://g.pro.autojs.org/docs/#/zh-cn/)，简单的写了个脚本用来抢菜。

## 美团买菜版本

这个脚本适用于美团买菜 v5.32.0，不要用最新的版本。
旧版本的获取方法：
下载一个”豌豆荚“app，或者在网页上搜索豌豆荚，然后在里面搜索应用名称，最下面会有一个“历史版本”，找到 v5.32.0 并安装，注意安装前需要先卸载掉美团买菜。

![image-20220408161750722](https://gitee.com/qulingyuan/ly_picture/raw/master/img/image-20220408161750722.png)

## 开发步骤：

1. 下载 `Auto.js` 安装包，目前官网提供的是 `Auto.js Pro`，是收费的，45 块钱买断制，还是良心的，建议大家购买。不过这里也分享一下免费版本(非破解版，是开始收费前的免费版，网上听说很多版本是有毒的，这个是我以身试毒后确认无毒的，大家可放心使用)—— 链接: https://pan.baidu.com/s/1zBTij7imoLCKf1bm7HtY6Q 提取码: n9jc 或通过 github 下载 [https://github.com/qulingyuan/robVeg/tree/master/assets/commonRelease-4.1.0%20Alpha2.apk](https://github.com/qulingyuan/robVeg/tree/master/assets/commonRelease-4.1.0%20Alpha2.apk)

```
Name: commonRelease-4.1.0 Alpha2.apk
Size: 14582300 bytes (13 MiB)
CRC32: 3A990E03
CRC64: EEB9E7ABDD3B7098
SHA256: 948a87427f460b046b066fd4fd5599e34d33abe7aaaf6ca56904400996eecf11
SHA1: c54e6d11c899f39cfd388ee12f837e1613d87a7d
BLAKE2sp: adcfab5219b9bbcd7b715bf1d4bab4e15078b1fbf1098a3e872fcfae285bfe00
```

2. 安装软件后，开启应用的**无障碍**和**悬浮窗**功能。

![image-20220406221255685](https://gitee.com/qulingyuan/ly_picture/raw/master/img/image-20220406221255685.png)

3. 可以使用悬浮窗的工具来检查 app 的元素。

   ![image-20220406225906556](https://gitee.com/qulingyuan/ly_picture/raw/master/img/image-20220406225906556.png)

   ![image-20220406225941640](https://gitee.com/qulingyuan/ly_picture/raw/master/img/image-20220406225941640.png)

4. 打开 VS Code，安装`Auto.js-VSCodeExt`插件。

![image-20220406222424648](https://gitee.com/qulingyuan/ly_picture/raw/master/img/image-20220406222424648.png)

5. `Command+shift+P`快捷键打开 `Command Palette`，运行 `Auto.js:New Project`命令创建一个新项目。

![image-20220406222958526](https://gitee.com/qulingyuan/ly_picture/raw/master/img/image-20220406222958526.png)

6. 在 main.js 文件中编写脚本：

```js
function robVeg() {
  launchApp("美团买菜");
  waitForPackage("com.meituan.retail.v.android", 200);
  auto.waitFor();
  const btn_skip = id("btn_skip").findOne();
  if (btn_skip) {
    btn_skip.click();
    toast("已跳过首屏广告");
  }
  sleep(1000);
  gotoBuyCar();
  sleep(1000);
  checkAll();
}

robVeg();

//打开购物车页面
function gotoBuyCar() {
  const buyCarBtn = id("cartredDotTextView").findOne();
  if (buyCarBtn) {
    buyCarBtn.parent().click();
    toast("已进入购物车");
  } else {
    toast("没找到购物车");
    exit;
  }
}

//勾选全部商品
function checkAll() {
  const isCheckedAll = textStartsWith("结算(").exists();
  const checkAllBtn = text("全选").findOne();
  if (!!checkAllBtn) {
    !isCheckedAll && checkAllBtn.parent().click();
    sleep(1000);
  } else {
    toast("没找到全选按钮");
    exit;
  }
}
```

7.运行 `Auto.js:Start Server` 命令启动服务，手机和电脑在同一局域网的情况下打开”连接电脑“选项，连接成功后，VS Code 会弹出提示。然后就可以愉快地改代码调试了。

![](assets/20220407_123340__.png)

8.跑代码

![](assets/20220407_123325__mian.js.png)

![](assets/20220407_123317__.png)
开发调试开启日志，手机需要开启悬浮窗。

![](assets/Screenshot_20220407-123018_美团买菜.png)

> https://g.pro.autojs.org/docs/#/zh-cn/console

9.调试完成后，确定代码可以正常运行了，可以在 Auto.js 上新建一个脚本文件，把写好的代码放进去，然后点击文件右侧的三个点->更多->定时任务，可以设置成早上 6 点。

![image-20220408164107042](https://gitee.com/qulingyuan/ly_picture/raw/master/img/image-20220408164107042.png)



![image-20220408164221962](https://gitee.com/qulingyuan/ly_picture/raw/master/img/image-20220408164221962.png)

## 项目中容易踩到的坑，给大家提个醒：

1. 一定要打开无障碍功能！如果发现无法选取元素了或者什么其他异常，大概率是无障碍功能关闭了，是的，无障碍功能有时候会自动关闭。
2. 手机的 GPS 打开。
3. 需要提前把商品添加到购物车里，不然购物车是空的，啥也买不了。
3. 注意美团买菜的版本，不同版本元素不一样，代码执行不了的。

## 原理

这个脚本的原理是找到屏幕上对应的按钮，模仿人的点击等手势动作，把抢菜的一系列操作封装成脚本。然后 Auto.js 有定时执行脚本功能，这样就不用早上六点钟起来自己点了。Auto.js 的功能很强大，网上搜资料的时候发现还可以搞支付宝的蚂蚁森林的能量收集，还有游戏脚本啥的，后面还可以研究研究。
