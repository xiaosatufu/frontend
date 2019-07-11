# APICloud

## 页面传值

- 使用参数形式

  ```js
  //传参 
  api.openFrame({
              name: obj.frameName,
              url: './html/' + obj.frameName + '.html',
              bounces: true,
              rect: {
                  // x: 0,
                  // y: 0,
                  marginTop: obj.marginTop,
                  marginBottom: obj.marginBottom,
                  w: 'auto',
                  // h: 'auto'
              },
              pageParam: {
                  headerH: headerH,
                  footerH: footerH
              }
          });
  //调用
  api.pageParam.headerH
  api.pageParam.footerH
  ```

  api.execScript

  1.我们常常将标题头写在一个以.win.html结尾的文件，将内容写在以.frm.html结尾的文件。

  在.win.html里面使用openFrame开发规定的内容文件，如果在内容frm页面传递到win页面，可以使用api.execScript方法

  例如在order_time_setting_frm.html文件里：

  ```
  var jsfun = "setTimeVal('"+ time1 +"','" + time2 + "')";
  api.execScript({
      name: 'order_time_setting',
      script: jsfun
  });
  ```

  然后在order_time_setting_win.html文件里接受参数：（注意方法名要一样）

  ```
  // 获取设置区间的时间值
  var secTime1 = '';
  var secTime2 = '';
  function setTimeVal (time1, time2) {
      secTime1 = time1;
      secTime2 = time2;
  }
  ```

  

  ## 切换刷新问题

  切换frame组中的页面时，是不会每次都刷新的；

  但是如果想要每次刷新，获取最新数据，该如何是好？

  setFrameGroupIndex来帮你：

  ```js
  api.setFrameGroupIndex({
      name: 'group1',
      index: index,
      reload: true // 设置为true
  });
  ```

  

  

  

  ```js
  
  ```

  

   

  