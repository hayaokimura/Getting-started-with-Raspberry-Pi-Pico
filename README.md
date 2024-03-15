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



