# Windows開発者のためのmacOS移行ガイド

本ガイドは、Windows PCからmacOSへ移行する開発者向けの包括的なチュートリアルです。

ログイン操作から開発環境構築まで、はまりやすいポイントを詳細に解説します。

---

## 目次

0. [購入前に：推奨スペック](#0-購入前に推奨スペック)
1. [初回起動とログイン設定](#1-初回起動とログイン設定)
2. [システム環境設定（最重要）](#2-システム環境設定最重要)
3. [キーボードとショートカットの違い](#3-キーボードとショートカットの違い)
4. [ターミナルとシェルの設定](#4-ターミナルとシェルの設定)
5. [Xcode と開発ツールのセットアップ](#5-xcode-と開発ツールのセットアップ)
6. [Homebrewのインストール](#6-homebrewのインストール)
7. [Gitの設定とSSHキー](#7-gitの設定とsshキー)
8. [Node.js環境（NVM）](#8-nodejs環境nvm)
9. [Python環境（pyenv）](#9-python環境pyenv)
10. [Docker Desktop](#10-docker-desktop)
11. [Flutter/iOS開発環境](#11-flutterios開発環境)
12. [VS Code のmacOS設定](#12-vs-code-のmacos設定)
13. [ファイルシステムの違い](#13-ファイルシステムの違い)
14. [セキュリティとGatekeeper](#14-セキュリティとgatekeeper)
15. [Rosetta 2とApple Silicon](#15-rosetta-2とapple-silicon)
16. [Mission ControlとStage Manager](#16-mission-controlとstage-manager)
17. [バックアップ（Time Machine）](#17-バックアップtime-machine)
18. [その他のはまりポイント](#18-その他のはまりポイント)
19. [よくある質問（FAQ）](#19-よくある質問faq)
20. [トラブルシューティング](#20-トラブルシューティング)

---

## 0. 購入前に：推奨スペック

### 0.1 RAM（メモリ）

| 用途 | 最小 | 推奨 |
| ---- | ---- | ---- |
| Web開発のみ | 8GB | 16GB |
| iOS開発（Xcode） | 16GB | 16GB以上 |
| iOS + Android両方 | 16GB | 32GB |
| 仮想化・Docker多用 | 16GB | 32GB以上 |

> **重要**: Apple Silicon Mac（M1以降）はRAMを後から増設できません。購入時に十分なメモリを選択してください。「ユニファイドメモリだから8GBでも大丈夫」という説は誤りです。

### 0.2 ストレージ

| 用途 | 最小 | 推奨 |
| --- | ---- | --- |
| 軽量な開発 | 256GB | 512GB |
| Xcode + Simulator | 512GB | 512GB以上 |
| 複数プロジェクト | 512GB | 1TB |

**ストレージを消費する主な要因:**

- Xcode本体: 約12GB
- iOSシミュレータ: 各バージョン5-10GB
- Xcode DerivedData: プロジェクトごとに2-10GB
- Docker images: 数GB〜数十GB
- macOSアップデート: 約35GBの空き容量が必要

### 0.3 推奨構成（2025年12月時点）

**コスパ重視:**

- Mac mini M4（16GB RAM / 256GB〜512GB）+ 外部モニター

**モバイル開発者向け:**

- MacBook Air M4（16GB RAM / 512GB）
- MacBook Pro M4（16GB〜32GB RAM / 512GB〜1TB）

---

## 1. 初回起動とログイン設定

### 1.1 macOSユーザーアカウント vs Apple ID

**重要な違い:**

- **macOSローカルユーザー**: Mac本体へのログインに使用（ユーザー名とパスワードを自分で設定）
- **Apple ID**: App Store、iCloud、Xcodeなどのサービス利用に必要

初回起動時の設定手順:

1. **言語と地域を選択**
2. **キーボード配列を選択**（日本語JISまたは英語US）
3. **Wi-Fiに接続**
4. **ローカルアカウントを作成**
   - フルネーム: 表示名
   - アカウント名: ホームディレクトリ名になる（英数字推奨、後から変更困難）
   - パスワード: Mac本体ログイン用
5. **Apple IDでサインイン**（後からでも可能）

> **はまりポイント**: Apple IDは設定アプリ（システム設定）やApp Storeへのログインに必要です。ローカルパスワードとApple IDパスワードは別物です。

### 1.2 Apple IDの作成

Apple IDを持っていない場合:

1. 「システム設定」→「Apple IDでサインイン」
2. 「Apple IDを作成」を選択
3. メールアドレスと生年月日を入力
4. 2ファクタ認証の設定（iPhoneがあると便利）

> 開発者として必要なアプリ（Xcode等）をダウンロードするにはApple IDが必須です。

---

## 2. システム環境設定（最重要）

### 2.1 スクロール方向の変更（最初にやるべき！）

**問題点**: macOSのデフォルトは「ナチュラルスクロール」で、Windowsと上下逆です。

**設定方法:**

1. 「システム設定」→「トラックパッド」→「スクロールとズーム」
2. 「ナチュラルなスクロール」のチェックを**オフ**

**マウスの場合:**

1. 「システム設定」→「マウス」
2. 「ナチュラルなスクロール」のチェックを**オフ**

> **注意**: トラックパッドとマウスの設定は連動しています。別々に設定したい場合は[UnnaturalScrollWheels](https://github.com/ther0n/UnnaturalScrollWheels)などのツールが必要です。

### 2.2 トラックパッドの設定

1. 「システム設定」→「トラックパッド」→「ポイントとクリック」
2. **「タップでクリック」を有効化**（Windowsのようにタップで選択可能に）
3. **「副ボタンのクリック」**で右クリック方法を選択

### 2.3 ウィンドウタイリング（macOS Sequoia 15の新機能）

macOS Sequoia 15では、Windowsの「Aero Snap」に似たウィンドウタイリング機能が追加されました。

**有効化:**

1. 「システム設定」→「デスクトップとDock」→「ウィンドウ」
2. 「ウィンドウをドラッグして画面端でタイル化」を有効化

**デフォルトのショートカット:**

| 操作 | ショートカット |
| ---- | ----------- |
| 左半分にタイル | Fn + Control + ← |
| 右半分にタイル | Fn + Control + → |
| 上半分にタイル | Fn + Control + ↑ |
| 下半分にタイル | Fn + Control + ↓ |
| フルスクリーン | Fn + Control + F |
| 中央配置 | Fn + Control + C |
| 元のサイズに戻す | Fn + Control + R |

> **注意**: これらのショートカットはGlobeキー（Fn）を使用します。外部キーボードでGlobeキーがない場合は動作しない場合があります。

### 2.4 Finderで隠しファイルを表示

開発者にとって `.git`, `.env`, `.zshrc` などの隠しファイルは必須です。

- **方法1: キーボードショートカット（一時的）**

    ```text
    Shift + Command + .（ピリオド）
    ```

- **方法2: ターミナルで永続設定**

    ```bash
    defaults write com.apple.finder AppleShowAllFiles YES
    killall Finder
    ```

### 2.5 フルキーボードアクセス

ダイアログでTabキーを使ってボタン間を移動できるようにする:

1. 「システム設定」→「キーボード」→「キーボードナビゲーション」を有効化

---

## 3. キーボードとショートカットの違い

### 3.1 修飾キーの対応表

| Windows | macOS | 記号 |
| ------- | ----- | ---- |
| Ctrl | Command (⌘) | ⌘ |
| Alt | Option (⌥) | ⌥ |
| Windows | Control (⌃) | ⌃ |
| - | Control (⌃) | ⌃ |

### 3.2 よく使うショートカットの対応

| 操作 | Windows | macOS |
| ---- | ------- | ----- |
| コピー | Ctrl+C | ⌘+C |
| 貼り付け | Ctrl+V | ⌘+V |
| 切り取り | Ctrl+X | ⌘+X |
| 元に戻す | Ctrl+Z | ⌘+Z |
| やり直し | Ctrl+Y / Ctrl+Shift+Z | ⌘+Shift+Z |
| 保存 | Ctrl+S | ⌘+S |
| 全選択 | Ctrl+A | ⌘+A |
| 検索 | Ctrl+F | ⌘+F |
| 閉じる | Alt+F4 | ⌘+Q |
| タブを閉じる | Ctrl+W | ⌘+W |
| アプリ切替 | Alt+Tab | ⌘+Tab |
| スクリーンショット | Win+Shift+S | ⌘+Shift+4 |
| ターミナル貼り付け | Ctrl+Shift+V | ⌘+V |

### 3.3 修飾キーのカスタマイズ

WindowsキーボードをMacで使う場合、ControlとCommandを入れ替える:

1. 「システム設定」→「キーボード」→「キーボードショートカット」→「修飾キー」
2. Caps Lock、Control、Option、Commandを好みに変更

---

## 4. ターミナルとシェルの設定

### 4.1 デフォルトシェルについて

- **macOS Catalina (10.15) 以降**: zshがデフォルト
- **それ以前**: bashがデフォルト

現在のシェルを確認:

```bash
echo $SHELL
```

### 4.2 設定ファイル

**zshの場合:**

- `~/.zprofile`: ログイン時に読み込まれる（PATH設定はここ）
- `~/.zshrc`: 新しいターミナルウィンドウごとに読み込まれる（エイリアス設定など）

**ファイルが存在しない場合は作成:**

```bash
touch ~/.zshrc
touch ~/.zprofile
```

> **重要**: macOSでは新規ユーザーの場合、これらのファイルがデフォルトで存在しません。手動で作成する必要があります。

### 4.3 PATHの設定

> **はまりポイント**: macOSの`path_helper`は`/etc/zprofile`で実行され、PATHを再順序付けします。

**Apple Silicon Mac (M1/M2/M3/M4) の場合:**

```bash
# ~/.zprofile に追加
eval "$(/opt/homebrew/bin/brew shellenv)"
```

**Intel Mac の場合:**

```bash
# ~/.zprofile に追加
eval "$(/usr/local/bin/brew shellenv)"
```

### 4.4 推奨ターミナルアプリ

- **標準ターミナル.app**: 十分使える
- **iTerm2**: 高機能な代替（`brew install --cask iterm2`）
- **Warp**: モダンなAI対応ターミナル
- **Alacritty**: 高速なGPU対応ターミナル

### 4.5 ターミナル基本操作（Windows CMDとの違い）

| 操作 | Windows CMD/PowerShell | macOS Terminal |
| ---- | -------------------- | -------------- |
| ディレクトリ内容表示 | `dir` | `ls` |
| 画面クリア | `cls` | `clear` または ⌘+K |
| ディレクトリ移動 | `cd` | `cd` |
| 現在のパス表示 | `cd`（引数なし） | `pwd` |
| ファイルコピー | `copy` | `cp` |
| ファイル移動/リネーム | `move` / `ren` | `mv` |
| ファイル削除 | `del` | `rm` |
| ディレクトリ作成 | `mkdir` | `mkdir` |
| ディレクトリ削除 | `rmdir` | `rmdir` または `rm -rf` |
| ファイル内容表示 | `type` | `cat` |
| テキスト検索 | `findstr` | `grep` |
| 管理者権限実行 | 管理者としてPowerShell | `sudo` コマンド |
| 環境変数表示 | `set` | `env` |
| コマンド中断 | Ctrl+C | Ctrl+C |
| コマンド履歴 | ↑↓キー | ↑↓キー |

**ターミナルの便利なショートカット:**

- `⌃A`: 行頭に移動
- `⌃E`: 行末に移動
- `⌃U`: カーソル前を削除
- `⌃K`: カーソル後を削除
- `⌃L`: 画面クリア
- `⌃R`: コマンド履歴検索
- `Tab`: ファイル名補完

---

## 5. Xcode と開発ツールのセットアップ

### 5.1 Xcode Command Line Toolsのインストール（必須）

> **これは最重要です！** Git、gcc、makeなどの基本的な開発ツールが含まれます。

```bash
xcode-select --install
```

ポップアップが表示されたら「インストール」をクリック。

**インストール確認:**

```bash
xcode-select -p
# 出力: /Library/Developer/CommandLineTools
```

### 5.2 フルXcodeのインストール（iOS/macOS開発に必要）

- **現在のバージョン（2025年12月時点）: Xcode 16.x**

1. App Storeを開く
2. 「Xcode」を検索
3. 「入手」→ダウンロード（約12GB以上、時間がかかる）

**または、複数バージョン管理には[xcodes](https://github.com/XcodesOrg/xcodes)ツールが便利:**

```bash
brew install xcodesorg/made/xcodes
xcodes install 16.2
```

**初回起動時:**

```bash
# ライセンス同意
sudo xcodebuild -license accept

# 初回セットアップ
sudo xcodebuild -runFirstLaunch
```

### 5.3 Xcodeパスの設定

> **はまりポイント**: Android Studioインストール後にパスが変わることがあります。

```bash
# 正しいパスに設定
sudo xcode-select --switch /Applications/Xcode.app/Contents/Developer
```

### 5.4 XcodeにApple IDを追加

1. Xcode → Settings（または Preferences）
2. 「Accounts」タブ
3. 左下の「+」ボタン → 「Apple ID」を選択
4. Apple IDでサインイン

> **注意**: App Store公開には年間$99のApple Developer Program登録が必要ですが、**実機テストは無料のApple IDでも可能**です（7日間の有効期限制限あり）。

---

## 6. Homebrewのインストール

Homebrewは macOS の非公式パッケージマネージャーで、開発者必須のツールです。

**対応環境（2025年12月時点）:**

- macOS Ventura 13以降推奨
- M1/M2/M3/M4 Apple Siliconおよび64-bit Intel対応

### 6.1 インストール

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

> このコマンドはXcode Command Line Toolsも自動でインストールします。

### 6.2 PATHの設定（Apple Silicon Mac）

インストール完了後に表示される指示に従う:

```bash
# ~/.zprofile に追加
echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> ~/.zprofile

# 設定を反映
eval "$(/opt/homebrew/bin/brew shellenv)"
```

### 6.3 インストール確認

```bash
brew --version
brew doctor
```

### 6.4 よく使うコマンド

```bash
brew install <package>      # パッケージインストール
brew install --cask <app>   # GUIアプリインストール
brew update                 # Homebrew自体を更新
brew upgrade                # インストール済みパッケージを更新
brew list                   # インストール済み一覧
brew search <keyword>       # パッケージ検索
```

### 6.5 開発者向け推奨パッケージ

```bash
# 基本ツール
brew install git
brew install wget
brew install jq
brew install tree

# GUIアプリ
brew install --cask visual-studio-code
brew install --cask iterm2
brew install --cask docker
```

---

## 7. Gitの設定とSSHキー

### 7.1 Git初期設定

```bash
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```

### 7.2 改行コードの設定

> **はまりポイント**: WindowsとmacOSで改行コードが異なります。

**macOSでの設定:**

```bash
git config --global core.autocrlf input
```

**Windows（参考）:**

```bash
git config --global core.autocrlf true
```

### 7.3 デフォルトブランチ名

```bash
git config --global init.defaultBranch main
```

### 7.4 SSHキーの生成

```bash
# Ed25519（推奨）
ssh-keygen -t ed25519 -C "your.email@example.com"

# または RSA
ssh-keygen -t rsa -b 4096 -C "your.email@example.com"
```

> **注意**: GitHub は 2022年3月以降、DSAキー（ssh-dss）をサポートしていません。

### 7.5 SSH設定ファイル

`~/.ssh/config` を作成/編集:

```text
Host github.com
  HostName github.com
  User git
  AddKeysToAgent yes
  UseKeychain yes
  IdentityFile ~/.ssh/id_ed25519
```

> パスフレーズを設定しなかった場合は `UseKeychain yes` の行を省略してください。

### 7.6 ssh-agentへの追加

```bash
# ssh-agentを起動
eval "$(ssh-agent -s)"

# キーを追加（macOS）
ssh-add --apple-use-keychain ~/.ssh/id_ed25519
```

### 7.7 GitHubへの公開鍵登録

```bash
# クリップボードにコピー
pbcopy < ~/.ssh/id_ed25519.pub
```

GitHubの Settings → SSH and GPG keys → New SSH key で貼り付け。

**接続テスト:**

```bash
ssh -T git@github.com
# "You've successfully authenticated" と表示されれば成功
```

---

## 8. Node.js環境（NVM）

### 8.1 NVMのインストール

> **注意**: Homebrewでのnvmインストールは公式にサポートされていません。

**最新版（v0.40.3）のインストール:**

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.3/install.sh | bash
```

### 8.2 シェル設定

`~/.zshrc` に以下を追加（自動で追加されることもある）:

```bash
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"
```

> **はまりポイント**: macOSでは `~/.zshrc` がデフォルトで存在しない場合があります。`touch ~/.zshrc` で作成してからインストールスクリプトを再実行してください。

設定を反映:

```bash
source ~/.zshrc
```

### 8.3 Node.jsのインストール

```bash
# 最新LTS版をインストール（推奨）
nvm install --lts

# 最新版をインストール
nvm install node

# 特定バージョン
nvm install 22

# デフォルト設定
nvm alias default 22

# バージョン切り替え
nvm use 20
```

**2025年12月時点のNode.jsバージョン:**

- LTS（推奨）: v22.x
- Current: v23.x

### 8.4 Apple Siliconでの注意点

Node.js v16.0以降はApple Siliconをネイティブサポートしています。古いバージョン（v16未満）を使用する場合はRosetta 2経由でx86_64としてコンパイルが必要になる場合があります。

---

## 9. Python環境（pyenv）

### 9.1 pyenvのインストール

**前提条件（ビルド依存関係）:**

```bash
brew install openssl readline sqlite3 xz zlib tcl-tk
```

**pyenvをインストール:**

```bash
brew install pyenv
```

### 9.2 シェル設定

`~/.zshrc` に以下を追加:

```bash
export PYENV_ROOT="$HOME/.pyenv"
[[ -d $PYENV_ROOT/bin ]] && export PATH="$PYENV_ROOT/bin:$PATH"
eval "$(pyenv init -)"
```

設定を反映:

```bash
source ~/.zshrc
```

### 9.3 Pythonのインストール

```bash
# 利用可能なバージョンを確認
pyenv install --list

# 特定バージョンをインストール
pyenv install 3.12.0

# グローバルで使用するバージョンを設定
pyenv global 3.12.0

# プロジェクトごとに設定（.python-versionファイルが作成される）
pyenv local 3.11.0

# バージョン確認
python --version
```

### 9.4 Apple Siliconでの注意点

Python 3.9.1以降はApple Siliconをネイティブサポート。古いバージョンが必要な場合:

```bash
# x86_64としてインストール
arch -x86_64 pyenv install 3.8.10
```

### 9.5 仮想環境（pyenv-virtualenv）

```bash
# インストール
brew install pyenv-virtualenv

# ~/.zshrc に追加
eval "$(pyenv virtualenv-init -)"

# 仮想環境を作成
pyenv virtualenv 3.12.0 myproject-env

# 有効化
pyenv activate myproject-env

# 無効化
pyenv deactivate
```

---

## 10. Docker Desktop

### 10.1 インストール

**Homebrewを使う場合:**

```bash
brew install --cask docker
```

**または公式サイトからダウンロード:**
<https://docs.docker.com/desktop/setup/install/mac-install/>

**システム要件（2025年12月時点）:**

- macOS Sonoma、Sequoia、Tahoeをサポート
- macOS Ventura以前は非サポート
- 4GB RAM以上（8GB以上推奨）

### 10.2 Apple Silicon (M1/M2/M3/M4) での注意点

**重要な変更（2025年）:**

- Apple Virtualization がデフォルトVMMに
- QEMU は2025年7月14日に完全に非推奨化

**Rosetta 2のインストール（推奨）:**

```bash
softwareupdate --install-rosetta
```

### 10.3 初回起動

1. Applicationsフォルダから「Docker」を起動
2. システム権限の許可を求められるので許可
3. ログインパスワードを入力

### 10.4 x86イメージの実行

```bash
# プラットフォームを明示的に指定
docker run --platform linux/amd64 <image-name>
```

### 10.5 Docker Model Runner（Beta）

Docker Desktop 4.53.0以降では、Apple Silicon Mac向けにDocker HubからAIモデルを直接実行できる新機能が追加されています。

---

## 11. Flutter/iOS開発環境

### 11.1 Flutterのインストール

```bash
brew install --cask flutter
```

または公式サイトからダウンロード。

**2025年12月時点の推奨:**

- Flutter 3.22以降を使用（Xcode 16およびiOS 18対応）
- 最新安定版: Flutter 3.27+

### 11.2 Flutter Doctorで確認

```bash
flutter doctor
```

### 11.3 Xcodeパスの設定

```bash
sudo sh -c 'xcode-select -s /Applications/Xcode.app/Contents/Developer && xcodebuild -runFirstLaunch'
```

### 11.4 iPhoneへの接続（実機テスト）

> **これが最大のはまりポイント！**

- **手順1: iPhoneをUSBケーブルで接続**
    - Apple純正またはMFi認証ケーブルを使用
    - サードパーティ製はデータ転送に対応していない場合あり

- **手順2: 「このコンピュータを信頼しますか？」**
    - iPhone側で「信頼」をタップ
    - パスコードを入力

- **手順3: デベロッパモードを有効化（iOS 16以降必須）**
    1. iPhoneの「設定」→「プライバシーとセキュリティ」→「デベロッパモード」
    2. オンにして再起動

- **手順4: Xcodeでデバイスを認識**
    1. Xcode → Window → Devices and Simulators
    2. 接続されたiPhoneを選択
    3. 「Enable Device for Development」をクリック

### 11.5 コード署名の設定

> **無料Apple IDでも実機テストは可能**（7日間の制限あり）

1. Xcodeでプロジェクトを開く（`ios/Runner.xcworkspace`）
2. Runner → Signing & Capabilities
3. 「Automatically manage signing」にチェック
4. Teamで「Personal Team」を選択

### 11.6 よくあるエラーと対処法

**「Untrusted Developer」エラー:**

1. iPhoneの「設定」→「一般」→「VPNとデバイス管理」
2. デベロッパAPPで自分のメールアドレスを選択
3. 「信頼」をタップ

**「iPhone is not paired」エラー:**

```bash
# Xcodeで再ペアリング
# Xcode → Window → Devices and Simulators
# デバイスを右クリック → Unpair Device → 再接続
```

**パス問題:**

```bash
sudo xcode-select --switch /Applications/Xcode.app/Contents/Developer
sudo xcodebuild -runFirstLaunch
```

**CocoaPods関連エラー:**

```bash
# CocoaPodsを再インストール
sudo gem install cocoapods
cd ios && pod install --repo-update
```

### 11.7 App Store公開の注意（2025年）

> **重要**: Appleは2025年8月以降、Xcode 16以降でビルドされたアプリのみApp Store更新を受け付けます。

---

## 12. VS Code のmacOS設定

### 12.1 インストール

```bash
brew install --cask visual-studio-code
```

### 12.2 ターミナル統合のショートカット

| 操作 | ショートカット |
| ---- | ----------- |
| ターミナル表示/非表示 | ⌃` (Control + バッククォート) |
| 新規ターミナル作成 | ⌃⇧` |
| 外部ターミナルで開く | ⇧⌘C |
| エディタとターミナル間を移動 | ⌘J |

### 12.3 macOS向け推奨設定

`settings.json` に追加:

```json
{
  // macOSのキーリピートを有効化（Vim拡張等で必要）
  "editor.suggest.snippetsPreventQuickSuggestions": true,

  // 統合ターミナルのシェル設定
  "terminal.integrated.defaultProfile.osx": "zsh",

  // フォント設定（Retina対応）
  "editor.fontSize": 13,
  "editor.lineHeight": 1.5,

  // ファイル保存時の設定
  "files.autoSave": "afterDelay",
  "files.autoSaveDelay": 1000
}
```

### 12.4 Vim拡張使用時の設定

ターミナルでキーリピートを有効化:

```bash
defaults write com.microsoft.VSCode ApplePressAndHoldEnabled -bool false
```

### 12.5 コマンドラインから起動

VS Codeのコマンドパレット（⌘+Shift+P）→ 「Shell Command: Install 'code' command in PATH」を実行。

```bash
# カレントディレクトリをVS Codeで開く
code .

# 特定ファイルを開く
code path/to/file.txt
```

---

## 13. ファイルシステムの違い

### 13.1 パス区切り文字

| OS | 区切り文字 | 例 |
| -- | --------- | -- |
| Windows | バックスラッシュ `\` | `C:\Users\name\Documents` |
| macOS | スラッシュ `/` | `/Users/name/Documents` |

### 13.2 大文字小文字の扱い

- **macOS**: デフォルトで**大文字小文字を区別しない**（case-insensitive）
- **Linux**: 大文字小文字を**区別する**（case-sensitive）

> **はまりポイント**: macOSで動作してもLinuxサーバーで `import Component` と `import component` が異なるファイルを指す可能性があります。

**対策:**

- ファイル名は常に小文字で統一
- Gitで大文字小文字の変更を検知:

```bash
git config --global core.ignorecase false
```

### 13.3 ホームディレクトリ

| OS | パス |
| -- | ---- |
| Windows | `C:\Users\<username>` |
| macOS | `/Users/<username>` |

シェルでは `~` で参照可能。

### 13.4 隠しファイル

- **Windows**: ファイル属性で「隠しファイル」設定
- **macOS/Linux**: ファイル名が `.`（ドット）で始まる

---

## 14. セキュリティとGatekeeper

### 14.1 Gatekeeperとは

App Store外のアプリを制限するmacOSのセキュリティ機能。

### 14.2 macOS Sequoiaでの重要な変更

> **Sequoia 15以降の変更**: 従来のControl+クリックによるGatekeeperバイパスが廃止されました。

### 14.3 アプリを開けない場合

**「開発元が未確認のため開けません」エラー:**

- **方法1: システム設定から許可（推奨）**

1. まずアプリを通常通り開こうとする（エラーが表示される）
2. 「システム設定」→「プライバシーとセキュリティ」
3. 下にスクロールして「セキュリティ」セクション
4. 「このまま開く」ボタンをクリック
5. 管理者パスワードを入力

> **注意**: macOS Sequoiaでは、この方法が唯一の正式な方法になりました。

### 14.4 開発者向け：Gatekeeperの無効化（非推奨）

```bash
# 無効化（セキュリティリスクあり）
sudo spctl --global-disable

# 再有効化
sudo spctl --global-enable
```

> 「Anywhere」オプションは有効化後約8分で再び非表示になります。
> 通常は無効化せず、個別のアプリを許可する方法を使用してください。

---

## 15. Rosetta 2とApple Silicon

### 15.1 Rosetta 2とは

Rosetta 2は、Intel Mac向けに開発されたアプリをApple Silicon（M1/M2/M3/M4）Mac上で動作させるための翻訳レイヤーです。

### 15.2 インストール方法

初回起動時に自動でインストールを促されますが、手動でインストールする場合:

```bash
softwareupdate --install-rosetta
```

### 15.3 Rosetta 2の将来（重要）

> **2025年6月にAppleが発表**: Rosetta 2はmacOS 27まで利用可能。macOS 28（2027年予定）で大部分の機能が削除されます。

**開発者への影響:**

- Intel用アプリは今後数年で動作しなくなる可能性
- 古いツールや依存関係のUniversal Binary化を計画的に進める必要がある
- ゲーム向けの一部機能は残存予定

### 15.4 アーキテクチャの確認

```bash
# 現在のアーキテクチャ確認
uname -m
# arm64: Apple Silicon ネイティブ
# x86_64: Rosetta 2経由またはIntel Mac

# アプリのアーキテクチャ確認
file /path/to/executable

# Rosetta 2で強制実行
arch -x86_64 /path/to/command
```

### 15.5 Rosetta 2の制限事項

以下はRosetta 2で動作しません:

- カーネル拡張（kext）を使用するアプリ
- x86_64仮想化を行うアプリ（例: 古いVMware）
- AVX/AVX2/AVX512命令を使用するアプリ

---

## 16. Mission ControlとStage Manager

### 16.1 Mission Control

すべてのウィンドウを俯瞰し、仮想デスクトップ（Spaces）を管理する機能。

**起動方法:**

- トラックパッド: 3本指で上スワイプ
- キーボード: F3（または⌃↑）
- マウス: Mission Controlボタン（対応マウスのみ）

**Spacesの追加:**

1. Mission Controlを起動
2. 画面上部の「+」をクリック
3. 新しいデスクトップが作成される

**Spaces間の移動:**

- トラックパッド: 3本指で左右スワイプ
- キーボード: ⌃← / ⌃→

### 16.2 Stage Manager

macOS Venturaで追加されたウィンドウ管理機能。現在の作業に集中しながら、他のアプリにも素早くアクセスできます。

**有効化:**

1. 「システム設定」→「デスクトップとDock」
2. 「Stage Manager」をオン

または:

- コントロールセンターからトグル

**開発者向けの活用例:**

- IDE + ターミナル + ブラウザをグループ化
- ドキュメント参照用のグループを別途作成
- プロジェクトごとにグループを分ける

### 16.3 Mission Control + Stage Manager の組み合わせ

Stage ManagerをSpacesと組み合わせると:

- Space 1: 開発作業（IDE + ターミナル）
- Space 2: コミュニケーション（Slack + メール）
- Space 3: ドキュメント・調査

それぞれのSpaceでStage Managerのグループを作成できます。

> **注意**: Stage Managerが有効にならない場合は、「システム設定」→「デスクトップとDock」→「Mission Control」→「ディスプレイごとに個別のSpacesを使用」をオンにしてください。

---

## 17. バックアップ（Time Machine）

### 17.1 Time Machineとは

macOS標準のバックアップ機能。外付けHDD/SSDやNASに自動的にバックアップを取ります。

### 17.2 設定方法

1. 外付けドライブを接続
2. 「システム設定」→「一般」→「Time Machine」
3. 「バックアップディスクを追加」→ドライブを選択
4. 「今すぐバックアップ」または自動バックアップを待つ

### 17.3 開発者向けの除外設定

大きなファイルやキャッシュを除外してバックアップ時間を短縮:

「オプション」→「除外項目」に以下を追加:

- `~/Library/Developer/Xcode/DerivedData`（Xcodeキャッシュ）
- `~/.npm`（npmキャッシュ）
- `~/Library/Caches`
- `node_modules`フォルダ（各プロジェクト）
- Docker関連ファイル

### 17.4 復元方法

**ファイル単位の復元:**

1. Finderで復元したいフォルダを開く
2. メニューバー「Time Machine」→「Time Machineに入る」
3. 過去のバージョンを選んで「復元」

**システム全体の復元:**

1. macOS復旧モードで起動（M1以降: 電源ボタン長押し）
2. 「Time Machineから復元」を選択

---

## 18. その他のはまりポイント

### 18.1 ライブ変換（日本語入力）

macOSはデフォルトで入力中に自動変換されます。

**無効化:**

1. メニューバーの入力メニュー（「あ」等）をクリック
2. 「ライブ変換」のチェックを外す

または:

1. 「システム設定」→「キーボード」→「入力ソース」→「編集」
2. 日本語入力設定で「ライブ変換」をオフ

### 18.2 スクリーンショット

| 操作 | ショートカット |
| ---- | ------------ |
| 画面全体 | ⌘+Shift+3 |
| 範囲選択 | ⌘+Shift+4 |
| ウィンドウ | ⌘+Shift+4 → Space |
| タッチバー | ⌘+Shift+6 |
| スクリーンショットアプリ | ⌘+Shift+5 |

### 18.3 Finderのパス表示

**パスバーを表示:**

- Finder → 表示 → パスバーを表示
- または ⌘+Option+P

### 18.4 ファイルパスのコピー

Finderでファイルを選択 → ⌘+Option+C

または右クリック時にOptionキーを押すと「パス名をコピー」が表示。

### 18.5 アプリの完全終了

- Windowsでは ✕ ボタンでアプリが終了
- macOSでは ✕ ボタンはウィンドウを閉じるだけ
- アプリを終了: ⌘+Q

### 18.6 Spotlight（検索）

- **起動**: ⌘+Space
- アプリ起動、ファイル検索、計算など多機能

### 18.7 Finder操作（Windowsエクスプローラーとの違い）

| 操作 | Windows | macOS |
| --- | ------- | ----- |
| ファイル名変更 | F2 | Enter（選択後） |
| ファイルを開く | Enter | ⌘+O |
| ファイル削除 | Delete | ⌘+Delete |
| 新規フォルダ | Ctrl+Shift+N | ⌘+Shift+N |
| 上の階層へ | Alt+↑ | ⌘+↑ |
| 戻る | Alt+← | ⌘+[ |
| 進む | Alt+→ | ⌘+] |
| 新規ウィンドウ | Ctrl+N | ⌘+N |
| ファイル情報 | Alt+Enter | ⌘+I |
| 検索 | Ctrl+F | ⌘+F |
| 切り取り→貼り付け | Ctrl+X → Ctrl+V | ⌘+C → ⌘+Option+V |

> **注意**: macOSのFinderには「切り取り」がありません。代わりにコピー（⌘+C）後、⌘+Option+Vで「移動」ができます。

### 18.8 外部ディスプレイ接続

**Apple Silicon Macの注意点:**

- 多くのMacBookは外部ディスプレイ1台のみ対応（クラムシェル含む）
- Mac Studio/Mac Proは複数ディスプレイ対応
- M3 Pro/Max以降は複数ディスプレイ対応

**ディスプレイ設定:**

1. 「システム設定」→「ディスプレイ」
2. 「配置」タブでモニター位置を調整
3. メインディスプレイはドラッグで白いバーを移動

**HiDPI（Retina）スケーリング:**

- 外部4Kモニターでは「スケーリング」設定が重要
- 「スペースを拡大」で作業領域を増やせる
- 低解像度モニターでは文字がぼやける場合あり

### 18.9 Apple Intelligence（macOS Sequoia 15.1以降）

M1以降のApple Siliconを搭載したMacでは、Apple Intelligenceが利用可能です:

- Siriの機能強化
- Writing Tools（文章校正・トーン変更）
- スマート返信
- ChatGPT連携

### 18.10 日本語キーボード（JIS配列）の注意点

Windows JISキーボードとmacOS JISキーボードでは配列が異なります。

**主な違い:**

- 「英数」「かな」キー: macOSではスペースキーの左右に配置
- 「¥」キー: macOSでは「\」と「¥」の切り替えが必要な場合あり
- Windowsキーボード使用時: 「Caps Lock」を「英数」として使う設定が便利

**設定:**

1. 「システム設定」→「キーボード」→「入力ソース」→「編集」
2. 「Caps Lockで入力ソースを切り替え」をオン

---

## 19. よくある質問（FAQ）

### Q1: macOSのアップデートはすぐにすべき？

**A**: 開発者は少し待つことを推奨します。

- メジャーアップデート（例: 15.0）: 1-2週間様子を見る
- マイナーアップデート（例: 15.2）: 1週間程度で適用
- セキュリティアップデート: できるだけ早く適用

特にXcode、Flutter、React Nativeなどは新OSへの対応に時間がかかることがあります。

### Q2: Intel MacとApple Silicon Macどちらを買うべき？

**A**: 2025年以降は**Apple Silicon Mac一択**です。

- Intel Macは2025年のmacOS Tahoeが最後のサポート
- Rosetta 2も2027年に大部分廃止予定
- 新しいApple Siliconは省電力で高性能

### Q3: RAM 8GBで開発できる？

**A**: 軽量なWeb開発なら可能ですが、推奨しません。

- Xcode使用: 16GB必須
- Docker使用: 16GB以上推奨
- 複数IDE同時起動: 16GB以上推奨
- Apple Silicon MacはRAM増設不可なので、購入時に多めに

### Q4: Windows用のソフトは使える？

**A**:

- **ネイティブ動作**: 不可（Windowsアプリは動作しない）
- **仮想化**: Parallels Desktop、UTMなどでWindows VMを実行可能（ARM版Windows）
- **Wine/CrossOver**: 一部のWindowsアプリを変換実行可能
- **代替アプリ**: 多くのソフトにはmacOS版または代替がある

### Q5: iPhoneがなくてもiOS開発できる？

**A**: シミュレータでの開発は可能です。ただし:

- 実機テストにはiPhoneが必要
- カメラ、GPS、加速度センサーなどのテストは実機のみ
- App Store公開前には実機テスト推奨
- 中古iPhoneでも開発用途には十分

### Q6: ウイルス対策ソフトは必要？

**A**: 基本的には不要です。

- macOSにはXProtect、Gatekeeperなどの保護機能が内蔵
- 信頼できるソースからのみアプリをインストール
- 企業ポリシーで必要な場合は別途導入

---

## 20. トラブルシューティング

### 20.1 「コマンドが見つかりません」エラー

```bash
# PATHを確認
echo $PATH

# Homebrewのパスが含まれているか確認
# Apple Silicon: /opt/homebrew/bin
# Intel: /usr/local/bin
```

**対処:**

```bash
# ~/.zprofile に追加
eval "$(/opt/homebrew/bin/brew shellenv)"
source ~/.zprofile
```

### 20.2 権限エラー

```bash
# 実行権限を付与
chmod +x script.sh

# 所有権の確認
ls -la filename
```

### 20.3 macOSアップデート後の問題

```bash
# Xcode Command Line Toolsを再インストール
sudo rm -rf /Library/Developer/CommandLineTools
xcode-select --install
```

### 20.4 「Operation not permitted」エラー

「システム設定」→「プライバシーとセキュリティ」→「フルディスクアクセス」でターミナルを許可。

### 20.5 Homebrew関連の問題

```bash
# 診断
brew doctor

# キャッシュクリア
brew cleanup

# 再インストール
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/uninstall.sh)"
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

### 20.6 Xcodeキャッシュのクリア

問題が発生した場合:

1. 「システム設定」→「一般」→「ストレージ」→「開発者」
2. 「Xcodeキャッシュを削除」

---

## 付録A: 推奨初期セットアップチェックリスト

### システム設定

```text
[ ] Apple IDでサインイン
[ ] ナチュラルスクロールを無効化
[ ] タップでクリックを有効化
[ ] ウィンドウタイリングを有効化（Sequoia）
[ ] 隠しファイルを表示（⌘+Shift+.）
[ ] フルキーボードアクセスを有効化
[ ] ライブ変換を無効化（日本語入力）
```

### 開発ツール

```text
[ ] Xcode Command Line Toolsをインストール
[ ] Homebrewをインストール
[ ] ~/.zshrc と ~/.zprofile を作成
[ ] Gitを設定（user.name, user.email, core.autocrlf）
[ ] SSHキーを生成してGitHubに登録
[ ] NVMとNode.jsをインストール
[ ] pyenvとPythonをインストール（必要な場合）
[ ] VS Codeをインストール
[ ] Docker Desktopをインストール（必要な場合）
[ ] フルXcodeをインストール（iOS開発の場合）
[ ] Flutter環境を構築（必要な場合）
```

### バックアップ

```text
[ ] Time Machineを設定
[ ] 除外項目を設定（DerivedData, node_modules等）
```

---

## 付録B: よく使うコマンド早見表

| 目的 | コマンド |
| ---- | ------- |
| ファイル一覧 | `ls -la` |
| 隠しファイル込み | `ls -la` |
| ディレクトリ移動 | `cd path/to/dir` |
| ホームへ移動 | `cd ~` |
| 現在のパス | `pwd` |
| ファイルコピー | `cp source dest` |
| ファイル移動 | `mv source dest` |
| ファイル削除 | `rm filename` |
| ディレクトリ削除 | `rm -rf dirname` |
| 権限変更 | `chmod +x filename` |
| プロセス確認 | `ps aux` |
| ポート確認 | `lsof -i :8080` |
| 環境変数確認 | `env` |
| パス確認 | `echo $PATH` |

---

## 付録C: おすすめエイリアス設定

`~/.zshrc` に追加:

```bash
# ディレクトリ操作
alias ll='ls -la'
alias la='ls -A'
alias ..='cd ..'
alias ...='cd ../..'

# Git
alias gs='git status'
alias ga='git add'
alias gc='git commit'
alias gp='git push'
alias gl='git log --oneline'
alias gd='git diff'
alias gco='git checkout'
alias gb='git branch'

# npm/yarn
alias ni='npm install'
alias nr='npm run'
alias yi='yarn install'

# Python
alias python='python3'
alias pip='pip3'

# Docker
alias dc='docker compose'
alias dps='docker ps'

# その他
alias c='clear'
alias h='history'
alias grep='grep --color=auto'

# macOS固有
alias showfiles='defaults write com.apple.finder AppleShowAllFiles YES && killall Finder'
alias hidefiles='defaults write com.apple.finder AppleShowAllFiles NO && killall Finder'
alias flushdns='sudo dscacheutil -flushcache; sudo killall -HUP mDNSResponder'
```

---

## 付録D: バージョン確認コマンド一覧

開発環境のセットアップ後、以下で各ツールのバージョンを確認:

```bash
# システム
sw_vers                      # macOSバージョン
uname -m                     # アーキテクチャ（arm64/x86_64）

# 開発ツール
xcode-select -v             # Xcode Command Line Tools
xcodebuild -version         # Xcode（フル版）
brew --version              # Homebrew
git --version               # Git

# ランタイム
node --version              # Node.js
npm --version               # npm
nvm --version               # NVM
python --version            # Python
pyenv --version             # pyenv

# コンテナ・モバイル
docker --version            # Docker
flutter --version           # Flutter

# シェル
echo $SHELL                 # 現在のシェル
zsh --version               # zshバージョン
```

---

## 付録E: Brewfile（環境の再現）

Homebrewでインストールしたパッケージを一括管理できます。

### Brewfileの作成

現在の環境をエクスポート:

```bash
brew bundle dump --file=~/Brewfile
```

### Brewfileの例

```ruby
# ~/Brewfile
tap "homebrew/bundle"
tap "homebrew/cask"

# CLI tools
brew "git"
brew "wget"
brew "jq"
brew "tree"
brew "nvm"
brew "pyenv"

# Development
brew "openssl"
brew "readline"
brew "sqlite3"

# Applications
cask "visual-studio-code"
cask "iterm2"
cask "docker"
cask "google-chrome"
cask "slack"
cask "notion"

# Fonts (optional)
tap "homebrew/cask-fonts"
cask "font-hack-nerd-font"
```

### 新しいMacでの環境復元

```bash
# Brewfileから一括インストール
brew bundle --file=~/Brewfile

# または、Brewfileと同じディレクトリで
brew bundle
```

---

## 参考リンク

### Apple公式

- [What's new in macOS Sequoia - Apple Support](https://support.apple.com/en-us/120283)
- [Mac keyboard shortcuts - Apple Support](https://support.apple.com/en-us/102650)
- [macOS Sequoia Window Tiling Guide](https://support.apple.com/guide/mac-help/mac-window-tiling-icons-keyboard-shortcuts-mchl9674d0b0/mac)
- [Xcode Command Line Tools](https://developer.apple.com/documentation/xcode/installing-the-command-line-tools/)
- [Stage Manager - Apple Support](https://support.apple.com/guide/mac-help/use-stage-manager-mchl534ba392/mac)

### 開発者向けツール

- [Homebrew公式サイト](https://brew.sh/)
- [GitHub SSH設定ドキュメント](https://docs.github.com/en/authentication/connecting-to-github-with-ssh)
- [NVM公式リポジトリ](https://github.com/nvm-sh/nvm)
- [pyenv公式リポジトリ](https://github.com/pyenv/pyenv)
- [VS Code キーボードショートカット (macOS)](https://code.visualstudio.com/shortcuts/keyboard-shortcuts-macos.pdf)

### モバイル・コンテナ開発

- [Flutter iOS Setup](https://docs.flutter.dev/platform-integration/ios/setup)
- [Docker Desktop for Mac](https://docs.docker.com/desktop/setup/install/mac-install/)

### セットアップガイド（外部）

- [Mac Install Guide](https://mac.install.guide/)
- [Mac Setup for Web Development 2025](https://www.robinwieruch.de/mac-setup-web-development/)

---

*本ガイドは2025年12月時点の情報に基づいています。macOSのバージョンアップにより設定方法が変更される場合があります。*
