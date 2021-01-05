## node.jsをmySqlに接続
npm install mysql

mysqlパッケージを読み込み、createConnectionメソッドを用います。データベースに接続するための情報を定数connectionに代入

//mysqlパッケージを読み込み
const mysql = require("mysql");

//接続情報を定数connectionに代入する
const connection = mysql.createConnection({
    //データベース名、パスワードなど入れる
});

## クエリの実行 
connection.query('クエリ', クエリ実行後の処理)と書くことで、Node.jsからデータベースに対してクエリを実行することができます。

const connection = mysql.createConnection({                        
    connection.query(
        //クエリ
        'SELECT*FROM items',
        //resultsに実行結果が入る
        (error,results) => {
            console.log(results);
            //renderメソッドの第2引数に{プロパティ : 値}と書くとEJS側に値を渡せる
            res.render("index.ejs",{items:results});
        }
    );
});

## クエリ実行後の処理
クエリ実行後の処理は2つの引数を取ることができます。
第1引数のerrorにはクエリが失敗したときのエラー情報が、第2引数のresultsにはクエリの実行結果が入ります。

## 取得した値の表示 EJSに値を渡す
EJSはrenderメソッドから値を受け取ることができます。renderメソッドの第2引数に{プロパティ : 値}と書くことで、EJS側に値を渡すことができます。今回はデータベースから取得した値を使いましょう。

## app.post
データの追加はpost文
postでリクエストする時は入力する項目がなくてもフォームを使う

Webでは、サーバーへリクエストするときに、どんな処理をしたいかをメソッドで伝えるようにルールで決まっています。
formはこんな書き方　methodにpostかget actionにルーティング先
<form action = "/create" method = "post">
          <input type="text">
          <input type="submit" value="作成する">
        <!-- formタグの閉じタグを書いてください -->
</form>

## フォームの値を受け取る
app.use(express.urlencoded({extended: false}));

app.post("/create",(req,res)=>{
    //フォームの値を取得する
    console.log(req.body.itemName);
})

## データの追加
SELECTの時と同様にqueryメソッドを使うことで　INSERTを実行することができます。itemsテーブルのidにはAUTO INCREMENTを設定しているので、idの値を指定する必要はありません。

フォームからの値をクエリに使うときは、VALUESに「?」を含めます。次
に「connection.query()」の第2引数に渡したい配列を指定します。この配列の要素が「?」の部分に入り、実行されます。

app.post("/create",(req,res)=>{
    connection.query(
        //idカラムはAuto incrementが原則設定されているので指定不要
        'INSERT INTO items (name) VALUES (?)',
        //配列の要素が？に入る
        [req.body.itemName],
        (error,results) => {
            //クエリ実行後の処理が下に入る
            connection.query(
                'SELECT * FROM items',
                (error, results) => {
                res.render('index.ejs', {items: results});
                }
            );
        }
    );
})

## リダイレクトとは
リロードは直前のリクエストを実行する機能。そのため、一覧表示と一緒に追加処理も実行されてしまう

サーバーは「次はこのURLにリクエストしてね」というレスポンスを返すことができます。このレスポンスを受け取ったブラウザは指定されたURLに自動的にリクエストします。このような別のURLに再度リクエストさせる仕組みをリダイレクトと言います。

リダイレクトするにはres.redirectメソッドを用い、引数にURLを指定します。

リダイレクトを使う場面はいくつかありますが、よくあるのは今回のようにpostメソッドでリクエストした時です。こういう時はres.renderではなくres.redirectを使って、getのルーティングにリダイレクトしてあげましょう。

res.redirect("/url")

## データから削除
削除にはDELETEクエリを使います。しかし現状では、サーバーはどのメモをWHEREに指定すれば良いか分からず削除できません。右のように、サーバーが削除するメモのidを受け取ることができれば、削除できるようになります。

メモのidを受け渡すにはURLを利用します。リクエストするURLは/delete/3のようにidを含めるようにし、ルーティングのURLは/delete/:idのように指定します。これでURLに含まれたidを取得できるようになります。/:idの部分をルートパラメータと呼びます。

## ルートパラメータの使用
//送信先urlにメモのidを含める
<form action = "/delete/<%= item.id %>" method = "post">
          <input type="submit" value="削除">
</form>

//:idの部分がルートパラメータ
app.post("/delete/:id",(req,res)=>{
    //中の処理
    //上で設定したルートパラメータがidとして今入っている
    console.log(req.params.id)
});

req.params.ルートパラメータ名でルートパラメータの値を受け取ることができます。console.logで出力して内容を確認可能

