# 321-development-of-API
3/21のCODE BASEプログラミングスクール卒業生イベント

## はじめに

今回はまずは簡単なタスク管理アプリを想定したWeb APIの開発を行います。DBとAPIのリクエスト, レスポンスはこちらで指定するので皆さんはそのとおりに動くようにWeb APIを実装してください！

## 注意事項
- 指定したAPIのリクエスト方法とレスポンスが返るようになっていれば、実装方法や使用する言語, フレームワークは基本的に自由です！
- ただし言語, フレームワークが同じ人は同じシマでそれぞれ開発をお願いします！(Railsチーム, Laravelチームなど...)
- 基本的に自分でコードを書くようにしたいですが、わからないことがあれば同じシマで助け合いましょ〜✨
- それぞれ自分の環境で指定したとおりに動くことを目標に頑張っていきましょう〜
- すべて終わった人は、自分で作りたいアプリのAPIを開発してみてください！作りたいものがなければタスク管理APIの拡張やTwitterのクローンAPIなど...

## 予備知識
### JSON
JSONとは、一言で言うとざっくりと書いたJavaScript。JavaScriptだけでなくあらゆるソフトウェアやプログラミング言語間で扱うことができるデータ形式。

例. 
```
[
  {
    "id": 2,
    "name": "puremoru0315",
    "description": "沖縄生まれ沖縄育ちで〜す",
  },
  {
    "id": 1,
    "name": "ibuki0315",
    "description": "東京生まれ東京育ちで〜す",
  }
]
```
iOSやAndroid, Web AppのフロントからサーバーサイドのAPIを叩いて目的のレスポンスをJSON形式で得ることが多い。


### curlコマンド
curlとは、サーバーからデータを取得したり、サーバーへデータ転送を行うコマンド。
ローカルで開発するAPIの挙動を確認するときにもよく使われる。
インストールしていない人はこちらから↓
https://qiita.com/kaizen_nagoya/items/f13df3e2c9fe6c3bf6fc

実際に使ってみましょう！
```
$ curl -X GET http://zipcloud.ibsnet.co.jp/api/search?zipcode=<郵便番号>
```

以下のようなレスポンスが返ってくると思います。
```
{
	"message": null,
	"results": [
		{
			"address1": "沖縄県",
			"address2": "宜野湾市",
			"address3": "我如古",
			"kana1": "ｵｷﾅﾜｹﾝ",
			"kana2": "ｷﾞﾉﾜﾝｼ",
			"kana3": "ｶﾞﾈｺ",
			"prefcode": "47",
			"zipcode": "9012214"
		}
	],
	"status": 200
}
```
## HTTPメソッド
HTTPメソッドとは、クライアントが行いたい処理をサーバに伝えるという役割があるがメソッドはたったの８つだけ。しかもメインで使うのは5つか６つ！
```
GET: リソースの取得(Twitterのタイムラインやキーワード検索の結果など)
POST: リソースへのデータを新規で追加(テキストの投稿機能など)
PUT: リソースの既存のデータの更新(プロフィール情報の編集など)
PATCH: こちらも既存のデータの更新, PUTより編集の対象が少ないイメージ(通知が来てほしいメールアドレスの変更など)
DELETE: 指定したデータの削除(昔つぶやいたテキストの削除など)
```

