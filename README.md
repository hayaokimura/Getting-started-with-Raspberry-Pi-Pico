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






