### ブートプロセスとGRUB の学習

#### ブートプロセス（起動シーケンス）
1. 電源投入
2. OS起動用ファームウェア(BIOS/UEFI)
3. ブートローダ(GRUB)
4. カーネル
5. initramfs
6. /sbin/init
7. サービスの起動
8. ログイン

#### OS起動用ファームウェア
- BIOS(Basic Input/Output System)
    - マザーボード上のNVRAM(不揮発性メモリ)に格納されている。 
    - 設定されたデバイスの優先順位に従い、MBR(マスタブートレコード)内のブートローダを検索する。
    - 最初に検知したデバイスのブートローダを起動する。
- UEFI(Unified Extensible Firmware Interface)
    - NVRAMのブートエントリに設定された優先順位に従い、GPTのEFIパーティション内のブートローダを起動する。

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
- システム起動時に表示されるGRUBメニュー画面で「e」キーを入力するとGRUBメニューの編集をすることができる。
- 破損したMBRを修復する方法
    - grub-installを実行する。
    - MBRのバックアップを元にddで上書きする。
- 関連リンク
    - https://linuc.org/study/samples/1184/
    - https://linuc.org/study/samples/3104/
    - https://linuc.org/study/samples/3422/
    - https://gihyo.jp/admin/serial/01/ubuntu-recipe/0743

#### UEFI
- UEFIはEFIパーティション内のFAT32ファイルシステム上のブートローダ(shim.efi)を呼び出す。
- EFI(Entensible Firmware Interface)はBIOSに代わるファームウェア規格。 
- 名前がEFIからUEFI(Unified Extensible Firmware Interface)に変わったため、EFIとUEFIは同じ意味を指すものとして使用される。
- 大容量ディスクへの対応(GPT)
    - GPT(GUID Partition Table)はMBR(Master Boot Record)に代わるハードディスクのパーティションの規格。
- セキュリティの強化(Secure Boot)
- ネットワークを介したリモート診断
- efibootmgrコマンドでEFIのブートエントリを表示、編集をすることができる。

#### カーネル
- カーネルの起動時のメッセージはカーネル内の循環バッファ(リングバッファ)と呼ばれる領域に格納される。
- カーネルの起動中に表示されるメッセージを起動後に確認するコマンド。
    - dmesg
    - journalctl -b

