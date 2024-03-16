# Getting-started-with-Raspberry-Pi-Pico

[Tutorial PDF](pdf/getting-started-with-pico.pdf)
https://datasheets.raspberrypi.com/pico/getting-started-with-pico.pdf

## このリポジトリは何
hachi が Raspberry Pi Pico の C プログラミングに入門するためにチュートリアルをやってみるリポジトリ

## 開発環境セットアップ
できるだけ手元の環境変えたくないので docker で開発できるならしたい。
ちょうどよく VSCode Dev Container を構築している人がいたので試してみる
https://github.com/mwinters-stuff/vscode-devcontainer-raspberrypi-pico

とりあえず clone して、.devcontainer と .vscode をコピーしてきた。

```sh
❯ cp -r ../../mwinters-stuff/vscode-devcontainer-raspberrypi-pico/.devcontainer/ .devcontainer
❯ cp -r ../../mwinters-stuff/vscode-devcontainer-raspberrypi-pico/.vscode .vscode
```

VSCode に Dev Containers 拡張を入れる
https://blog.kinto-technologies.com/posts/2022-12-10-VSCodeDevContainer/

Reopen in Container を実行してみる

失敗した。
https://github.com/microsoft/vscode-remote-release/issues/8714
https://github.com/microsoft/vscode-dev-containers/tree/main/containers/cpp

image がないかも？なので編集して再試行

原因は
- device がなかった
- platform を指定してしまっていた

とりあえずできたっぽい。

## Hello World を実行できるようにする
とりあえず、 Blink が Hello World みたいなものなので、 Blink を実装してみる。

以下をコピペして、 `blink.c`として保存した。
https://github.com/raspberrypi/pico-examples/blob/master/pico_w/wifi/blink/picow_blink.c#L10-L22

チュートリアル通り以下を実行してみる。
```sh
$ mkdir build
$ cd build
$ cmake ..
```

だめっぽい。

```sh
$ cmake ..
CMake Error: The source directory "/workspaces/Getting-started-with-Raspberry-Pi-Pico" does not appear to contain CMakeLists.txt.
Specify --help for usage, or press the help button on the CMake GUI.
```

CMakeLists.txt が必要らしい。

CMakeLists.txt と pico_sdk_import.cmake をdevcontainer を作っていた人から拝借してきた。実行してみる。

何かで死んだ。

arm64 のライブラリのなにかがなくて死んでそう。
というかこのイメージ、x86_64 じゃなかったのか
x86_64 で rebuild する必要がある？
FROM のところに platform 指定を追加した

いけてそう！！！！

build dir で cmake .. を実行したあと、 make を実行した。 pico/cyw43_arch.h がないと言われた。 
お、ドキュメントによると、 cmake するときに option をつけないといけなさそう？

` -DPICO_BOARD=pico_w` というのをつけると記載があるのでやってみる

だめだった。
target_link_libraries というところに `pico_cyw43_arch_none` というのがあるのを見たのでやってみる
https://github.com/raspberrypi/pico-examples/blob/eca13acf57916a0bd5961028314006983894fc84/pico_w/wifi/blink/CMakeLists.txt#L6-L7

動いた！！
https://x.com/hachiblog/status/1768840858324148635?s=20

```sh
$ cd build
$ cmake .. -DPICO_BOARD=pico_w 
$ make -j4

```

## image のサイズを低減させる
image size が 10G もあるのでもう少しコンパクトにしたい

`arm-none-eabi`をダウンロードして展開するところでレイヤーが複数に分かれていたので一つにまとめる

6.44GB になった。コンパクト化
これ以上は厳しそうなので一旦ここまで

## "Hello World" をやってみる
[Getting started with Raspberry Pi Pico](https://datasheets.raspberrypi.com/pico/getting-started-with-pico.pdf) では、UART でやっていたが、ラズパイもないのでUSBでやりたい
以下のブログが参考になりそう

[macOSからPi Picoを使う](https://decafish.blog.ss-blog.jp/2021-05-12)

ブログを見ると、以下のサンプルを参考にしてそう
https://github.com/raspberrypi/pico-examples/tree/master/hello_world/usb

CMakeLists.txt に以下の行が必要。uart を無効にして、usb を有効にする
hello_usb は add_executable command で定義された Binary Target になっている。

cf. https://cmake.org/cmake/help/latest/command/add_executable.html
cf. https://qiita.com/sakaeda11/items/fc95f62b68a14ab861dc#%E3%82%BF%E3%83%BC%E3%82%B2%E3%83%83%E3%83%88target
cf. https://www.raspberrypi.com/documentation/pico-sdk/runtime.html
cf. https://cmake.org/cmake/help/latest/manual/cmake-buildsystem.7.html#binary-targets

```cmake
# enable usb output, disable uart output
pico_enable_stdio_usb(hello_usb 1)
pico_enable_stdio_uart(hello_usb 0)
```

ブログによるとこれをすると TinyUSB が自動でリンクされるらしい？
TinyUSB というのはオープンソースの組み込みシステム用USBホスト/デバイス・スタック らしい。後で読む。
cf. https://interface.cqpub.co.jp/wp-content/uploads/a179f1519df152bce1f5f35f5f41cf12.pdf

> 実はPi Picoでpico_stdioをUSBに設定すると、Pi PicoのUSBはdeviceモードになり、CDCクラスとしてhostから見えるようにdescriptorが設定される。

CDC は Communication Devices Class の略。 USB接続できるデバイスが共通のプロトコル、機能を持つことを定義したグループのことらしい。
https://usb.org/documents?search=CDC&items_per_page=50

気を取り直して USB でシリアル通信をしてみる

書き込みを上書きしようとしたら何故かうまくいかなかったのでリセットした。以下のところから UF2 ファイルをダウンロードして書き込むといける。
https://www.raspberrypi.com/documentation/microcontrollers/raspberry-pi-pico.html#resetting-flash-memory

ビルドして、インストールした。
シリアル通信を受け取るために screen を実行

```sh
$ screen /dev/tty.usbmodem101 9600
```

できた！！
USB を抜いてつなぎ直したら何故か表示されなくなったので試行錯誤したが、どうやら USB の A to C の変換アダプタを繋いだままUSBを抜き差ししていたのが良くなかったらしい。

その過程で学んだ screen command の操作

```sh
# screen のプロセスから抜けるには Ctrl+A, D を打つ
# screen list
$ screen -list
# terminate する
$ screen -X -S <pid> quit
```
## 2つのバイナリをどっちもそれぞれのディレクトリでビルドできるようにする
sample がそういう構成になってるので多分できるはず。CMake ってどうやって実行するんだろう。

add_subdirectory command をつかうっぽい。

## ntp client のサンプルを動かしてみる

動いた。サンプルを動かすのはそろそろ良さそう。

ntp client の実装を読んでみたいが、動くことが確認できたので一旦ここまでにしようと思う。
