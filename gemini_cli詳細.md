
### Gemini CLI 設定ガイド

Gemini CLIの動作を設定するための様々な方法（環境変数、コマンドライン引数、設定ファイルなど）について説明します。

| 項目 | 説明 |
| :--- | :--- |
| **設定の優先順位** | 設定は以下の順序で適用されます（番号が小さいものは大きいものによって上書きされます）。<br>1. **デフォルト値**: アプリケーションにハードコードされたデフォルト。<br>2. **ユーザー設定ファイル**: 現在のユーザー向けのグローバル設定。<br>3. **プロジェクト設定ファイル**: プロジェクト固有の設定。<br>4. **システム設定ファイル**: システム全体の設定。<br>5. **環境変数**: システム全体またはセッション固有の変数（.envファイルから読み込まれる可能性あり）。<br>6. **コマンドライン引数**: CLI起動時に渡される値。 |
| **設定ファイル** | Gemini CLIは、永続的な設定のために`settings.json`ファイルを使用します。これには3つの場所があります。<br><ul><li>**ユーザー設定ファイル**: `~/.gemini/settings.json`にあり、現在のユーザーのすべてのGemini CLIセッションに適用されます。</li><li>**プロジェクト設定ファイル**: プロジェクトのルートディレクトリ内の`.gemini/settings.json`にあり、そのプロジェクトからCLIを実行する場合にのみ適用されます。ユーザー設定を上書きします。</li><li>**システム設定ファイル**: OSごとに指定された場所にあり（`GEMINI_CLI_SYSTEM_SETTINGS_PATH`環境変数で上書き可能）、システム上の全ユーザーの全セッションに適用されます。ユーザー設定やプロジェクト設定を上書きします。</li></ul>**注**: `settings.json`ファイル内の文字列値は、`$VAR_NAME`または`${VAR_NAME}`構文を使用して環境変数を参照できます。 |
| **プロジェクトの`.gemini`ディレクトリ** | プロジェクト設定ファイルに加えて、このディレクトリにはカスタムサンドボックスプロファイルなど、プロジェクト固有のファイルを含めることができます。 |

---

### `settings.json`で利用可能な設定

| 設定名 | 説明 | デフォルト値 |
| :--- | :--- | :--- |
| `contextFileName` | コンテキストファイル（例：GEMINI.md）のファイル名を指定します。単一のファイル名またはファイル名のリストが可能です。 | `GEMINI.md` |
| `bugCommand` | `/bug`コマンドのデフォルトURLを上書きします。 | `{ "urlTemplate": "https://..." }` |
| `fileFiltering` | `@`コマンドやファイル検出ツールにおける、gitを意識したファイルフィルタリングの動作を制御します。`.gitignore`を尊重するかどうかなどを設定できます。 | `respectGitIgnore: true`, `enableRecursiveFileSearch: true` |
| `coreTools` | モデルで利用可能にすべきコアツール名のリストを指定します。組み込みツールセットを制限するために使用できます。 | 全てのツールが利用可能 |
| `excludeTools` | モデルから除外すべきコアツール名のリストを指定します。`coreTools`と両方にリストされているツールは除外されます。 | 除外されるツールなし |
| `allowMCPServers` | モデルで利用可能にすべきMCPサーバー名のリストを指定します。 | 全てのMCPサーバーが利用可能 |
| `excludeMCPServers` | モデルから除外すべきMCPサーバー名のリストを指定します。 | 除外されるMCPサーバーなし |
| `autoAccept` | 安全と見なされるツールコール（読み取り専用操作など）をユーザーの確認なしで自動的に受け入れて実行するかどうかを制御します。 | `false` "autoAccept": true|
| `theme` | Gemini CLIの表示テーマを設定します。 | `"Default"` |
| `vimMode` | 入力編集のためのvimモードを有効または無効にします。 | `false` |
| `sandbox` | ツール実行のためのサンドボックス利用を制御します。`true`に設定すると、事前にビルドされたDockerイメージを使用します。 | `false` |
| `toolDiscoveryCommand` | プロジェクトからツールを発見するためのカスタムシェルコマンドを定義します。 | 空 |
| `toolCallCommand` | `toolDiscoveryCommand`で発見された特定のツールを呼び出すためのカスタムシェルコマンドを定義します。 | 空 |
| `mcpServers` | カスタムツールを発見・使用するためのModel-Context Protocol (MCP)サーバーへの接続を設定します。 | 空 |
| `checkpointing` | 会話とファイルの状態を保存・復元できるチェックポイント機能を設定します。 | `{"enabled": false}` |
| `preferredEditor` | 差分表示に使用する優先エディタを指定します。 | `vscode` |
| `telemetry` | Gemini CLIのロギングとメトリクス収集を設定します。 | `{"enabled": false, ...}` |
| `usageStatisticsEnabled` | 使用状況統計の収集を有効または無効にします。 | `true` |
| `hideTips` | CLIインターフェースのヒントを非表示にします。 | `false` |
| `hideBanner` | CLIインターフェースの起動バナーを非表示にします。 | `false` |
| `maxSessionTurns` | セッションの最大ターン数を設定します。 | `-1` (無制限) |
| `summarizeToolOutput` | ツール出力の要約を有効または無効にします。 | `{}` (無効) |

