

- [WSL で Docker コンテナーを始めよう \| Microsoft Learn](https://learn.microsoft.com/ja-jp/windows/wsl/tutorials/wsl-containers)
	- 前提条件 補足：PCメモリは8GB以上ないとDocker厳しい


### Windows Terminal をMicrosoft Store からインストール
- [Windows Terminal - Windows に無料でダウンロードしてインストールする \| Microsoft Store](https://apps.microsoft.com/detail/9n0dx20hk701?hl=ja-JP&gl=JP)
- Microsoft Storeから探すのでも〇
	- 参考： [WSL2 環境構築の手順](https://zenn.dev/tatsujin/articles/6938b76c084e4d)
	- [Windows ターミナルのインストール \| Microsoft Learn](https://learn.microsoft.com/ja-jp/windows/terminal/install)

### [WSL を使用して Windows に Linux をインストールする方法 \| Microsoft Learn](https://learn.microsoft.com/ja-jp/windows/wsl/install)
- Windows Terminal 上で PowerShell を管理者として実行
	- プルダウンよりPowerShell を右クリック → 管理者として実行
	- PowerShell上で wsl --install 実行し、PC再起動
		- 再起動後、PowerShell(管理者)  上で wsl -l -v 実行
			- (加えて自動的にUbuntuが起動しているはず)
		- Ubuntu-24.04 Running Versionが2 であることを確認
			- 異なる場合、PowerShell上で wsl --install -d Ubuntu-24.04 を実行

### Ubuntu の初期設定
- Ubuntu上でLinux のユーザー名とパスワードを設定する
	- パスワードは控えておく
	- ※Ubuntuが起動していない場合、Powershell上で wsl を実行すれば起動
- パッケージの更新とアップグレード
	- sudo apt update && sudo apt upgrade を実行
	- 求められるパスワードは、上記で設定したパスワード
		- Tip: sudo コマンド～ 実行時に求められる
- 参考：[WSL 開発環境を設定する \| Microsoft Learn](https://learn.microsoft.com/ja-jp/windows/wsl/setup/environment#set-up-your-linux-username-and-password)

### Dockerインストール
巷記事のDockerインストール方法は古い可能性があるため、以下公式Doc推奨
- [Ubuntu \| Docker Docs](https://docs.docker.com/engine/install/ubuntu/#install-using-the-convenience-script)
	- Install using the apt repository の１～３まで実施

### VSCodeインストール
- [Download Visual Studio Code - Mac, Linux, Windows](https://code.visualstudio.com/download)
	- インストーラーの違い [\[Visual Studio Code\] System InstallerとUser Installerの違い \| IT情報サイト ”ITアベイラボ”](https://shinmeisha.co.jp/newsroom/2021/03/27/visual-studio-code-system-installer%E3%81%A8user-installer%E3%81%AE%E9%81%95%E3%81%84/)
- 拡張設定
	- [Visual Studio Code と WSL を連携](https://www.drill-lancer.com/windows10_wsl2_almalinux9_wp-env.html#:~:text=%E8%B5%B7%E5%8B%95%E3%81%97%E3%81%BE%E3%81%99%E3%80%82-,Visual%20Studio%20Code%20%E3%81%A8%20WSL%20%E3%82%92%E9%80%A3%E6%90%BA,-%E3%81%93%E3%82%8C%E3%81%8B%E3%82%89%20Visual%20Studio)にある手順1～4で各拡張機能をインストール



