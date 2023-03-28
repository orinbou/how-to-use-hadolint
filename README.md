# How to use hadolint

Dockerfile の Lintツール hadolint でいろいろ試してみる。

## 公式情報

### Github
https://github.com/hadolint/hadolint

### Dockerhub
https://hub.docker.com/r/hadolint/hadolint

### Web App
https://hadolint.github.io/hadolint/

### VSCode Marketplace
https://marketplace.visualstudio.com/items?itemName=exiasr.hadolint

---

## 準備

※以下Windows環境（PowerShell）の場合、、、

### コンテナイメージ取得
```
docker pull hadolint/hadolint
or
docker pull ghcr.io/hadolint/hadolint
```

### インストール

まずパッケージ管理ツール（Scoop）をインストールする。（※Chocolateyみたいなやつ）

https://github.com/ScoopInstaller/Scoop#requirements

の手順のとおり下記コマンドを実行する。
```
iwr -useb get.scoop.sh | iex
```
を実行すると、、、
```
Initializing...
Downloading ...
Creating shim...
Adding ~\scoop\shims to your path.
Scoop was installed successfully!
Type 'scoop help' for instructions.
```
となり完了する。

続いてLintツール本体（hadolint）をインストールする。
```
scoop install hadolint
```
を実行すると、、、
```
Installing 'hadolint' (2.12.0) [64bit] from main bucket
hadolint-Windows-x86_64.exe (7.0 MB) [==========================================================================================================================================================================================] 100%
Checking hash of hadolint-Windows-x86_64.exe ... ok.
Linking ~\scoop\apps\hadolint\current => ~\scoop\apps\hadolint\2.12.0
Creating shim for 'hadolint'.
'hadolint' (2.12.0) was installed successfully!
```
インストールが完了する。

バージョンを確認してみる。
```
hadolint --version
```
下記のとおりバージョンが確認できる。
```
Haskell Dockerfile Linter 2.12.0
```

### 手動でパスを設定する

VSCode Marketplace

https://marketplace.visualstudio.com/items?itemName=exiasr.hadolint

でプラグインをセットアップしてDockerfileを開くと

> hadolint: Cannot find hadolint from system $PATH. Please install hadolint.
> ソース:hadolint(機能拡張)

と言われてしまうので、機能拡張の「機能のコントリビューション」を参考に手動でパスを設定する。

機能拡張ID
```
hadolint.hadolintPath
```
を

> Path to hadolint executable. Windows user, make sure you escape the back slash in the path, e.g, C:\\path\to\\hadolint\\excutable

に従って
```
C:\\Users\\XXXXXXXX\\scoop\\apps\\hadolint\\current\\hadolint
or
C:\\Users\\XXXXXXXX\\scoop\\apps\\hadolint\\current\\hadolint.exe
```
のように設定する。

ちなみにユーザ名（XXXXXXXX）は、以下のように環境変数での指定はできないのでご注意ください！
```
%username%　←コマンドプロンプト
$env:USERNAME　←PowerShell
```

---

いろいろ試してみる。

## ハンズオン1

Dockerコンテナで実行してみる。

### Dockerfile1

https://hadolint.github.io/hadolint/

にデフォルト記載されている内容でLintチェックを実行してみる。

```
cat .\samples\Dockerfile1 | docker run --rm -i hadolint/hadolint
```
を実行すると、、、
```
-:1 DL3006 warning: Always tag the version of an image explicitly
-:2 DL3015 info: Avoid additional packages by specifying `--no-install-recommends`
-:2 DL3009 info: Delete the apt-get lists after installing something
-:2 SC2154 warning: node_verion is referenced but not assigned (did you mean 'node_version'?).
-:4 DL3045 warning: `COPY` to a relative destination without `WORKDIR` set.
-:5 DL3003 warning: Use WORKDIR to switch to a directory
-:5 DL3016 warning: Pin versions in npm. Instead of `npm install <package>` use `npm install <package>@<version>`
-:8 DL3011 error: Valid UNIX ports range from 0 to 65535
```
となる。

### Dockerfile2

指摘内容を適当に修正した内容でLintチェックを実行してみる。
```
cat .\samples\Dockerfile2 | docker run --rm -i hadolint/hadolint
```
を実行すると、指摘なしであることが確認できる。


## ハンズオン2

ローカルでコマンド実行してみる。

### Dockerfile1

https://hadolint.github.io/hadolint/

にデフォルト記載されている内容でLintチェックを実行してみる。

```
hadolint .\samples\Dockerfile1
```
を実行すると、、、
```
.\samples\Dockerfile1:1 DL3006 warning: Always tag the version of an image explicitly
.\samples\Dockerfile1:2 DL3015 info: Avoid additional packages by specifying `--no-install-recommends`
.\samples\Dockerfile1:2 DL3009 info: Delete the apt-get lists after installing something
.\samples\Dockerfile1:2 SC2154 warning: node_verion is referenced but not assigned (did you mean 'node_version'?).
.\samples\Dockerfile1:4 DL3045 warning: `COPY` to a relative destination without `WORKDIR` set.
.\samples\Dockerfile1:5 DL3003 warning: Use WORKDIR to switch to a directory
.\samples\Dockerfile1:5 DL3016 warning: Pin versions in npm. Instead of `npm install <package>` use `npm install <package>@<version>`
.\samples\Dockerfile1:8 DL3011 error: Valid UNIX ports range from 0 to 65535
```
となる。

### Dockerfile2

指摘内容を適当に修正した内容でLintチェックを実行してみる。
```
hadolint .\samples\Dockerfile2
```
を実行すると、指摘なしであることが確認できる。

---

## 参考
* https://qiita.com/yutaroud/items/80ae6a65aa2159104dfe
* https://dev.classmethod.jp/articles/hadolint-docker/
* https://zenn.dev/yumemi_inc/articles/5b537830ea3cad
* https://zenn.dev/jnxjez/articles/8b5d05302a8957
