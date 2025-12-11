### Linuxカーネルの構成要素 の学習

#### カーネル
- カーネルはOSの中核を成すプログラム。
- Linux ≒ カーネル

#### カーネルの構成要素
- カーネルバージョン
    - unameコマンド または `/proc/version` で確認可能。
      ```
      $ uname -a
      Linux akase244-Latitude-3420 6.8.0-87-generic #88~22.04.1-Ubuntu SMP PREEMPT_DYNAMIC Tue Oct 14 14:03:14 UTC 2 x86_64 x86_64 x86_64 GNU/Linux
      
      $ uname --all
      Linux akase244-Latitude-3420 6.8.0-87-generic #88~22.04.1-Ubuntu SMP PREEMPT_DYNAMIC Tue Oct 14 14:03:14 UTC 2 x86_64 x86_64 x86_64 GNU/Linux

      $ cat /proc/version
      Linux version 6.8.0-87-generic (buildd@lcy02-amd64-060) (x86_64-linux-gnu-gcc-12 (Ubuntu 12.3.0-1ubuntu1~22.04.2) 12.3.0, GNU ld (GNU Binutils for Ubuntu) 2.38) #88~22.04.1-Ubuntu SMP PREEMPT_DYNAMIC Tue Oct 14 14:03:14 UTC 2
      ```
    - カーネルの名前を確認。
      ```
      $ uname -s
      Linux

      $ uname --kernel-name
      Linux
      ```
    - カーネルのバージョンを確認。
      ```
      $ uname -r
      6.8.0-87-generic

      $ uname --kernel-release
      6.8.0-87-generic
      ```
    - ハードウェアのアーキテクチャを確認。
      ```
      $ uname -m
      x86_64

      $ uname --machine
      x86_64
      ```
    - OSを確認。
      ```
      $ uname -o
      GNU/Linux

      $ uname --operating-system
      GNU/Linux
      ```
- カーネルイメージ
    - Linuxカーネルはイメージファイルとしてファイルシステム上に格納されている。
    - ファイル名は「vmlinuz-(カーネルバージョン)」の形式になっている。
      ```
      $ ls -1 /boot/vmlinuz*
      /boot/vmlinuz
      /boot/vmlinuz-5.15.0-89-generic
      /boot/vmlinuz-6.8.0-87-generic
      /boot/vmlinuz.old
      ```
- カーネルモジュール
    - カーネルはモジュールを必要に応じて読み込んで使用する。
    - lsmod
      - ロードされているモジュールを表示。
    - insmod
      - モジュールをロードする。
    - rmmod
        - モジュールをアンロードする。
    - modprobe
        - モジュールの依存関係を考慮してロード・アンロードする。
    - depmod
        - モジュールの依存関係を更新する。（`/lib/modules/カーネルバージョン/modules.dep` を作成）
    - modinfo
        - モジュールの情報を表示する。
