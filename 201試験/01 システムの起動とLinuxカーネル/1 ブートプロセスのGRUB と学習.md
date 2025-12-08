### ブートプロセスとGRUB の学習

#### ブートプロセス(起動シーケンス)
1. 電源投入
2. OS起動用ファームウェア(BIOS/UEFI)
3. ブートローダ(GRUB)
4. カーネル
5. 初期RAMディスク(initrd/initramfs)
6. systemd(init)
7. サービスの起動
8. ログイン

#### OS起動用ファームウェア
- BIOS(Basic Input/Output System)
    - BIOSではMBRでパーティションを管理する。
        - MBR(Master Boot Record)は起動デバイスの最初のセクタ。
    - マザーボード上のNVRAM(不揮発性メモリ)に格納されている。 
    - 設定されたデバイスの優先順位に従い、MBR内のブートローダを検索する。
    - 最初に検知したデバイスのブートローダを起動する。
- UEFI(Unified Extensible Firmware Interface)
    - UEFIではGPTでパーティションを管理する。
        - GPT(GUID Partition Table)はMBRに代わるハードディスクのパーティションの規格。
    - EFI(Entensible Firmware Interface)はBIOSに代わるファームウェア規格。
    - 名前がEFIからUEFI(Unified Extensible Firmware Interface)に変わったため、EFIとUEFIは同じ意味を指すものとして使用される。
    - NVRAMのブートエントリに設定された優先順位に従い、ESP内のブートローダ(shim.efi)を起動する。
        - ブートローダはESP(EFI System Partition)に格納されている。
        - ESPはFAT(またはVFAT)でフォーマットされ、/boot/efiディレクトリにマウントされる。
          ```
          $ lsblk
          NAME                  MAJ:MIN RM   SIZE RO TYPE  MOUNTPOINTS
          loop0                   7:0    0  49.4M  1 loop  /snap/aws-cli/345
          loop1                   7:1    0  49.4M  1 loop  /snap/aws-cli/349
          ・
          ・
          nvme0n1               259:0    0 476.9G  0 disk
          ├─nvme0n1p1           259:1    0   512M  0 part  /boot/efi
          ├─nvme0n1p2           259:2    0   732M  0 part  /boot
          ・
          ・
          │                                            /
          └─vgubuntu-swap_1 253:2    0   980M  0 lvm   [SWAP]
          ```
        - GRUB関連のファイルであるboot.img、core.img、grub.cfgと間違えやすいので注意する。
    - 主な機能
        - 大容量ディスクへの対応(GPT)
            - GPTパーティションでは起動ドライブに2TB以上を割り当て可能。
        - セキュリティの強化(Secure Boot)
        - ネットワークを介したリモート診断
    - efibootmgrコマンドでEFIのブートエントリを表示、編集をすることができる。
      ```
      $ efibootmgr -v
      BootCurrent: 0006
      Timeout: 0 seconds
      BootOrder: 0006,0003,0001,0002,0000,0004,0005,0007
      Boot0000* UEFI PM9A1 NVMe Samsung 512GB S6H3NF0RB00510 1    PciRoot(0x0)/Pci(0x1c,0x0)/Pci(0x0,0x0)/NVMe(0x1,00-00-00-00-00-00-00-00)/HD(1,GPT,14fd5246-ffd8-4c91-9d8b-94af1c845d3f,0x800,0x100000)/File(\EFI\Boot\BootX64.efi)N.....YM....R,Y.
      Boot0001* ONBOARD NIC (IPV4)    PciRoot(0x0)/Pci(0x1d,0x3)/Pci(0x0,0x0)/MAC(b4450644b41a,0)/IPv4(0.0.0.00.0.0.0,0,0)N.....YM....R,Y.
      ・
      ・
      ```

#### GRUB
- GRUB(GRand Unified Bootloader)
- GRUB2はGRUB Legacy(GRUB1)の後継となるブートローダ。
- boot.img、core.img、動的にロードされる複数のモジュールから構成される。
- grub-install(またはgrub2-install)はインストールを行うコマンド。
    - grub-install /dev/sda
    - grub2-install /dev/sda
- grub-mkconfig(またはgrub2-mkconfig)は/etc/default/grubに記載されている設定情報を元に/boot/grub/grub.cfg（または/boot/grub2/grub.cfg）を生成するコマンド。
    - grub2-mkconfig > /boot/grub2/grub.cfg
    - grub2-mkconfig -o /boot/grub2/grub.cfg
    - 設定内容を変更する場合は/etc/default/grubを編集し、grub-mkconfig(またはgrub2-mkconfig)を実行する。
    - grub.cfgを直接編集しない点に注意する。
- システム起動時に表示されるGRUBの画面で「e」キーを入力するとメニューの編集ができる。
- システム起動時に表示されるGRUBの画面で「c」キーを入力するとGRUBシェルを起動してコマンドラインモードとなり対話的な操作ができる。
- 破損したMBRを修復する方法
    - grub-installを実行する。
    - MBRのバックアップを元にddで上書きする。
- システム起動後に起動オプションを確認するには /proc/cmdline を参照する。
  ```
  $ cat /proc/cmdline 
  BOOT_IMAGE=/vmlinuz-6.8.0-40-generic root=/dev/mapper/vgubuntu-root ro quiet splash vt.handoff=7
  ``` 

#### カーネル
- カーネルの起動時のメッセージはカーネル内の循環バッファ(リングバッファ)と呼ばれる領域に格納される。
- カーネルの起動中に表示されるメッセージを起動後に確認するコマンド。
    - dmesg
    - journalctl -b

#### 初期RAMディスク
- RAMディスク
    - メモリ上にファイルシステムを作成する機能。
- ループバックマウント 
    - ファイルをファイルシステムとしてマウントすることができる機能。
- Linux起動用のRAMディスクのことを初期RAMディスク(initrd(Initial RAM Disk))と呼ぶ。 
- Linuxの起動時にRAMディスク、ループバックマウントの機能を使っている。
- 初期RAMディスクは2種類
    - initrd
        - ファイルシステムイメージをgzip圧縮したもの。
    - initramfs
        - cpioアーカイブをgzip圧縮したもの。
        - initramfs.imgはシステム起動時にメモリに読み込まれる小さなルートファイルシステム。
        - initramfsがルート(/)ディレクトリにマウントされた後、/initコマンドが実行される。
        - /initは/usr/lib/systemd/systemdへのシンボリックリンク。
- 初期RAMディスクを作成するコマンド
    - mkinitrd
        - 互換性のためmkinitrdの中でdracutが呼び出されて実行される。
    - mkinitramfs
        - mkinitrdと同等の機能を提供するコマンド。
    - dracut
        - mkinitrdを改善したコマンド。
- 初期RAMディスクの内容を確認するコマンド
    - lsinitrd
    - lsinitramfs
      ```
      $ lsinitramfs /boot/initrd.img-$(uname -r)
      .
      kernel
      kernel/x86
      ・
      ・
      var/lib
      var/lib/dhcp
      ```

#### 練習問題
- https://linuc.org/study/samples/1184/
- https://linuc.org/study/samples/3104/
- https://linuc.org/study/samples/3422/
