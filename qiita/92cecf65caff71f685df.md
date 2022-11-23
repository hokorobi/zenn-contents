---
title: 日本語ファイル名を含む Mercurial リポジトリの Git リポジトリ移行
tags: Mercurial Git
author: hokorobi
slide: false
---
今動かしてみたらエラーになる。
あるえ？

---

Bitbucket が Mercurial のサポートをやめるので、もろもろの Mercurial リポジトリを Git リポジトリへ変換しています。
ただ、convert だと日本語ファイル名を含む Mercurial リポジトリを Git リポジトリに移行するとファイル名が文字化けするので、そうならないように以下の手順で移行しました。 Thanks [@shuichigoto](https://twitter.com/shunichigoto)
MSYS をインストール済みで、MSYS のコンソールを起動するものとします。
MSYS コンソール起動後、以下のコマンドを実行。

```
pacman -S python2 python2-pip
pacman -S msys/libcrypt-devel
pip2 install mercurial
mkdir hg2git
cd hg2git
git clone https://github.com/frej/fast-export
mkdir togit
cd togit
git init
../fast-export/hg-fast-export.sh -r /path/to/Mercurial/repo --force --fe cp932
```


