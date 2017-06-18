# ドリコムで使ってるgem一覧
sue445

2017/06/22 [Rails Developers Meetup #2](https://rails-developers-meetup.connpass.com/event/58046/)

---
## 自己紹介 [![sue445](images/sue445.png)](https://twitter.com/sue445)

* Go Sueyoshi (a.k.a [@sue445](https://twitter.com/sue445))
* [株式会社ドリコム](http://www.drecom.co.jp/) 所属
  * 2012年入社（今年6年目）
  * インフラストラクチャー部で社内gemを中心にアプリからインフラまでだいたいサーバサイドを浅く広く見てる
  * **ソシャゲのバックエンドチョットワカル**
* 今まで作った主要なものは [sue445 Advent Calendar 2016 - Qiita](http://qiita.com/advent-calendar/2016/sue445) を参照
* **プリキュアのカバレッジ100%**
  * 初代から現行までテレビと映画全部見てる
  * リアルタイムでの視聴は半分くらい

---
### 今期の嫁：キュアカスタード
![cure custard](images/cure_custard.jpg)

---
### 本気の嫁：キュアピース
![cure peace](images/cure_engineer_peace.jpg)

---
## 今日話すこと
* たくさんのRailsアプリを運用してるドリコムでよく使われてるgemの紹介

---
## アジェンダ
* ドリコムとRails
* かぶりgem集計
* ドリコムでよく使われてるけど他ではあまり使われてなさそうなgemを紹介

---
## ドリコムとRails
* Railsは2006年くらいから利用 (当時Rails 1.0)
* 現在本番環境で動いてるアプリだけでも10個以上
  * ネイティブアプリのサーバサイドやwebサービスなど
* 開発中のアプリ、クローズしたアプリ、社内アプリも含めたらもっとある

---
## かぶりgem集計
* ここ数年でリリース or 現在開発中のアプリのアプリのGemfileを実際にparseして集計

---
### 集計スクリプト
https://gist.github.com/sue445/538c7339ccfb35decdb3463511acf21e

```ruby
# parse_gemfile.rb
ARGV.each do |source_file|
  File.read(source_file).each_line do |line|
    match_data = line.match(/^\s*gem\s*+["'](.+?)["']/)
    next unless match_data

    gem_name = match_data[1]
    next if EXCLUDE_GEMS.include?(gem_name)
    puts gem_name
  end
end
```

```bash
$ ruby parse_gemfile.rb *.gemfile | sort | uniq -c | sort -nr
```

---
### 集計結果（抜粋）
* 11アプリのGemfileを頑張って集計
* `rails new` するとデフォで入ってるgemやOSSじゃない社内gem除く
* メジャーどころが集まりすぎて面白みのない結果 :sweat_smile:

```text
  11 sentry-raven
  11 rspec-rails
  11 komachi_heartbeat
  11 factory_girl_rails
  11 annotate
  10 sidekiq
  10 pry-byebug
  10 jemalloc
  10 global
   9 sinatra
   9 sidekiq-cron
   9 redis-objects
   9 pry-rails
   9 newrelic_rpm
   9 kaminari
   8 onkcop
   8 jquery-rails
   8 capistrano
   8 bullet
   8 activerecord-import
   7 timecop
   7 redis-namespace
   7 pry-doc
   7 oj
   7 guard-rspec
   6 unicorn
   6 quiet_assets
   6 dalli
   6 capistrano-bundler
   6 better_errors
   5 slim-rails
   5 shibaraku
   5 rubocop
   5 rspec-parameterized
   5 roo
   5 pry-stack_explorer
   5 newrelic-redis
   5 aws-sdk
   5 active_hash
   4 weighted_sample
   4 walker_method
   4 simplecov-rcov
   4 rspec-sidekiq
   4 redis-rails
   4 rails-erd
   4 pry
   4 pre-commit
   4 oj_mimic_json
   4 faraday
   4 devise
   4 chatwork
   4 bootstrap-sass
   4 barrage
   4 activerecord-turntable
   4 activerecord-simple_index_name
   4 active_model_serializers
```

---
## ドリコムでよく使われてるけど他ではあまり使われてなさそうなgemを紹介
* komachi_heartbeat (11 apps)
* global (10 apps)
* onkcop (8 apps)
* rspec-parameterized (5 apps)
* shibaraku (5 apps)
* activerecord-simple_index_name (4 apps)
* activerecord-turntable (4 apps)

---
### komachi_heartbeat (11 apps)
https://github.com/mitaku/komachi_heartbeat

* Rails アプリケーションとDBサーバーなどの死活監視するためのURLを提供するEngine
* `/MOUNT_PATH/heartbeat` にアクセスしたらDB, Redis, memcachedの接続をチェックして問題なければokを返す
* 弊社ではkomachi_heartbeatのエンドポイントをZabbixで監視して異常時にアラートメールを飛ばしてる

---
### global (10 apps)
https://github.com/railsware/global

* yamlの設定ファイルをいい感じに扱うためのgem
* 普通にyaml書く場合と比べて痒いところに手が届いて、記述が少なくて済む
* 詳しくは [Railsアプリやgem作る時にいつも入れているgem一覧 - くりにっき](http://sue445.hatenablog.com/entry/2015/03/29/012855) 参照

---
### onkcop（オニクコップ） (8 apps)
https://github.com/onk/onkcop

* [rubocop](https://github.com/bbatsov/rubocop) のデフォルトの設定を [@onk](https://twitter.com/onk) がいい感じにカスタマイズしたgem
* ドリコムの標準コーディング規約
* onkが本家のリリースを全部読んで精査してからonkcopに取り込んでるので安心感がある

---
### rspec-parameterized (5 apps)
https://github.com/tomykaira/rspec-parameterized

* GroovyのSpockみたいなテーブル表記でパラメタライズドテストするgem

```ruby
describe "plus" do
  using RSpec::Parameterized::TableSyntax

  where(:a, :b, :answer) do
    1 | 2 | 3
    5 | 8 | 13
    0 | 0 | 0
  end

  with_them do
    it "should do additions" do
      expect(a + b).to eq answer
    end
  end
end
```

---
### shibaraku (5 apps)
https://github.com/onk/shibaraku

* ActiveRecord で start_at/end_at がある model を扱う gem
* よくある使い方だと、イベントの時限公開とか

```ruby
Campaign.in_time
# => SELECT start_at <= now < end_at records

Campaign.in_time(super_user)
# => SELECT test_start_at <= now < test_end_at records
```

---
### activerecord-simple_index_name (4 apps)
https://github.com/sue445/activerecord-simple_index_name

* Railsで生成されるインデックス名からtable名を抜いて短くするためのgem
* 下記の例だと `index_user_stocks_on_user_id_and_article_id` (43文字) -> `user_id_and_article_id` (22文字)

```ruby
create_table :user_stocks do |t|
  t.integer :user_id,    null: false
  t.integer :article_id, null: false
  t.timestamps null: false
end

add_index :user_stocks, [:user_id, :article_id]
```

---
### activerecord-turntable (4 apps)
https://github.com/drecom/activerecord-turntable

* RailsでDBを水平分散するためのgem
  * ユーザIDごとにアクセスするDBを変える
* DAU（Daily Active User）ウン十万を想定してるアプリではリリース時からだいたい入れてる
* [octopus](https://github.com/thiagopradi/octopus) と違って、アプリケーションコードにturntable特有の記述が無い
  * 普通にコードを書けばturntableが勝手に水平分散してくれるので、アプリ側でDBが水平分散されてることを全く意識することがない
* 詳しい資料 https://speakerdeck.com/gussan/sosiyarugemudedbshui-ping-fen-san-number-mdb-casual

---
## WE’RE HIRING !!
メンバー積極採用中です。
社員・契約社員・個人事業主・アルバイト etc..
http://www.drecom.co.jp/recruit/

### 募集職種
* エンジニア
  * Web/アプリ/フロントエンド
  * 基盤・インフラ
* デザイナー
* ディレクター/プランナー
* データサイエンティスト and more !!
