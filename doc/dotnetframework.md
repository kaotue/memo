# コードが.NET Framework上で動くまで

## 昔の実行環境 (～2000年以前)
* メモリ管理、ポインタなど開発者が意識してコーディングする必要がある。
* CPU毎（x86/x64）の命令に合わせたEXEファイルが必要。

## .NET Framework(C#)が生まれた経緯（C#はJ++の生まれ変わり？）<br>
1996年頃MicrosoftがJavaの拡張としてJ++という言語を開発していた。<br>
MicrosoftとしてはJ++をさらに拡張していく予定であったが、Javaのライセンス元のSunMicrosystemsが商標権侵害でMicrosoftを訴訟。<br>
（※SunのJava実装に含まれない独自の拡張機能を実装していた）<br>
Microsoftが敗訴したため、これ以降J++の開発は中止となった。<br>
Javaを元にMicrosoftがやりたいことができなくなったため、独自で開発言語環境を作りはじめる。→.NET Framework(C#)。

## .NET Frameworkとは<br>
2000年にbeta版が発表。2003年から一般的に使われ始めた。<br>
Microsoftが開発したアプリケーション開発・実行環境。.NET Frameworkにおける最も重要な概念は共通言語基盤 (CLI) に含まれている。<br>
CLIの目的は言語に依存しない開発環境および実行環境を提供することである。マイクロソフトによるCLIの実装は共通言語ランタイム (CLR) と呼ばれる。<br>

* CLR (Common Language Runtime)<br>
マネージドコードの取得と機械語へのコンパイルと実行し<br>
それに加えて、メモリ管理、アセンブリのロード、例外処理、スレッドの同期などを処理する。<br>

* マネージドコードとアンマネージドコード<br>
  * マネージドコード：IL(中間言語)のみで構成され、特定の機種やOSなどに依存するコードを含まないプログラム。<br>
  * アンマネージコード：CLRが担当している、OSへの命令を直接記載したプログラム。<br>

## .NET Frameworkの利点
* Win32APIを読んでいることを意識する必要がない
* メモリ管理を意識する必要がない
* CPUの差異を吸収できる
* IL（中間言語）さえ作成できればどのような言語でもコーディング可能

## .NET Frameworkの挙動
### 全体像
ソースコードから中間言語へコンパイルされ、実行時にその都度機械語へコンパイルされて実行される<br>
![image](https://user-images.githubusercontent.com/56548030/182747985-af5877db-69e0-442c-91c6-b8cc7511bedb.png)

### ソースコードのマネージモジュールへのコンパイル
![image](https://user-images.githubusercontent.com/56548030/182310217-fd6bc395-0095-4740-a3e3-9ef91fd608a9.png)

* PE32/PE32+ヘッダ(Portable Executable)<br>
  このプログラムが動作可能なマシンの種類などが記載。<br>
  PE32かPE32+かは、ビルド時のターゲットによって変わる。<br>
  * PE32 : x86/AnyCPU<br>
  * PE32+ : x64<br>
![image](https://user-images.githubusercontent.com/56548030/182280961-7e5011d3-b040-4cd4-aa2c-1383110bbeff.png)
* CLRヘッダ<br>
必要となるCLRバージョン、マネージモジュールのエントリポイントメソッド（Mainメソッド）の情報などが記載。<br>
* メタデータ<br>
  コード内で定義されたクラスや型、インタフェースなどコードに関する情報が記載。<br>
  VisualStudioのIntelliSense機能はメタデータを解析して、型が提供するメソッド、プロパティ、イベント、およびフィールドを（メソッドの場合はさらに、メソッドが必要とするパラメーターも）通知する。
* ILコード<br>
  コンパイラが生成したコード。実行時にJITコンパイラはILをネイティブのCPU命令にコンパイルする。<br>
  
### マネージモジュールの結合によるアセンブリの生成
![image](https://user-images.githubusercontent.com/56548030/182318866-dd3cd608-fe91-4318-92a1-00648089716c.png)

## 別言語で作成したモジュールの呼び出しが可能
プログラミング言語どうしを強力に統合しつつ簡単に切り替えられる能力は、CLRの非常に素晴らしい機能。
C#やVisualBasicのようなプログラミング言語はI/O処理に適した言語。
APLは工学や金融の分野における高度な計算の処理に適した言語。
CLRを使用すると、アプリケーションのI/O部分をC#で記述し、工学的な計算の部分をAPLで記述するといったことが可能。

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

# 言語処理
## 字句解析<br>
一定のルールに従ってキーワードを分ける作業。後の構文解析をしやすくするための前準備。<br>
１つの配列に全て入っている。<br>
## 構文解析<br>
前後を見て意味を解析する。<br>
ツリー構造を作成している。<br>



* CLR (Common Language Runtime)<br>
