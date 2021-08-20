# REST、REST API、RESTfulについて

## RESTとは

**RE**presentational **S**tate **T**ransferの略

 広く普及したWebのインフラをそのまま利用して、簡易な手順でWebサービスへのアクセスを可能にする仕組み。もともとはHTTPプロトコルの設計者の一人でもあるRoy Fielding氏によって2000年に提唱されたものである。

## RESTは軽量なWebサービス

RESTによるWebサービスの実装は軽量で、以下のような特徴がある。

* 特定のプラットフォーム（OS）に依存しない
* 特定の言語に依存しない（Java、C#、Perlなど、さまざまな言語で利用できる）
* HTTPなどのインターネット標準プロトコルを利用
* ファイアウォールがある環境でも容易に利用できる
　
 ただしREST自体には、セキュリティや暗号化、セッション管理、サービス品質の管理（QoS：Quality of Service）などの機能は組み込まれていない。例えば暗号化が必要なら、HTTPSを組み合わせるといった対策が別途必要になる。

 RESTによるデータ取得API呼び出しを図にすると次のようになる。クライアント側アプリケーションのAPIのリクエストは、HTTP GETによりサーバ側に送られる。前述した通り、呼び出すAPIはURLとして指定する。

![alt text](https://image.itmedia.co.jp/ait/articles/1601/13/wi-restfig02.png "クライアント側")

## RESTの4つの設計原則

1. セッションなどの状態管理を行わない。(やり取りされる情報はそれ自体で完結して解釈することができる)
2. 情報を操作する命令の体系が予め定義・共有されている。（HTTPのGETやPOSTメソッドなど）
3. すべての情報は汎用的な構文で一意に識別される。（URLやURIなど）
4. 情報の内部に、別の情報や(その情報の別の)状態へのリンクを含めることができる。

<div class="video-responsive">
 <iframe width="560" height="315" src="https://www.youtube.com/embed/7YcW25PHnAA" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</div>

## REST APIとは

RESTful API(REST API)とは、Webシステムを外部から利用するためのプログラムの呼び出し規約(API)の種類の一つで、RESTと呼ばれる設計原則に従って策定されたもの。RESTそのものは適用範囲の広い抽象的なモデルだが、一般的にはRESTの考え方をWeb APIに適用したものをRESTful APIと呼んでいる。
RESTful APIでは、URL/URIですべてのリソースを一意に識別し、セッション管理や状態管理などを行わない(ステートレス)。同じURLに対する呼び出しには常に同じ結果が返されることが期待される。
また、リソースの操作はHTTPメソッドによって指定(取得ならGETメソッド、書き込みならPOSTメソッド)され、結果はXMLやHTML、JSONなどで返される。また、処理結果はHTTPステータスコードで通知するという原則が含まれることもある。

## RESTfulとは

* 完全にREST原則に従うもは REST API と呼び、そうではない場合は RESTful API と呼ぶ
* REST原則に従わないのであれば REST という言葉は使わす、HTTP API などと呼ぶべきだ
* RESTful の -ful は満たすということなので、RESTful API は完全にREST原則を満たすものだ



