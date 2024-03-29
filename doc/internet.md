# ブラウザでWEBページが表示されるまで

## 概要
1. DNS名前解決
2. Webリクエスト
3. 画面表示

## フロー
1. アドレスバーにURLを入力しEnter押下
1. URLを解読
1. キャッシュにIPアドレスがあるか確認
1. DNSからIPアドレスを取得
1. ポート番号を割り当てる
1. IPアドレスでサーバーにリクエストを実行
1. HTTPリクエストを送信する
1. HTTPレスポンスを受信する
1. レンダリング

```mermaid
flowchart TD
　subgraph ブラウザ
  A1["アドレスバーにURLを入力しEnter押下"]-->A2
  A2["URLを分割する\nhttps://twitter.com/kaotue1\n↓\nスキーム部：https\nドメイン部：twitter.com\nパス部：kaotue1\nパラメータ部：省略されている\nポート番号：省略されている"]-->R0
  R0["DNS名前解決の開始"]-->
  B1{"ブラウザの\nDNSキャッシュに\nドメイン部twitter.comの\nIPアドレスがあるか"} --> |Yes| B2
  B1 --> |No| R1
  R1{"OSのDNSキャッシュに\nIPアドレスがあるか"} --> |Yes| B2
  B2["IPアドレスの取得完了"] -->
  B2-2["通信先ポート番号を割り当てる"] -->
  B3{"ポート部にポート番号が\n指定されているか"} --> |Yes| B4
  B3 --> |No| B99["ブラウザがservicesファイルから\nポート番号を割り出し\nHTTP->80\nHTTPS->443"] --> B4
  B4["【URLの解析結果】\n\nhttps://twitter.com/kaotue1\n↓\nhttps://133.106.196.78:443/kaotue1"]-->C1
  end
  subgraph "DNSサーバー"
  R1 --> |No| R2
  R2[("【最寄りのDNSサーバ】\n\nルートドメインサーバの\nIPアドレスを取得")]-->
  R3[("【ルートドメインサーバ】\n\nトップレベルドメイン(com)サーバの\nIPアドレスを取得")]-->
  R4[("【トップレベルドメインサーバ(com)】\n\ntwitter.comのIPアドレスを取得")]-->B2
  end
  subgraph "HTTPSサーバ"
  C1["HTTPSリクエスト送信"]-->
  C2["証明書の検証"]-->
  C3["twitter.comへリクエスト"]-->
  C4[("【twitter.comサーバ】\nレスポンスを返却")]-->D0
  end
  subgraph "ブラウザ"
  C1---D2
  D0["ブラウザへレスポンス返却"]-->
  D2["Loading\nHTMLの読み込み、他CSSなど別途リクエスト実行"]-->D3
  D3["Scripting\nJavascriptを実行"]-->
  D4["Rendering"]-->
  D5["Painting"]
  end
```

## URL (Uniform Resource Locator)
インターネット上のリソースを一意に特定するための名前

## DNS (Domain Name System)
IPアドレスとドメインを関連して管理する仕組み

### ルートサーバ
トップレベルドメイン（TLD）の参照情報を保持、<br>
具体的にそれぞれのTLDを受け持つDNSサーバの名前とIPアドレスの対応といった情報が記載されている。<br>
DNSクライアントはその情報を元にして、次に問い合わせるべきDNSサーバを把握する。<br>

ルートサーバの場所については、DNSサーバ内に静的ファイルとして置かれており<br>
a.root-servers.net～m.root-servers.net<br>
先頭の各アルファベットがa～mまでの13レコードが定義されている<br>
mのサーバが日本に存在する<br>

