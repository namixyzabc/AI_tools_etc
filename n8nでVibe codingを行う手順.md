
---

### n8n MCP Server ガイド

このドキュメントは、AIアシスタントが自然言語（話し言葉）でn8nワークフローを操作できるようにするツール「n8n MCP Server」について、分かりやすく解説したものです。

#### 1. これは何？ 何ができる？
一言でいうと、**AIアシスタントとn8nを繋ぐ「通訳」サーバー**です。

*   **目的**: AIアシスタント（例: VS CodeのClaude拡張機能など）から、「〇〇のワークフローを実行して」といった自然な言葉で指示を出すだけで、n8nのワークフローを動かせるようになります。
*   **仕組み**: このサーバーがAIからの指示を受け取り、n8nが理解できる命令に変換してくれます。

---

### 2. 導入方法（インストールと設定）

インストール方法は3種類あります。ご自身のスキルや環境に合わせて選んでください。
どの方法を選んでも、後の設定手順は共通です。

#### 事前準備
まず、以下の2つをご用意ください。
1.  **Node.js**: バージョン20以降
2.  **n8nインスタンス**: APIアクセスが有効になっているもの

---

#### ▼ インストール方法を選ぶ

##### 方法A：npmで手軽にインストール（一番かんたん・推奨）
すぐに使い始めたい方向けです。

*   **インストール手順**
    1.  ターミナル（コマンドプロンプト）で以下のコマンドを実行します。
        ```bash
        npm install -g @leonardsellem/n8n-mcp-server
        ```

*   **アップデート手順**
    1.  最新版に更新するには、以下のコマンドを実行します。
        ```bash
        npm install -g @leonardsellem/n8n-mcp-server@latest
        ```
    2.  サーバーが起動している場合は、再起動してください。

##### 方法B：ソースコードからインストール（開発者向け）
自分でコードをカスタマイズしたい方向けです。

*   **インストール手順**
    1.  リポジトリをクローン（コピー）して、そのフォルダに移動します。
        ```bash
        git clone https://github.com/leonardsellem/n8n-mcp-server.git
        cd n8n-mcp-server
        ```
    2.  必要なライブラリをインストールし、プロジェクトをビルド（実行可能な形式に変換）します。
        ```bash
        npm install
        npm run build
        ```
    3.  （任意）PCのどこからでもコマンドを実行できるように、グローバルインストールします。
        ```bash
        npm install -g .
        ```

*   **アップデート手順**
    1.  プロジェクトのフォルダに移動し、最新のソースコードを取得します。
        ```bash
        cd path/to/n8n-mcp-server
        git pull origin main
        ```
    2.  変更されたライブラリを更新し、再度ビルドします。
        ```bash
        npm install
        npm run build
        ```
    3.  （任意）グローバルインストールを更新します。
        ```bash
        npm install -g .
        ```
    4.  サーバーを再起動してください。

##### 方法C：Dockerで利用する
PCの環境を汚さずに、隔離された環境で実行したい方向けです。

*   **インストール（実行）手順**
    1.  最新のDockerイメージを取得します。
        ```bash
        docker pull leonardsellem/n8n-mcp-server
        ```
    2.  ご自身のn8n環境に合わせて、環境変数を設定してコンテナを起動します。
        ```bash
        docker run -e N8N_API_URL=http://your-n8n:5678/api/v1 \
          -e N8N_API_KEY=your_n8n_api_key \
          -e N8N_WEBHOOK_USERNAME=username \
          -e N8N_WEBHOOK_PASSWORD=password \
          leonardsellem/n8n-mcp-server
        ```

*   **アップデート手順**
    1.  最新のイメージを取得します。
        ```bash
        docker pull leonardsellem/n8n-mcp-server:latest
        ```
    2.  古いコンテナを停止・削除します。（コンテナ名は `docker ps` で確認できます）
        ```bash
        docker stop <コンテナ名 or ID>
        docker rm <コンテナ名 or ID>
        ```
    3.  新しいイメージを使って、再度コンテナを起動します（上記のインストール（実行）手順と同じコマンドです）。

---

#### ▼ 共通の設定手順

インストール後、サーバーをn8nに接続するための設定を行います。

1.  **n8nでAPIキーを発行する**
    1.  n8nの管理画面を開きます。
    2.  `Settings > API > API Keys` に移動します。
    3.  新しいAPIキーを作成し、コピーしておきます。

2.  **環境変数を設定する**
    サーバーにAPIキーなどの情報を教える設定です。
    `ソースコード`からインストールした場合は、`.env.example` をコピーして `.env` ファイルを作成し、編集します。
    `Docker`や`AIアシスタント連携`で設定する場合は、それぞれの設定項目に以下の値を入力します。

| 変数名 | 説明 | 設定例 |
| :--- | :--- | :--- |
| **`N8N_API_URL`** | n8nのAPIのURL（末尾に `/api/v1` が必要） | `http://localhost:5678/api/v1` |
| **`N8N_API_KEY`** | 先ほどコピーしたAPIキー | `n8n_api_...` |
| `N8N_WEBHOOK_USERNAME` | （Webhook機能を使う場合）認証用ユーザー名 | `username` |
| `N8N_WEBHOOK_PASSWORD` | （Webhook機能を使う場合）認証用パスワード | `password` |

---

### 3. 使い方

#### サーバーの起動
*   **npm**または**ソースコード**でグローバルインストールした場合:
    ```bash
    n8n-mcp-server
    ```
*   それ以外の場合:
    ソースコードをインストールしたフォルダ内で `npm run build` を実行した後、`node build/index.js` で起動します。

#### AIアシスタントとの連携
VS CodeなどのAIアシスタントに、このサーバーの場所を教える設定です。
アシスタントの設定ファイル（例: `settings.json`）に、以下のように記述します。

```json
{
  "mcpServers": {
    "n8n-local": {
      // サーバーの起動コマンドを指定
      "command": "node",
      // サーバーの実行ファイルの「絶対パス」を指定
      "args": [
        // Mac/Linuxの場合
        "/path/to/your/cloned/n8n-mcp-server/build/index.js"
        // Windowsの場合はバックスラッシュを2つ重ねる
        // "C:\\path\\to\\your\\cloned\\n8n-mcp-server\\build\\index.js"
      ],
      // 環境変数をここで設定することも可能
      "env": {
        "N8N_API_URL": "http://your-n8n-instance:5678/api/v1",
        "N8N_API_KEY": "YOUR_N8N_API_KEY"
      },
      "disabled": false
    }
  }
}
```
**【重要】**
*   `args`には、`npm run build`で作成された`index.js`ファイルの**絶対パス**を正しく指定してください。
*   `env`で設定するか、`.env`ファイルを使うか、どちらかの方法で環境変数を設定してください。

---

### 4. 何ができるか？ (主な機能)

このサーバーを介して、AIアシスタントは以下の操作を行えるようになります。

*   **ワークフロー管理**:
    *   一覧表示、詳細取得、作成、更新、削除、有効化/無効化
*   **実行管理**:
    *   ワークフローの実行、実行の停止、実行履歴の取得
*   **Webhook経由での実行**:
    *   n8nでWebhookトリガー（Basic認証付き）を設定しておけば、AIから安全にワークフローをキックできます。
