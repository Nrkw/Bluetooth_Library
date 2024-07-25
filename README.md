# Bluetooth_Library
## 概要
Windows向けの、C++言語で実装されたBluetoothライブラリです。
基本的にC言語の知識のみで簡単に使うことができます。
# 必要なもの
* MinGW Version 13.1.0 [ダウンロードはこちら](https://github.com/niXman/mingw-builds-binaries/releases)  
  13.1.0のAssetの中にある、x86_64で始まるものを選んで解凍してください。 
## 使いかた
1. 好きな場所に作業用ディレクトリを作成
2. コマンドプロンプトを開く(Windowsキーを押してから"コマンドプロンプト"と検索するか、Windows+Rキーで"cmd"と入力)
```bat:コマンドプロンプト
> cd working_directory
```
このworking_directoryにMinGW13.1.0を解凍したフォルダとこのライブラリが入った"bluetooth"フォルダが入っている状況にしてください。

4. compile.batを作成
`copy nul ファイル名`と打つことで、中身が空のファイルを作成することができます。
```bat:コマンドプロンプト
> copy nul compile.bat
```
5. メモ帳でcompile.batを編集
`notepad ファイル名`でメモ帳で"ファイル名"を開き、編集することができます。
 ```bat:コマンドプロンプト
> notepad compile.bat
 ```
メモ帳で開いたcompile.batで、以下のように記述してください。
```bat:メモ帳
  .\MinGW13.1.0\bin\g++ main.cpp -o main -lbluetooth -lws2_32 -lole32
```
この`.\MinGW13.1.0`は適宜自分で解凍したMinGWのフォルダ名に合わせてください。
main.cppは自分の作りたいファイル名にしてください。

6. プログラム作成
今回はmain.cppにプログラムを書いていくことにします。
main.cではなくmain.cppなのは、C++言語でプログラムを書くよ、というだけの意味なので特に深い意味はありません。
いつもと同じC言語の感覚でプログラムを書いてもらって結構です。
このbluetoothライブラリを使用するには、`stdio.h`に加えて`bluetooth/SocketManager.hpp`と`bluetooth/Data.hpp`をincludeする必要があります。
```C:コード
#include <stdio.h>
#include "../bluetooth/Data.hpp"
#include "../bluetooth/SocketManager.hpp"

#define CONNECTED 1
#define NOT_CONNECTED 0

int is_connected = NOT_CONNECTED;
//何がbluetoothに関するイベントが起こった際に呼ばれる関数。
//注意：必ずData型の変数を引数で受け取るようにしてください。
void event(Data data)
{
  //端末に接続されたときや端末から情報が送られたときに呼ばれる関数
  //(manager.start()関数で指定する)
  if(data.type == "connect")
  {
    //端末と接続されたとき
    is_connected = CONNECTED;
    printf("接続したよ！/n");
  }
}

int main(void)
{
  //SocketManager型の変数managerを宣言。
  SocketManager manager;

  //bluetoothサーバーを起動。
  //何かイベントが起こった際は、start()関数で渡したevent関数が呼ばれる。
  manager.start(event);

  while(is_connected == NOT_CONNECTED)
  {
    //ここに色々な処理
  }
  //端末と接続されたら、ループを抜ける

  //SocketManagerの後片付け
  manager.end();
}
```
これで基本的にはプログラムの原型は完成です。

7. 実行
コマンドプロンプトでcompile.batを実行します。
```
> compile
```
すると、main.exeというファイルが生成されるはずです。
これを実行します。
```
> main
```
この状態で、Androidアプリの方から端末を接続します。(前もってPCとのペアリングを済ませておく必要があります。)
すると、スマホから端末を接続したときに`接続したよ！`と出力され、処理が終了するはずです。
