---
layout: post
title: "壊れたファイルシステムの修復方法(FILESYSTEM CHECK FAILD)"
date: 2024-07-04 18:39 +0900
tags: [Linux, 修理, ext4, fs]
---
カーネルアップデート中にThinkPadのバッテリーが切れてしまい、それが原因でシステムが起動できなくなったので修復した。
再度カーネルをアップデートしたはいいものの、次はファイルシステムが壊れていた。
`FILESYSTEM CHECK FAILD`と出ていたので大方ファイルシステムであろうと思ったが的中していたようだ。

今回は手元にあったKaliLinux LiveUSBで修復したが、fsckさえ使えれば特別Kaliでなくても修復できる。
同様にシステムを起動できない方の助けになれば幸い。

# 修復作業
まず手元に起動可能なLiveUSBかLiveDVDを用意する。試していないがUbuntuでもFedoraでもfsckが用意されていると思う。入っていない場合リポジトリを検索して欲しい(aptやdnfで)。
次にLive起動してターミナルかシリアルコンソールを開いてrootにログインする。これには作業をより簡便に行う意図がある。
`lslk -f`を呼び出し起動できなくなったシステムのルートディレクトリが入っているパーティションを見つける。パーティション構成はそのコンピュータ毎に異なる為割愛する。
```bash
┌──(root㉿kali)-[/]
└─# lslk -f
```
先にファイルシステムがマウントされていないか確認する。マウントされている場合はアンマウント。
ルートパーティションを以下のようにfsckに渡して修復する。私のファイルシステムはext4なので以下のように指定。
```bash
┌──(root㉿kali)-[/]
└─# fsck.ext4 -fyv dev/sdX #Xは任意の数字、これも使用環境に依存する
```
`-f`や`-v`はe2fsckに用意されている。fsckはext4を検出するか指定すると自動的にe2fsckに切り替えてくる。

修復が完了した事を確認し再起動、電源が落ちたらUSBやDVDを外しておく。

# 番外編 カーネルアップデートを再開する
カーネルの修復方法も記載しておく(Arch系限定)
`chroot`を使いKaliから修復する。
```bash
## EFIパーティションをmanjaoのルートパーティションの/bootにマウント

mount dev/sda1 /path/to/my/manjaro_root_dir/boot/efi

cd path/to/my/manjaro_root_dir/

## ファイルシステムAPIをロード

mount -t proc /proc proc/
mount -t sysfs /sys sys/
mount --rbind /dev dev/

## ルートディレクトリからchrootを実行

chroot .

```
pacmanでアップデートする。なおchootとは言えKali上で動いている為ホスト名はKaliである。
```bash
## pacman実行

[ Kali ~]% pacman -Syyu

## mkinitcpio実行

[ Kali ~]% mkinitcpio -P Linux

## 一応update-gubも実行しておく

[ Kali ~]% update-gub

## exit

[ Kali ~]% exit
```


