# ラスベリーパイ初期設定

殴り書き

## 機材

- Raspberry pi 3 Model B

- microSDカード 32GB

- [スイッチングACアダプター（USB ACアダプター） MicroBオス 5V3A](https://akizukidenshi.com/catalog/g/gM-12001/)

## Raspberry Pi OSインストール

※**NOOBS**を使用したが現在は**Raspberry Pi Imager**の使用を推奨されている。2021/06/07

## 無線lan

```/etc/dhcpcd.conf```を編集してipアドレス変更。

``` txt
interface wlan0
static ip_address="固定id(ex:192.168.3.9/24)"
static routers="デフォルトデートウェイip"
static domain_name_servers="dnsサーバーip"
```

下記ファイルを編集して無線lan設定する。

  ``` bash
  network={
      ssid="ssid"
      # wpa_passphraseでパスワードを暗号化
      psk="password"
  }
  ```
  
- [無線LANのパスフレーズを暗号化するには？](https://www.atmarkit.co.jp/ait/articles/1601/23/news008.html)
上記を参考にする。

無線lan再起動。

``` bash
sudo ifdown wlan0
sudo ifup wlan0
```

**参考サイト:**
[How to connect to WiFi without reboot?](https://raspberrypi.stackexchange.com/questions/73749/how-to-connect-to-wifi-without-reboot)

## Raspberry Pi OSアップデート

次のコマンドを実行。

``` bash
sudo apt update
```

さらに次のコマンドを実行。

``` bash
sudo apt full-upgrade
```

**参考サイト:**

- [Updating and upgrading Raspberry Pi OS](https://www.raspberrypi.org/documentation/raspbian/updating.md)

## CUI/GUI切り替え

下記コマンドを実行。

``` bash
sudo raspi-config
```

起動したメニューから```1.System Option```>>```S5 Boot / Auto Login```を選択すれば切り替え選択肢がある。

## パッケージの自動アップデート方法

1. **unattended-upgrades**をインストールする。

   ``` bash
   sudo apt install unattended-upgrades
   ```

2. ```/etc/apt/apt.conf.d/50unattended-upgrades```を編集する。

3. ホワイトリストに下記を追加する。

   ``` txt
   Unattended-Upgrade::Allowed-Origins {
          // すべてのパッケージをアップデート
           "${distro_id}:${distro_codename}";
           "${distro_id}:${distro_codename}-security";
   };
   ```

4. ```/etc/apt/apt.conf.d/20auto-upgrades```を編集してアップデートを有効化する。

   ``` txt
   APT::Periodic::Update-Package-Lists "1";
   APT::Periodic::Unattended-Upgrade "1";
   APT::Periodic::Download-Upgradeable-Packages "1";
   APT::Periodic::AutocleanInterval "21";
   ```

5. ```sudo unattended-upgrade -d```でデバッグをする。
   ※エラーが出ていないのなら成功

**参考サイト:**

- [Using Unattended-Upgrades on Debian and Ubuntu](https://pimylifeup.com/unattended-upgrades-debian-ubuntu/)

### ログインユーザ変更

1. 次のコマンドを実行して一時ユーザを作成する。

   ``` bash
   # 仮のユーザ(tmp)を作成する
   sudo useradd -M tmp
   # tmpユーザをsudoグループに追加(そうしないとsudoが使えない)
   sudo gpasswd -a tmp sudo
   # tmpユーザのパスワードを設定
   sudo passwd tmp
   # ログアウトする
   exit
   ```

2. ユーザ名を変更する。

   ``` bash
   # usermod -lでユーザ名をpiからnewpiに変更
   sudo usermod -l newpi pi
   # usermod -dでホームディレクトリを/home/piから/home/newpiに変更
   sudo usermod -d /home/newpi -m newpi
   # groupmod -nでpiグループをnewpiグループに変更
   sudo groupmod -n newpi pi
   # ログアウト
   exit
   ```

3. パスワードを変更

   ``` bash
   # 仮ユーザを削除(警告的なのが出るかと思いますが、構わず削除して下さい)
   sudo userdel tmp
   # newpiユーザのパスワードを変更
   sudo passwd newpi
   ```

**参考サイト:**

- [[Raspbian]ユーザ名変更の個人的に「正しい」と思うやり方](https://jyn.jp/raspberrypi-username-change/)

## ssh接続(vscode)

作成中

## unattended-upgradesメール通知

作成中

## 公式ドキュメント

- [Raspberry Pi Documentation](https://www.raspberrypi.org/documentation/)
