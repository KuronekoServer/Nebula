# Nebula

KuronekoLauncher用のdistribution.jsonを生成します。このフォーマットに関するドキュメントは [ここ][distro.md] にあります。

## Requirements

* Node.js 16
* Java 8+ (https://adoptopenjdk.net/)
  * Forge インストーラの実行、[XZ](https://tukaani.org/xz/format.html) ファイルの処理、および mod ファイルのバイトコード解析の実行に必要です。
  * 1.17 は Java 16 を必要としますが、forge インストーラは Java 8 で動作します。


### Notes

* Nebulaは100%完成しているわけではありません。未完成の項目は [TODO リスト](https://github.com/dscalzi/Nebula/issues/2) にリストアップされています。
* 現在、Forge ベースのサーバーの作成のみをサポートしています。バニラサポートはヘリオス v2 が完了したときに追加される予定です。

## Setup

1. リポジトリをクローンする
2. 依存関係をインストールする (`npm i`)
3. クローンしたフォルダのルートディレクトリに [`.env`][dotenvnpm] というファイルを作成し、必要な値を設定する。

Example
```properties
JAVA_EXECUTABLE=C:\Program Files\Eclipse Foundation\jdk-17.0.0.35-hotspot\bin\java.exe
ROOT=D:\TestRoot2
BASE_URL=http://localhost:8080/
HELIOS_DATA_FOLDER=C:\Users\user\AppData\Roaming\Helios Launcher
```

## Usage

Nebulaはまだ開発中です。使い方は変わるかもしれませんが、ここしばらくは安定した状態を保っています。

#### TL;DR (Too Long; Didn't Read) Usage

これは、全体的な使い方の骨子となるものです。このドキュメントの残りの部分をお読みください。

* 上記のセットアップ手順に従ってください。
* init root` コマンドを実行します。
* g server` コマンドを使用してサーバを生成します。
* すべてのファイルをそれぞれのフォルダに入れます (以下で説明します)。
* g distro` コマンドを使用してディストリビューションを生成します。
* 疑わしいことがあったら、このドキュメントを読み直してから Discord で質問してください。

## Commands

コマンドはここで文書化されます。どのコマンドも `--help` オプションを付けて実行すると、より詳細な情報を見ることができます。

### Command Usage

以下に示すコマンドの実行方法について説明します。コマンドの実行にはいくつかの方法がありますので、お好みの方法をお選びください。

例 例：`init root` を実行するには、`npm run start -- init root` と実行します。

*Recommended*

* **npm run start -- <COMMAND>`** を実行します。
  * *なぜこれが推奨されるのでしょうか？このコマンドは、最初にソースコードをコンパイルします。

*Other*

* Build the project using **`npm run build`**
* Run **`node dist/index.js <COMMAND>`** OR **`npm run faststart -- <COMMAND>`**
  * `faststart` は、ビルドせずにメインファイルを実行するためのエイリアスです。.

> ***Note:***
> - ***ファイルを修正した場合は、プロジェクトを再構築する必要があります。***
> - ***gitからpullした後、プロジェクトを再構築する必要があります。***
>
> ***npm start does this automatically.***

---

### Init

Initコマンドは、空のファイル構造を初期化するために使用されます。

Aliases: [`init`, `i`]

__*Subcommands*__

---

#### Init Root

空の標準ファイル構造を生成する。JSONスキーマも生成される。

`init root`

---

### Generate

Generateコマンドは生成に使用されます。

Aliases: [`generate`, `g`]

__*SubCommands*__

---

#### Generate Server

ルート・ディレクトリーに新しいサーバーを生成します。生成されるサーバーに forge を含めるためのオプションが提供されます。

`generate server <id> <version> <options>`

Options:

* `--forge <string>` forgeのバージョンを指定します。これは minecraft のバージョンを除いたものです (例: 14.23.5.2847)
  * OPTIONAL (デフォルト: null)
  * 提供されない場合、forge は有効になりません。
  * `latest` または `recommended` を指定すると、forge の最新/推奨バージョンを使用することができます。

>
> Example Usage
>
> `generate server Test1 1.12.2 --forge 14.23.5.2847`
>

---

#### Generate Distribution

ルートファイル構造から配布ファイルを生成する。

`generate distro [name]`

Arguments:
* `name` ディストリビューションファイルの名前。
  * OPTIONAL (default: `distribution`)

Options:

* `--installLocal` 生成された配布物のコピーを、アプリケーションがHeliosのデータフォルダにインストールするようにします。
  * OPTIONAL (デフォルト: false)
  * これは、Heliosのdevモードで、新しいdistribution.jsonを簡単にテストするのに便利です。
  * Tip: Tip: このオプションを使うときは、nameに `dev_distribution` をセットしてください。
* `--discardOutput` キャッシュされた出力が不要になったら、それを削除します。ディスクスペースが限られている場合に便利です。
  * OPTIONAL (デフォルト: false)
OPTIONAL (default: false) * `--invalidateCache` 既存のキャッシュを無効にして削除します。新しいキャッシュの生成が必要です。
  * OPTIONAL (デフォルト: false)

#### Notes

Forge 1.13 では、必要なファイルを生成するためにインストーラーを実行する必要があります。インストーラの出力はデフォルトでキャッシュされます。これは以降のビルドを高速化し、Nebula を CI ジョブとして実行できるようにするためのものです。インストーラの出力を破棄する (キャッシュしない) オプションと、キャッシュを無効にする (キャッシュされた出力を削除し、新しい生成を要求する) オプションが用意されています。単一のバージョンキャッシュのみを無効にするには、キャッシュされたフォルダを手動で削除します。

>
> Example Usage
>
> `generate distro`
>
> `generate distro dev_distribution --installLocal`
>

---

#### Generate Schemas

Nebula の内部型(ex. Distro Meta と Server Meta スキーマ)で使用される JSON スキーマを生成する。このコマンドは、Nebula の変更に伴いスキーマの更新が必要になった場合に使用する必要があります。新しいJSONスキーマを有効にするために、エディタを再度開く必要があるかもしれません。

`generate schemas`

---

### Latest Forge

Forgeの最新バージョンを入手する。

`latest-forge <version>`

---

### Recommended Forge

Forge の推奨バージョンを取得します。推奨ビルドがない場合は、最新バージョンを取得します。

`recommended-forge <version>`

---

## File Structure Setup (Tentative)

Nebulaは、ファイルを保存するための情報を保持する構造をユーザーに提供することを目的としています。このアプリケーションは、この構造を利用して、HeliosLauncherの完全なdistribution.jsonを生成します。一貫性を保つために、ディストリビューション構造はモジュール化され、ディレクトリパターンによってカプセル化されています。これらのカプセル化については、以下で説明します。これらは手動で生成することもできますし、上記のコマンドを使用して生成することもできます。

### Distribution Encapsulation

配布オブジェクトは、メインルートで表されます。すべてのコマンド出力はこのディレクトリに保存されます。その構造は以下の通りです。

例.

* `TestRoot` ディストリビューションをカプセル化したルートディレクトリです。
  * `servers` すべてのサーバーファイルはこのディレクトリに格納されます。

### Server Encapsulation

サーバーオブジェクトは、それぞれのフォルダーにカプセル化されています。サーバーのフォルダー名には、そのidとバージョンの両方が含まれています。

例.

* `servers`
  * `TestServer-1.12.2` A server with id TestServer set to version 1.12.2.

serverディレクトリには、そのサーバーに関連するファイルが格納されます。

Ex.

* `TestServer-1.12.2`.
  * `files` `File` 型のすべてのモジュール。
  * `libraries` `Library` タイプの全てのモジュール。
  * `forgemods` `ForgeMod` タイプの全てのモジュール。
    * これはトグル可能なモジュールのディレクトリです。以下の注釈を参照してください。
  * `TestServer-1.12.2.png` サーバーアイコンファイルです。

#### Toggleable Modules

もしディレクトリがトグル可能なMODを表すなら、それは3つのサブディレクトリを持つことになります。この3つにファイルをフィルタリングする必要があります。

* `required` 必須のモジュールです。
* `optionalon` オプションのモジュールで、デフォルトで有効になります。
* `optionaloff` オプションのモジュールで、デフォルトで無効化されます。

### Additional Metadata

ファイル構造から推測できないメタデータを保持するために、2つのファイルが存在する。デフォルト値は、該当する場合に生成されます。ニーズに合わせてカスタマイズしてください。これらの値は自明なはずです。さらに詳細が必要な場合は、[distribution.json仕様書][distro.md]を参照してください。

#### ${ROOT}/meta/distrometa.json

ディストリビューションオブジェクトに追加されたメタデータを表します。

このファイルを編集するためのJSONスキーマが提供されています。デフォルトのファイルが生成される際に、自動的に参照されるはずです。

Sample:

```json
{
  "$schema": "file:///${ROOT}/schemas/DistroMetaSchema.schema.json",
  "meta": {
      "rss": "<LINK TO RSS FEED>",
      "discord": {
          "clientId": "<FILL IN OR REMOVE DISCORD OBJECT>",
          "smallImageText": "<FILL IN OR REMOVE DISCORD OBJECT>",
          "smallImageKey": "<FILL IN OR REMOVE DISCORD OBJECT>"
      }
  }
}
```

#### servers/${YOUR_SERVER}/servermeta.json

サーバーオブジェクトの追加メタデータを表します (YOUR_SERVERの場合)。

このファイルの編集を支援するために、JSON スキーマが提供されています。このスキーマは、デフォルトのファイルが生成される際に自動的に参照されるはずです。

Sample:

```json
{
  "$schema": "file:///${ROOT}/schemas/ServerMetaSchema.schema.json",
  "meta": {
    "version": "1.0.0",
    "name": "Test (Minecraft 1.12.2)",
    "description": "Test Running Minecraft 1.12.2 (Forge v14.23.5.2854)",
    "address": "localhost:25565",
    "discord": {
      "shortId": "1.12.2 Test Server",
      "largeImageText": "1.12.2 Test Server",
      "largeImageKey": "seal-circle"
    },
    "mainServer": false,
    "autoconnect": true
  },
  "forge": {
    "version": "14.23.5.2854"
  },
  "untrackedFiles": []
}
```

#### Untracked Files

未トラックのファイルはオプションです。MD5 ハッシュは、指定されたグロブパターンに一致するファイルには生成されません。ランチャーは、MD5ハッシュのないファイルの検証/更新を行いません。

```json
{
  "untrackedFiles": [
    {
      "appliesTo": ["files"],
      "patterns": [
        "config/*.cfg",
        "config/**/*.yml"
      ]
    }
  ]
}
```

上記の例では、config ディレクトリにある `cfg` タイプのファイルはすべてアントラックされます。さらに、config ディレクトリとそのサブディレクトリにある `yml` タイプのファイルはすべて追跡されなくなります。これらのパターンはあなたのニーズに合わせて調整することができます。パターンは `appliesTo` で指定されたフォルダーにのみ適用されます。例として、有効な値は `files`、`forgemods`、`libraries` などである。

```json
{
  "untrackedFiles": [
    {
      "appliesTo": ["files"],
      "patterns": [
        "config/*.cfg",
        "config/**/*.yml"
      ]
    },
    {
      "appliesTo": ["forgemods"],
      "patterns": [
        "optionalon/*.jar"
      ]
    }
  ]
}
```

もう一つの例は、すべての `optionalon` forgemods がアントラックされている場合です。**Untracking mods is NOT recommended. これはあくまで例です。

### Note on JSON Schemas

JSONファイルの `$schema` プロパティは、JSONスキーマファイルへのURLです。このプロパティはオプションです。NebulaはJSONの編集を容易にするために、内部型のためのスキーマを提供します。Visual Studio Code などのエディタでは、このスキーマファイルを用いてデータの検証を行い、プロパティの説明などの有用な情報を表示します。また、有効なプロパティはオートコンプリートされます。詳細については、[JSON Schema Website](jsonschemawebsite)を参照してください。

Nebula は JSON スキーマを `${ROOT}/schemas` に格納する。これは、あなたのローカルバージョンの Nebula と常に同期するためである。スキーマは初期状態では `init root` コマンドで生成される。スキーマを更新するには、 `generate schemas` コマンドを実行する。


[dotenvnpm]: https://www.npmjs.com/package/dotenv
[distro.md]: https://github.com/dscalzi/HeliosLauncher/blob/master/docs/distro.md
[jsonschemawebsite]: https://json-schema.org/