---

| 項目 | 説明 |
| :--- | :--- |
| **シェル履歴** | CLIは実行したシェルコマンドの履歴を保持します。履歴はプロジェクト固有のディレクトリ `~/.gemini/tmp/<project_hash>/shell_history` に保存されます。 |
| **環境変数と`.env`ファイル** | `.env`ファイルは作業ディレクトリから親ディレクトリを遡って検索され、自動的に読み込まれます。主な変数は以下の通りです。<br><ul><li>`GEMINI_API_KEY` **(必須)**: Gemini APIのAPIキー。</li><li>`GEMINI_MODEL`: デフォルトで使用するGeminiモデルを指定します。</li><li>`GOOGLE_API_KEY`: Google CloudのAPIキー。</li><li>`GOOGLE_CLOUD_PROJECT`: Google CloudのプロジェクトID。</li><li>`GOOGLE_APPLICATION_CREDENTIALS`: Google Application Credentials JSONファイルへのパス。</li><li>その他、`OTLP_GOOGLE_CLOUD_PROJECT`, `GOOGLE_CLOUD_LOCATION`, `GEMINI_SANDBOX`など多数。</li></ul> |
| **コマンドライン引数** | CLI実行時に直接渡される引数で、他の設定をそのセッションに限り上書きします。<br><ul><li>`--model <model_name>` (`-m`): 使用するGeminiモデルを指定。</li><li>`--prompt <your_prompt>` (`-p`): プロンプトを直接渡して非対話モードで実行。</li><li>`--sandbox` (`-s`): サンドボックスモードを有効化。</li><li>`--debug` (`-d`): デバッグモードを有効化。</li><li>`--yolo`: 全てのツールコールを自動承認するYOLOモードを有効化。</li><li>その他、`--help`, `--version`など多数。</li></ul> |
| **コンテキストファイル** | `GEMINI.md`（設定で変更可能）などのMarkdownファイルで、Geminiモデルに提供する指示的コンテキスト（メモリ）を設定します。プロジェクト固有の指示やコーディングスタイルなどをAIに与えることで、応答をよりニーズに合わせることができます。ファイルはグローバル、プロジェクトルート、サブディレクトリから階層的に読み込まれます。`/memory`コマンドで管理できます。 |
| **サンドボックス** | 潜在的に危険な操作（シェルコマンドやファイル変更など）をサンドボックス環境内で実行し、システムを保護します。デフォルトでは無効ですが、`--sandbox`フラグなどで有効にできます。プロジェクト固有のサンドボックスとして、`.gemini/sandbox.Dockerfile`を配置することも可能です。 |
| **使用状況統計** | Gemini CLIの改善のため、匿名化された使用状況統計が収集されます。収集されるのは、ツールコールの名前や成否、APIリクエストの情報などです。プロンプト内容や個人を特定できる情報（PII）は収集されません。`settings.json`で`"usageStatisticsEnabled": false`と設定することでオプトアウトできます。 |
