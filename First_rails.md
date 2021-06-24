# Railsの基礎

## 基礎知識メモ

* `rails new [プロジェクト名]`でrailsプロジェクトの初期ディレクトリや必要なファイルを作成できる。

* ローカルサーバーは`rails server -b [ip]`で立てれる。
  * `-b`オプションは後に続くipアドレスにRailsのプロセスをバインドするというもの。もし、つけないなら、ループバックアドレス(**127.0.0.1**)に割り当てられる。これは、ブラウザ上からlocalhostでアクセスできる。
  * `-d`オプションをつければ、バックグラウンドで起動する。
  * デフォルトのポートは、**3000番**となっている。

* バックグラウンドで起動したサーバーを停止する方法。
  * `cat tmp/pids/server.pid`でプロセスidを調べ、`kill -9 [pid]`でkillする。
  * ちなみに**9**オプションは、強制終了。
  * `ps aux | grep puma`でpidを求めることもできる

* ログは`log/development.log`に保存されている。

### newで作成したフォルダのフォルダ構成

```bash
myblog
├── app
├── bin
├── config
├── db
├── lib
├── log
├── node_modules
├── public
├── storage
├── test
├── tmp
└── vendor
```

* たいていの場合は、**db**,**config**で設定をいじりつつ、**app**の中で、**model**,**View**,**Controller**を作り込んでいくという流れになるらしい、、、

## modelの作成

modelでは個々のデータの構造を定義する

* `rails g model [model_name] [data_name1]:[data_type]...`として、modelを作成する。

```bash
hogehoge@LAPTOP-RNJHN4U2:~/Study_ruby/myblog$ rails g model Post title:string body:text
Running via Spring preloader in process 401
      invoke  active_record
      create    db/migrate/20210624115618_create_posts.rb
      create    app/models/post.rb
      invoke    test_unit
      create      test/models/post_test.rb
      create      test/fixtures/posts.yml
hogehoge@LAPTOP-RNJHN4U2:~/Study_ruby/myblog$ 
```

* `rails db:migrate`でデータをDBに反映する

* `rails db:migrate:reset`でテーブルを全部削除して、作り直すことができる。

* テーブルの初期値を別ファイルで管理したい場合は、`[aplication]/db/seeds.rb`に書き込む。そして、`rails db:seed`とすることで、データを流し込む。

```ruby :seeds.rb
5.times do |i|
    Post.create(title: "title #{i}", body: "body #{i}")
end
```

```bash
sqlite> select * from posts;
1|title 0|body 0|2021-06-24 12:17:37.623310|2021-06-24 12:17:37.623310
2|title 1|body 1|2021-06-24 12:17:37.638070|2021-06-24 12:17:37.638070
3|title 2|body 2|2021-06-24 12:17:37.657601|2021-06-24 12:17:37.657601
4|title 3|body 3|2021-06-24 12:17:37.674952|2021-06-24 12:17:37.674952
5|title 4|body 4|2021-06-24 12:17:37.704468|2021-06-24 12:17:37.704468
sqlite> .quit
```

### 設定したDBをコンソールから操作する

* `rails db`で接続。デフォルトはsqlite3

### active_recordを利用して、データの登録、参照を行う

sql文を意識せずにDBに接続ができる。

* Postテーブルにデータを追加する例。`new`してから、`save`する方法と、`create`で登録する方法がある。登録したデータは、`.all`で全件表示できる。また、railsでは、テーブルを作成した際に、**created_at**,**updated_at**というフィールドが自動で設定されている。

```bash
irb(main):001:0> p = Post.new(title: 'title 1', body: 'body 1')
   (0.3ms)  SELECT sqlite_version(*)
=> #<Post id: nil, title: "title 1", body: "body 1", created_at: nil, updated_at: nil>
irb(main):002:0> p. save
  TRANSACTION (0.1ms)  begin transaction
  Post Create (0.4ms)  INSERT INTO "posts" ("title", "body", "created_at", "updated_at") VALUES (?, ?, ?, ?)  [["title", "title 1"], ["body", "body 1"], ["created_at", "2021-06-24 11:59:57.854384"], ["updated_at", "2021-06-24 11:59:57.854384"]]
  TRANSACTION (20.4ms)  commit transaction
=> true
irb(main):003:0> Post.create(title: 'title 2', body: 'body 2')
  TRANSACTION (0.1ms)  begin transaction
  Post Create (0.3ms)  INSERT INTO "posts" ("title", "body", "created_at", "updated_at") VALUES (?, ?, ?, ?)  [["title", "title 2"], ["body", "body 2"], ["created_at", "2021-06-24 12:01:06.835629"], ["updated_at", "2021-06-24 12:01:06.835629"]]
  TRANSACTION (20.0ms)  commit transaction
=> #<Post id: 2, title: "title 2", body: "body 2", created_at: "2021-06-24 12:01:06.835629000 +0000", updated_at: "2021-06-24 12:01:06.835629000 +0000">
irb(main):004:0> Post.all
  Post Load (0.2ms)  SELECT "posts".* FROM "posts" /* loading for inspect */ LIMIT ?  [["LIMIT", 11]]
=> #<ActiveRecord::Relation [#<Post id: 1, title: "title 1", body: "body 1", created_at: "2021-06-24 11:59:57.854384000 +0000", updated_at: "2021-06-24 11:59:57.854384000 +0000">, #<Post id: 2, title: "title 2", body: "body 2", created_at: "2021-06-24 12:01:06.835629000 +0000", updated_at: "2021-06-24 12:01:06.835629000 +0000">]>
```

## Controllerを作成する

* `rails g controller [controller_name]`:controller_nameは複数形で記述する

```bash
~/Study_ruby/myblog$ rails g controller Posts
Running via Spring preloader in process 1185
      create  app/controllers/posts_controller.rb
      invoke  erb
      create    app/views/posts
      invoke  test_unit
      create    test/controllers/posts_controller_test.rb
      invoke  helper
      create    app/helpers/posts_helper.rb
      invoke    test_unit
      invoke  assets
      invoke    scss
      create      app/assets/stylesheets/posts.scss
```

### ルーティングを設定する

ルーティングとは、どのURLにアクセスされたときに、controllerのどのメソッドを実行するかの設定。`[application]/config/routes.rb`に設定する。

* `resources :posts`と記述すれば、一般的な処理を一気に設定可能

### controllerにアクションを作成する

`[application]/app/controllers/`に作成したcontrollerのファイルができている。今回は、indexというアクションを作成

```ruby :posts_controller.rb
class PostsController < ApplicationController

    def index
        @posts = Post.all.order(created_at: 'desc')
    end
end
```

### Viewを作成する

controllerに対応するViewは`[application]/app/views/`のなかに、controller名のフォルダがあり、その中に`アクション名.html.erb`のファイルを作成し記述していけばよい。

* erb内では、アクションで設定したインスタンス変数を使うことができる。

```erb :index.html.erb
<h2>My Posts</h2>
<ul>
<% @posts.each do |post| %>
<li><%= post.title %></li>
<% end %>
</ul>
```