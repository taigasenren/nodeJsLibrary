## ejsの変数をjsで用いる方法

参考リンク
https://off.tokyo/blog/ejs%E3%81%AB%E5%AE%9A%E7%BE%A9%E3%81%97%E3%81%9F%E5%A4%89%E6%95%B0%E3%82%92js%E3%81%A7%E4%BD%BF%E3%81%86%E6%96%B9%E6%B3%95/

<% var test = 101; %> // variable created by ejs
                    <script>
                    var getTest = <%= test  %>;  //var test is now assigned to getTest which will only work on browsers
                    console.log(getTest);  // successfully prints 101 on browser
                    </script>


## ejsでクエリパラメータの取得
参考リンク
https://noumenon-th.net/programming/2018/12/21/query/