![DNS](https://upload.wikimedia.org/wikipedia/commons/d/d2/DNS_schema.svg)

## 国際化ドメイン名
ascii以外の、漢字、アラビア文字、キリル文字、ギリシア文字などを使用したドメイン名<br>
※例 日本語.jp

## Punycode
国際化ドメインのutf-8で表現されている文字列をasciiに変換するためのルール
<br>
https://punycode.jp/<br>
正規化後	:	日本語.jp<br>
Punycode	:	xn--wgv71a119e.jp<br>

## 国際化ドメイン名のDNSについて
Punycodeでasciiへ変換後にいつも通りのDNSの名前解決を実施する<br>
ブラウザ側でのエンコード対応だけなので、国際化ドメイン名が対応時にDNSの仕様変更は一切行われなかった。<br>

## Socketライブラリ
OSに組み込まれているネットワーク機能をアプリケーション(ブラウザなど)から呼び出すためのプログラム群

## DNSリゾルバ
Socketライブラリの中の一プログラム
DNSの名前解決を実行するプログラム

## servicesファイル
スキーマ→第４層のプロトコル、ポート番号の対応表が載っているファイル<br>
ポート番号を指定していない場合、ブラウザからはポート番号のみ取得
### Windows
C:\Windows\System32\drivers\etc\services
```console
# Copyright (c) 1993-2004 Microsoft Corp.
#
# This file contains port numbers for well-known services defined by IANA
#
# Format:
#
# <service name>  <port number>/<protocol>  [aliases...]   [#<comment>]
#
http               80/tcp    www www-http           #World Wide Web
https             443/tcp    MCom                   #HTTP over TLS/SSL
https             443/udp    MCom                   #HTTP over TLS/SSL
```
### Mac
/etc/services
```console
/etc % cat services 
#
# Network services, Internet style
#
# Note that it is presently the policy of IANA to assign a single well-known
# port number for both TCP and UDP; hence, most entries here have two entries
# even if the protocol doesn't support UDP operations.
#
# The latest IANA port assignments can be gotten from
#
#	http://www.iana.org/assignments/port-numbers
#
# The Well Known Ports are those from 0 through 1023.
# The Registered Ports are those from 1024 through 49151
# The Dynamic and/or Private Ports are those from 49152 through 65535
#
# $FreeBSD: src/etc/services,v 1.89 2002/12/17 23:59:10 eric Exp $
#	From: @(#)services	5.8 (Berkeley) 5/9/91
#
# WELL KNOWN PORT NUMBERS
#
#                          David Zimmerman <dpz@RUTGERS.EDU>
http             80/udp     www www-http # World Wide Web HTTP
http             80/tcp     www www-http # World Wide Web HTTP
#                          Bill Davidson <billd@equalizer.cray.com>
https           443/udp     # http protocol over TLS/SSL
https           443/tcp     # http protocol over TLS/SSL
```

## HTTP
* HTTP1.1 (TCP)<br>
基本的に1リクエスト1レスポンスで構成されており、複数のリクエストを同時に行うには<br>
複数のセッションを張るしかない。<br>

* HTTP2 (TCP)<br>
1つのTCPセッションを張り内部で複数のリクエストを同時に行える構成としている<br>
多数のファイルをまとめてリクエストしたり、リクエスト中に別のファイルをリクエストすることが可能<br>

* HTTP3 (UDP)<br>
HTTP2ではTCPを利用するため、途中のパケットが一つロストするとそのパケットの再送を受信するまで<br>
処理を進めることができない。<br>
特に動画再生等のストリーミングであれば途中のパケットロスは無視して最新のデータのみ受信できることが望ましい。<br>
HTTP3ではUDPを利用するため、3way Handshake等が不要で早い。<br>
  
日本ではパケットロスが少ないためHTTP2が適している<br>
海外ではパケットログが頻繁に起こるため、その場合HTTP2を使用していた場合HTTP1.1のほうが効率がいい<br>

## 参考文献

* DNSルートサーバ<br>
https://www.nic.ad.jp/ja/newsletter/No45/0800.html
* ネットワークはなぜつながるのか 第2版 知っておきたいTCP/IP、LAN、光ファイバの基礎知識<br>
https://www.amazon.co.jp/dp/4822283119
