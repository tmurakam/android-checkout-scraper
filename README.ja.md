Android Checkout Scraper
========================

本プロジェクトは終了しました。
新プロジェクトは https://github.com/tmurakam/googleplay-scraper です。

はじめに
========

このツールは、Google Play および Google Checkout で提供
される販売者向けの売上レポート CSV ファイルを自動でダウン
ロードするためのツールです。

ダウンロードできるのは以下の２通りです。

* Google Play のデベロッパーコンソールで提供される販売レポート
* Google Checkout で提供される注文リスト
* 売上管理の支払概要

売上の集計をするなり、経理システムにぶち込むなり、お好きに
どうぞ。

この他、自動で発送ボタンを押す機能もあります。


必要システム
============

以下のものが必要です。

* Ruby 1.8.7以上 or 1.9.2以上
* RubyGems
* Bundler
* Mechanize

Bundler がインストールされていない場合は、以下のようにして
インストールします。

    $ gem install bundler

Mechanize は Bundler でインストールします。

    $ bundle install


設定とか
========

config.rb.sample を config.rb にコピーし、編集してください。

secrets.rb.sample を secrets.rb にコピーし、Android Market に
ログインするときのメールアドレスとパスワード、デベロッパIDを
設定してください。
(素のパスワードを設定するのでアクセス権には注意)

デベロッパID は、developer console にログインした後の URL 末尾の
dev_acc=... の数字です。


使い方
======

売上レポート取得
----------------

売上レポートの取得には get-sales-report.rb を使います。
例えば 2011年10月の売上を取得する場合は以下のようにします。
結果は標準出力に出力されます。

    $ ./get-sales-report.rb 2011 10

また推定売上レポートもダウンロードできます。

    $ ./get-estimated-sales-report.rb 2011 10


オーダー一覧取得
----------------

オーダー一覧取得は get-orders.rb を使います。こちらは開始日時と
終了日時を指定します。時刻は日本時間で指定。

    $ ./get-orders.rb 2011-08-01T:00:00:00 2011-09-30T23:59:59

--details オプションを付与すると詳細な CSV データを出力します。

なお、get-orders.rb には第３引数として以下のいずれかの条件を指定
できます。省略時は CHARGED が指定されたものとして扱います。

* ALL : すべて
* CANCELLED : ユーザーによりキャンセルされました
* CANCELLED_BY_GOOGLE : Google によりキャンセルされました
* CHARGEABLE: 請求可能
* CHARGED : 請求済み
* CHARGING : 請求中
* PAYMENT_DECLINED : 支払いの不承認
* REVIEWING : 確認中

なお、オーダー一覧は最大で 500件までしか取得できません(サイトには
そう書いてある)。したがって、これより多くのデータを取得したい場合は
開始日時と終了日時を狭めて取得してください。


支払い概要取得
--------------

Google checkout の支払概要取得は get-payouts.rb で行います。
こちらは開始日と終了日を指定します。

    $ ./get-payouts.rb 2011-11-01 2011-12-01

第３引数には以下の引数を指定できます。省略時は PAYOUT_REPORT です。

* PAYOUT_REPORT : 支払いの詳細
* TRANSACTION_DETAIL_REPORT : トランザクション


アプリケーション統計情報取得
----------------------------

Developer Console の統計情報 CSV エクスポートと同じものを得ます。
対象となるアプリのパッケージ名と、開始日/終了日を指定してください。

    $ ./get-app-stats.rb your.package.name 20120101 20120630 > stat.zip

ZIP ファイルが標準出力に出力されるので、リダイレクトでファイルに
落としてください。


発送ボタン自動処理
------------------

注文の受信トレイにある全ての「発送」ボタンを自動で押す機能です。
使い方は以下のとおり。

    $ ./auto-deliver.rb

「アーカイブ」も全部押したい場合は -a オプションをつけてください。

    $ ./auto-deliver.rb -a

なお、発送ボタンが押されるのは注文の受信トレイの１ページ目に
あるオーダーだけです。2ページ目以降のものは押されません。

# そもそもなんで発送ボタンなるものがあるのか不明ですが、、、


内部動作とか
============

Mechanize を使って Web サイトに自動アクセスし、フォームを叩いて
CSV を入手するだけです。

本体は android_checkout_scraper.rb です。ソース見れば何やってる
かはわかると思います。Rails アプリの中で使うとか、お好きにどうぞ。


ライセンス
==========

Public domain 扱いとします。


免責事項
========

* 無保証です。
* Google 側のサイトの作りが変わったら当然動作しなくなります。
* Google から怒られても責任は取りません。
* 動かなくても文句言わない。自分で直すように。
* 直したら修正を送るなり pull request するなりしてくれると嬉しい。


ひとりごと
==========

* Google さん、Android 向けの Google Checkout API 解放してくれるとすごく嬉しいのですが、、、

---
'12/12/13
Takuya Murakami, E-mail: tmurakam at tmurakam.org
