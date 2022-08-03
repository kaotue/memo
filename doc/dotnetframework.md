# コードが.NET Framework上で動くまで
## 概要

## 昔の実行環境 (～2000年以前)
昔はWin32APIを直接呼んでいた。<br>
※Win32API：WindowsOSが提供するAPI。例えば「ファイルを開く」といった１つの機能をアプリケーションに対して提供するもの。<br>
アプリケーション開発者はOSのバージョン毎でのAPIの違いに注意したり、<br>
一貫性のないAPI群を複雑な手順で呼び出したりしなければならず、開発が非常に面倒だった。<br>

## .NET Frameworkの利点
* Win32APIを呼びやすくする
* 機能ごとにまとめる
* OSの差異を吸収できる

## .NET Frameworkの挙動
### ソースコードのマネージモジュールへのコンパイル
![image](https://user-images.githubusercontent.com/56548030/182310217-fd6bc395-0095-4740-a3e3-9ef91fd608a9.png)

* PE32/PE32+ヘッダ(Portable Executable)<br>
  このプログラムが動作可能なマシンの種類などが記載。<br>
  PE32かPE32+かは、ビルド時のターゲットによって変わる。<br>
  * PE32 : AnyCPU<br>
  * PE32+ : x64<br>
![image](https://user-images.githubusercontent.com/56548030/182280961-7e5011d3-b040-4cd4-aa2c-1383110bbeff.png)
* CLRヘッダ<br>
必要となるCLRバージョン、マネージモジュールのエントリポイントメソッド（Mainメソッド）の情報などが記載。<br>
* メタデータ<br>
  コード内で定義されたクラスや型、インタフェースなどコードに関する情報が記載。<br>
  VisualStudioのIntelliSense機能はメタデータを解析して、型が提供するメソッド、プロパティ、イベント、およびフィールドを（メソッドの場合はさらに、メソッドが必要とするパラメーターも）通知する。
* ILコード<br>
  コンパイラが生成したコード。実行時にCLRはILをネイティブのCPU命令にコンパイルする。<br>
  
### マネージモジュールの結合によるアセンブリの生成
![image](https://user-images.githubusercontent.com/56548030/182318866-dd3cd608-fe91-4318-92a1-00648089716c.png)

## 別言語で作成したモジュールの呼び出しが可能
プログラミング言語どうしを強力に統合しつつ簡単に切り替えられる能力は、CLRの非常に素晴らしい機能です。
C#やVisualBasicのようなプログラミング言語はI/O処理に適した言語です。
APLは工学や金融の分野における高度な計算の処理に適した言語です。
CLRを使用すると、アプリケーションのI/O部分をC#で記述し、工学的な計算の部分をAPLで記述するといったことができます。

## 用語
* CLR (Common Language Runtime)<br>
マネージドコードの取得と機械語へのコンパイルと実行し<br>
それに加えて、メモリ管理、アセンブリのロード、例外処理、スレッドの同期などを処理する。<br>

* マネージドコードとアンマネージドコード<br>
  * マネージドコード：IL(中間言語)のみで構成され、特定の機種やOSなどに依存するコードを含まないプログラム。<br>
  * アンマネージコード：CLRが担当している、OSへの命令を直接記載したプログラム。<br>

* CLR対応のコンパイラ一覧<br>
  * Microsoft開発<br>
    * C#、VisualBasic、F#、ILアセンブラ<br>
    * C++/CLI<br>
      C++/CLIコンパイラは、開発者がマネージコードとアンマネージコードの両方を記述して、単一のモジュールとして出力できる唯一のコンパイラ。<br>
      開発者は既存のネイティブのC/C++コードをマネージコードから使用でき、既存のコードにマネージ型を組み入れていくことができる。<br>
    * IronPython<br>
      .NET Framework上で動作するPythonの実装。2006年9月5日に初版がリリースされた。<br>
      長らくPythonの２系（※サポート終了）にしか対応していなかったが、
      最近になって３系対応のバージョンがリリースされたが、これもPython自体はサポート終了している。（※2022年8月時点で3.4.0-beta）<br>
      https://ironpython.net/
    * IronRuby<br>
      .NET Framework上で動作する、マイクロソフトによるRubyの実装。<br>
      2011年3月13日バージョン1.1.3リリースを最後に更新なし。<br>
      http://ironruby.net/
  * 他社開発<br>
　　Ada、APL、Caml、COBOL、Eiffel、Forth、Fortran、Haskell、Lexico、LISP、LOGO、Lua、Mercury、ML、Mondrian、Oberon、Pascal、Perl、PHP、Prolog、RPG、Scheme、Smalltalk、Tcl/Tk<br>

## 参考文献
* CLR 【Common Language Runtime】 共通言語ランタイムとは<br>
https://e-words.jp/w/CLR.html
