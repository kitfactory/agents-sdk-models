# 実装TODO

- [x] AnthropicModel の async テストを作成する
- [x] GeminiModel の async テストを作成する
- [x] OllamaModel のテストを作成する
- [x] AgentPipeline の `_build_generation_prompt` メソッドのテストを作成する
- [x] AgentPipeline の `_build_evaluation_prompt` メソッドのテストを作成する
- [x] 評価が閾値未満の場合の `improvements_callback` の動作テストを作成する
- [x] ガードレール機能（Guardrails）統合テストを作成する
- [x] ツール統合のテストを作成する
- [x] ダイナミックプロンプト機能のテストを作成する
- [x] ルーティング機能のテストを作成する
- [x] コードカバレッジを90%以上に向上させる
- [x] examples フォルダ内のスクリプトを動作検証する
- [x] docs フォルダ内のドキュメントの整合性を確認・更新する

## docs/ APIリファレンス & チュートリアル構築（MkDocs Material）
- [x] MkDocs Material をプロジェクトに開発環境に導入する
- [x] mkdocs.yml 設定ファイルを作成し、サイト構成を設計する
- [x] docs/index.md にプロジェクト概要・導入方法を記載する
- [x] docs/api_reference.md に主要クラス・関数のAPIリファレンスを記載する
- [x] docs/tutorials/ ディレクトリを作成し、チュートリアル記事（例：クイックスタート、応用例）を追加する
- [x] コードブロックや図（PlantUML等）を活用し、分かりやすいドキュメントにする
- [x] mkdocs serve でローカルプレビューし、表示・リンク切れ等を確認する
- [x] 完成したらGitにコミット・プッシュする

## 粒度の確認

- 上記のステップは対象ファイルと対象機能が明確であり、現状の粒度で十分です。

# TODO

- [x] OpenAI Agents SDK標準Tracerの無効化方法を調査する
- [x] set_tracing_disabledの挙動を確認し、ソースコード上で依存箇所を特定する
- [x] 独自TracingProcessorに必要な要件を整理する
- [x] 独自TracingProcessorのインターフェース設計を行う
  - [x] トレース入力フォーマットを定義する
  - [x] トレース出力フォーマットを定義する
- [x] 独自TracingProcessorの機能クラスを実装する
- [x] 独自TracingProcessorのテストを作成し、機能を検証する
- [x] set_tracing_disabledと独自TracingProcessorを組み合わせて動作確認を行う
- [x] トレース表示の簡易UI/CLIコンポーネントを設計・実装する
- [x] pytestを実行し、すべてのテストがパスすることを確認する
- [x] 全体の動作テストを実行し、問題がないことを確認する

## 粒度確認
各ステップは単一責務に基づき、末端機能のテスト作成→実装→結合テストの順で実行可能な粒度に細分化しています。

## 調査結果
- `set_tracing_disabled`はGLOBAL_TRACE_PROVIDER.set_disabledを呼び出し、トレースの有効/無効を切り替える。
- デフォルトTraceProcessor(`default_processor`)はimport時にGLOBAL_TRACE_PROVIDERに登録されている。
- 標準Tracerを無効化し、独自Processorを使用するには`set_trace_processors`でProcessorリストを置き換える必要がある。
- 依存箇所: `src/agents_sdk_models/llm.py`、examplesディレクトリ内の各スクリプト。

## 要件整理
- `on_trace_start`, `on_trace_end`, `on_span_start`, `on_span_end`, `shutdown`, `force_flush`のすべてのメソッドを実装すること
- デフォルトProcessor(`default_processor`)への依存を排除し、OpenAIへの通信を行わないこと
- コンソールやファイルなど、出力先を設定可能にすること
- トレースおよびスパン情報を開始時刻、終了時刻、ID、親子関係を含む読みやすいフォーマットで出力すること
- 例外を投げず、安全に動作すること
- アプリケーション終了時に`force_flush`を必ず呼び出すこと
- 並列実行や高頻度呼び出しに耐えうる性能を確保すること
- テスト可能性を考慮し、出力を検証できること
- 他の機能に影響を与えない設計とすること

## 次フェーズ: TracingProcessor拡張タスク
- [x] `todo.md`に計画をチェックリスト形式で追加・更新し、タスクの進捗を管理する
- [x] SDKの`llm.get_llm`で`set_tracing_disabled`を呼び出し、依存箇所を特定する
- [x] `TracingProcessor`インターフェースを確認し、独自Processorの要件を整理する
- [x] `src/agents_sdk_models/tracing_processor.py`に`ConsoleTracingProcessor`スケルトンを作成する
- [x] クラスドキュメントにトレース／スパンの入出力フォーマットを定義する
- [x] `ConsoleTracingProcessor`に`on_trace_start`、`on_trace_end`、`on_span_start`、`on_span_end`、`shutdown`、`force_flush`を実装する
- [x] テスト（`tests/test_tracing_processor.py`、`tests/test_tracing_integration.py`）を作成し、正常動作および`set_tracing_disabled`時の無効化を検証する
- [x] `pyproject.toml`のTOML構文エラー（`Bug Tracker`キー）を修正する
- [x] `examples/simple_llm_query.py`と`examples/pipeline_trace_example.py`を追加する
- [x] CLIコンポーネント`examples/trace_cli.py`を実装する
- [x] `ConsoleTracingProcessor`に`simple_mode`を追加し、ANSIカラーで`SPAN START`、`Input`、`Output`、`SPAN END`を表示する
- [x] ユーザー入力やプロンプト、実際のLLMレスポンス本文のみをカラー出力するよう改良する
- [x] `PipelineTracingProcessor`を定義し、`Runner.run_sync`をモンキーパッチしてプロンプトを捕捉し、生成と評価のinstruction、prompt、outputを一貫して表示する
- [x] `PipelineTracingProcessor`の`on_trace_start`/`on_trace_end`で単一のSpanにまとめて色分け表示する方法を示唆する

- [ ] ConsoleTracingProcessorの最終レビュー
  - [ ] ANSIカラー表示の動作検証（Span開始・終了、Instruction/Prompt/Output）
  - [ ] コードコメント・ドキュメントの整備
- [ ] PipelineTracingProcessorの重複表示防止ロジックのテスト作成
  - [ ] 正常系テストケースの追加
  - [ ] エッジケース（カスタムスパン含む）の動作確認
- [ ] DialogProcessorのユニットテスト実装
  - [ ] モックAgentPipelineを用いた入力・出力検証
  - [ ] 異常系テスト（入力なし、空リストなど）の作成
- [ ] examplesディレクトリのスクリプト動作確認
  - [ ] simple_llm_query.pyの動作検証
  - [ ] 他例スクリプトの動作検証
- [ ] docsフォルダにドキュメント作成
  - [ ] 要件定義書(requirements.md)の作成
  - [ ] アーキテクチャ設計書(architecture.md)の作成
  - [ ] 機能仕様書(function_spec.md)の作成
- [ ] README.mdとREADME_ja.mdの作成
  - [ ] README.md（英語、絵文字・サポート環境・メリット訴求）
  - [ ] README_ja.md（日本語のみ）
- [ ] pyproject.tomlの依存確認と調整
- [ ] 仮想環境(.venv)下でpytest実行による全体動作確認 