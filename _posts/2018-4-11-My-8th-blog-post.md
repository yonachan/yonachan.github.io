# nekotterの作成手順

### 事前準備
view(html,cssの部分)は事前に用意してあるviewをダウンロードしましょう

```
#projectsのディレクトリに移動する
$cd ~/projects
#projects以下に、nekotterのhtml/cssをダウンロード
$git clone https://github.com/georgesekkie/nekotter.git nekotter_only_html_css

```
### railsアプリケーションを作成
```
$rails new nekotter -d mysql
```
Gemfile
```
gem 'mysql2', '~> 0.3.18'
```
```
$cd nekotter
$bundle install
```
データベースを作成
```
$rake db:create
```
これで事前準備は完了

### ルーティング、コントローラー、ビューをそれぞれ作成
routes.rb
```
Rails.application.routes.draw do
  get 'tweets' => 'tweets#index'
end
```
```
$ rails g controller tweets
```
app/controllers/tweets_controller.rb
```
class TweetsController < ApplicationController
  def index
  end
end
```
### ダウンロードした以下の3つのファイルを組み込む
1. views/tweetsファイルの中にindex.html.erbを作成し、nekotter_only_html_cssのindex.htmlの記述を貼り付け

2. nekotter_only_html_cssのstyle.cssの記述をapp
 assets/stylesheets/tweets.scssに貼り付け

3. nekotter_only_html_cssにあるcat1.png をapp/assets/imagesディレクトリ下に置く

### index.html.erbにあるcat1.pngが表示できるように
```
<div class="message clearfix">
  <%= image_tag 'cat1.png' %>
  <div class="message_box">
  </div>
</div>
```

テーブルに保存されたレコードの情報が表示される為に
![](https://tech-master.s3.amazonaws.com/uploads/curriculums//6f1629eaca7da1bb09fac25fcaa577ee.png)

### tweetモデルを作成
```
$rails g model tweet
```
db/migrate/2015XXXXXXXXXXXX_create_tweets.rb
```
class CreateTweets < ActiveRecord::Migration
  def change
    create_table :tweets do |t|
→     t.text        :text
      t.timestamps
    end
  end
end
```
null: falseは削除
```
$ rake db:migrate
```
### SequelProにあるtweetsテーブルのレコードを3つ作成
![](https://tech-master.s3.amazonaws.com/uploads/curriculums//8ad07ef5d1be1b5c1f0d0171268968d3.png)

app/controlers/tweets_controller.rb
```
class TweetsController < ApplicationController

  def index
→   @tweets = Tweet.all
  end

end
```
### Tweetモデルから取得した内容を出力できるように

### textの部分を変数にし、tweetsテーブルのtextカラムから引用してきたテキストを表示できるように

```
    <header>
      <h1>ねこったー</h1>
    </header>
    <div class="contents">
→    <% @tweets.each do |tweet| %>
        <div class="message clearfix">
          <%= image_tag 'cat1.png' %>
          <div class="message_box">
            <p class="user_name">
              野良猫1号
            </p>
            <p class="text">
→            <%= tweet.text %>
            </p>
          </div>
        </div>
→    <% end %>
    </div>
```
### サーバーを起動して表示されるか確認
$rail s

投稿機能の実装
![](https://tech-master.s3.amazonaws.com/uploads/curriculums//5e947e6fe93eb01085d039b9a5326b1a.png)
### 新規投稿画面に遷移するためのルーティング→コントローラ→ビューをそれぞれ作成
config/routes.rb
```
Rails.application.routes.draw do
  get 'tweets' => 'tweets#index'
  get 'tweets/new' => 'tweets#new'
end
```
app/controller/tweets_controller.rb
```
class TweetsController < ApplicationController
  def index
    @tweets = Tweet.all
  end

  def new
  end
end
```
新しいファイルnew.html.erbを作成  

事前準備でダウンロードしたnew.htmlの中の記述を先ほど作成したnew.html.erbに貼り付け  
app/views/tweets/new.html.erb  
```
<h1>ねこったー</h1>
</header>
<div class="contents">
  <h2>投稿する</h2>
    <form class="clearfix">
      <img src="/assets/cat1.png">
      <div class="input-box">
        <textarea rows="4" cols="40">今日も疲れたにゃー</textarea>
      </div>
      <div class="button-box">
        <input type="submit" class="submit-button">
      </div>
    </form>
</div>
```
### new.html.erbで投稿フォームをform_tagを使って作成しましょう
app/view/tweets/new.html.erb
```
<div class="contents">
  <h2>投稿する</h2>
→ <%= form_tag('/tweets', method: :post, class: "clearfix") do %>
→   <img src="/assets/cat1.png">
→   <textarea cols="30" name="text" rows="4"></textarea>
    <div class="button-box">
      <input type="submit" class="submit-button">
    </div>
→ <% end %>
</div>
```

### ツイートを投稿するために新たに、ルーティング→コントローラ→ビューをそれぞれ作成

config/routes.rb
```
post 'tweets' => 'tweets#create'
```
app/controllers/tweets_controller.rb
```
def create
  Tweet.create(tweet_params)
end

private
def tweet_params
  params.permit(:text)
end
```
app/views/tweets/create.html.erb
```
<p>投稿が完了しました！</p>
<%= link_to "投稿フォームへ戻る", "/tweets/new" %>
```
ブラウザで下記のURLに飛び、投稿画面を確認
http://localhost:3000/tweets/new

nekotterの実装は以上です。


## その他機能追加
* トップページに表示させる
* orderメソットを追加 並び順  
* ページネーションを実装 ページの部分  
* ログイン機能追加