## HTTPステータスコード
Webサーバーからのレスポンスの意味を表す三桁のコード
https://ja.wikipedia.org/wiki/HTTP%E3%82%B9%E3%83%86%E3%83%BC%E3%82%BF%E3%82%B9%E3%82%B3%E3%83%BC%E3%83%89
## ER図
![エビフライトライアングル](https://user-images.githubusercontent.com/22650969/54676136-53109b00-4b43-11e9-864b-48ec852c48dc.png "サンプル")
しんぷる...！

## Web API設計書
### ユーザー新規登録API
- request 
```
curl --request POST
--url http://localhost:3000/api/v1/users
--header 'Content-Type: application/json'
--data '{ 
"name": "testuser"
"email": "mizutama12120329@gmail.com",
"password": "password"
}
'
```
- response
```
{"status": 200, message: "ユーザー登録が完了しました"}
```
- メールアドレスとパスワードそれぞれの入力チェックも
```
{"status": 400, "message": "メールアドレスを入力してください"}
```
### ユーザーログインAPI
- request
```
curl --request POST
--url http://localhost:3000/api/v1/users/login
--header 'Content-Type: application/json'
--data '{ 
"email": "mizutama12120329@gmail.com",
"password": "password"
}
'
```
- response 
```
{"access_token": xxxxxxxx}
```
- メールアドレスとパスワードそれぞれの入力チェックも
```
{"status": 400, "message": "メールアドレスを入力してください"}
```
- 入力されたアドレスとパスワードからユーザーが見つからなかったとき
```
{"status": 401, "message": "認証に失敗しました"}
```
### ユーザー一覧API
- request
```
$ curl --request GET \
--url http://localhost:3000/api/v1/users \
--header 'Content-Type: application/json' \
```
- response
```
[
  {
    "name": "puremoru0315",
  },
  {...
  }
]
```

### ユーザー詳細API
- request
```
$ curl --request GET \
--url http://localhost:3000/api/v1/users/1 \
--header 'Content-Type: application/json' \
```
- response
```
{"name": "puremoru0315", "description": "hogehoge, hello, world"}
```
### 自分のタスク一覧API
- request
```
$ curl --request GET \
--url http://localhost:3000/api/v1/todos \
--header 'Authorization: Basic eyJhbGciOiJIUzI1NiJ9.eyJlbWFpbCI6ImNvY29va2luZ0BsaXZlLmpwIiwicGFzc3dvcmQiOiJjb2Nvb2tpbmcifQ.X5TV5RG_SlFSUl_8-QU3n5PixmVpex2Nsp1ziWryeXc' \
--header 'Content-Type: application/json' \
```
- response 
```
[
  {
    "title": "たすく1",
    "status": 1
  }, 
  {
    "title": "たすく2",
    "status": 0
  }, ....
]
```
- ログイン時に発行されたトークンを渡していないとき, またはトークンが不正のとき
```
{"status": 401, "message": "ログインしてください"}
```
### 自分のタスクの詳細API
- request 
```
$ curl --request GET \
--url http://localhost:3000/api/v1/todos/2 \
--header 'Authorization: Basic eyJhbGciOiJIUzI1NiJ9.eyJlbWFpbCI6ImNvY29va2luZ0BsaXZlLmpwIiwicGFzc3dvcmQiOiJjb2Nvb2tpbmcifQ.X5TV5RG_SlFSUl_8-QU3n5PixmVpex2Nsp1ziWryeXc' \
--header 'Content-Type: application/json' \
```
- response 
```
{"title": "たすく1", "description": "これが終わったら遊べるぞー", "status": 0}
```
- ログイン時に発行されたトークンを渡していないとき, またはトークンが不正のとき
```
{"status": 401, "message": "ログインしてください"}
```
- トークンは正しいけど他のユーザーのタスクなので見る権限がないとき
```
{"status": 403, "message": "権限がありません"}
```
### 自分のタスク作成API
- request
```
$ curl \             
-F title=たすく \
-F description=テスト説明 \
http://localhost:3000/api/v1/todos \
-H 'Authorization: Token eyJhbGciOiJIUzI1NiJ9.eyJlbWFpbCI6ImZhdHpzam9uQGdtYWlsLmNvbSIsInBhc3N3b3JkIjoiZmF0empvbjEyMiJ9.Ei1Ojd6dbSOJmJAqmOOmH3TBEFDKnNKVpdsxmB2l9rc' \
```
- response 
```
{"status": 200, "message": "タスクを作成しました"}
```
- タスクのタイトルの入力チェック
```
{"status": 400, "message": "タイトルを入力してください"}
```
- `status`の初期値は0(未着手)にする
### 自分のタスクのステータス更新API
- 未着手→着手中, 着手中→済にそれぞれ更新する
- 自分以外が更新できないようにする
- request 
```
$ curl --request PATCH \
--url http://localhost:3000/api/v1/todos/1 \
--header 'Authorization: Basic eyJhbGciOiJIUzI1NiJ9.eyJlbWFpbCI6ImNvY29va2luZ0BsaXZlLmpwIiwicGFzc3dvcmQiOiJjb2Nvb2tpbmcifQ.X5TV5RG_SlFSUl_8-QU3n5PixmVpex2Nsp1ziWryeXc' \
--header 'Content-Type: application/json' \
```
- response 
```
{"status": 200, "message": "タスクのステータスを更新しました"}
```
- ログイン時に発行されたトークンを渡していないとき, またはトークンが不正のとき
```
{"status": 401, "message": "ログインしてください"}
```
- トークンは正しいけど他のユーザーのタスクなので権限がないとき
```
{"status": 403, "message": "権限がありません"}
```
### 自分のタスクの削除API
- 自分以外が更新できないようにする
- request
```
$ curl --request DELETE \
--url http://localhost:3000/api/v1/todos/1 \
--header 'Authorization: Basic eyJhbGciOiJIUzI1NiJ9.eyJlbWFpbCI6ImNvY29va2luZ0BsaXZlLmpwIiwicGFzc3dvcmQiOiJjb2Nvb2tpbmcifQ.X5TV5RG_SlFSUl_8-QU3n5PixmVpex2Nsp1ziWryeXc' \
--header 'Content-Type: application/json' \
```
- response 
```
{"status": 200, "message": "タスクを削除しました"}
```
- ログイン時に発行されたトークンを渡していないとき, またはトークンが不正のとき
```
{"status": 401, "message": "ログインしてください"}
```
- トークンは正しいけど他のユーザーのタスクなので権限がないとき
```
{"status": 403, "message": "権限がありません"}
```
