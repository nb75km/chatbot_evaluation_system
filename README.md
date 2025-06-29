# chatbot_evaluation_system# チャットボット評価システム

## 1. プロジェクトの目的 (Project Purpose)

本プロジェクトの主な目的は、特にRAG (Retrieval Augmented Generation) システムを採用したチャットボットの性能を、効率的かつ体系的に評価・比較するための総合的なプラットフォームを構築することです。

具体的には以下の達成を目指します。

* **客観的評価の実現**: 事前に定義された統一的なクエリセットを用い、複数のチャットボットの回答を生成させます。
* **性能比較の容易化**: 異なるチャットボット、または同一チャットボットで参照データを変更した場合の性能差を、直感的なGUIを通じて横並びで比較・評価できるようにします。
* **データドリブンな改善支援**: 人間による評価（正確性に基づく5段階スコア、コメント）をデータベースに記録し、蓄積します。
* **傾向分析の提供**: 記録された評価データを基に、チャットボットごとの平均スコアなどの基本的な分析機能を提供し、性能傾向の把握を助けます。
* **開発サイクルの加速**: 上記を通じて、チャットボットの課題発見と改善サイクルの迅速化に貢献します。

## 2. プロジェクトのルール・ガイドライン (Project Rules & Guidelines)

本プロジェクトを円滑に進め、品質を維持するために、以下のルールとガイドラインを定めます。

### 2.1 バージョン管理 (Version Control)

* **Git**: すべてのソースコード、設定ファイル、ドキュメントはGitでバージョン管理します。
* **ブランチ戦略**: 基本的に以下のブランチモデルを採用します（プロジェクト規模により調整）。
    * `main` (または `master`): 本番リリース可能な安定バージョン。直接コミットは禁止。
    * `develop`: 次期リリースのための開発ブランチ。フィーチャーブランチはここから作成し、ここにマージします。
    * `feature/<feature-name>`: 新機能開発やタスクごとの作業ブランチ。 (例: `feature/evaluation-gui`)
    * `fix/<issue-id>`: バグ修正用のブランチ。
    * `hotfix/<issue-id>`: 緊急性の高い本番バグ修正用のブランチ (`main` から分岐)。
* **コミットメッセージ**: Conventional Commits 形式を推奨します。
    * 例: `feat: 評価結果保存APIエンドポイントを追加`
    * 例: `fix: クエリ選択時のUI表示不具合を修正`
    * 例: `docs: READMEのセットアップ手順を更新`
    * 参考: [Conventional Commits](https://www.conventionalcommits.org/)
* **プルリクエスト (Merge Request)**:
    * `develop` ブランチ (または `main` ブランチ) へのマージは、必ずプルリクエスト経由で行います。
    * プルリクエストには、変更内容の概要、関連するIssue番号などを記載します。
    * 最低1名以上のレビュアーによる承認を必須とします。

### 2.2 開発環境 (Development Environment)

* **Docker / Docker Compose**: 標準の開発・実行環境としてDockerおよびDocker Composeを使用します。
    * プロジェクトルートの `docker-compose.yml` (基本構成) と `docker-compose.override.yml` (ローカル開発用上書き設定) を使用して環境を起動します。
    * 開発に必要なサービス (FastAPI, Streamlit, MySQL/SQLite) はすべてDockerコンテナ内で実行されます。
* **環境変数**:
    * データベースのパスワードやAPIキーなどの機密情報・環境依存設定は、プロジェクトルートの `.env` ファイルに記述します。
    * `.env` ファイルはGit管理対象外とします (`.gitignore` に記載)。
    * 設定テンプレートとして `.env.example` を提供します。

### 2.3 コーディング規約 (Coding Standards)

* **Python**:
    * [PEP 8](https://www.python.org/dev/peps/pep-0008/) に準拠します。
    * コードフォーマッター: **Black** を使用し、自動整形を推奨します。
    * リンター: **Flake8** を使用し、潜在的なエラーやスタイル違反をチェックします。
    * インポート順序: **isort** を使用し、インポート文を整理します。
    * これらのツールは、ローカルでのコミット前やCIパイプラインで実行します。
* **一般**:
    * 可読性・保守性の高いコードを心がけます。
    * 複雑なロジックや意図が伝わりにくい箇所には、適切なコメントを記述します。
    * ハードコーディングを避け、設定値は設定ファイル (`configs/app_config.yaml`) や環境変数から読み込むようにします。
* **エラーハンドリング**:
    * 適切なエラーハンドリングを行い、予期せぬエラーが発生した場合でもアプリケーションがクラッシュしないようにします。
    * エラー発生時には、原因究明に役立つ情報をログに出力します。

### 2.4 テスト (Testing)

* **ユニットテスト**: 新規機能の実装やバグ修正時には、関連するロジックのユニットテストを作成します。
    * FastAPIのビジネスロジック、Streamlitのユーティリティ関数などが対象です。
* **インテグレーションテスト**: 重要なコンポーネント間の連携（例: FastAPIとデータベース、FastAPIと外部チャットボットAPI）については、インテグレーションテストを作成します。
* **テストフレームワーク**: **Pytest** を使用します。
* **テスト実行**: テストはDockerコンテナ内で実行できるようにします。
    * 例: `docker-compose exec fastapi_server pytest`
* **テストカバレッジ**: 主要な機能については、十分なテストカバレッジを目指します。
* CIパイプラインで自動テストを実行します。

### 2.5 設定管理 (Configuration Management)

* アプリケーションの動作設定 (チャットボット定義、クエリファイルのパスなど) は `configs/app_config.yaml` で一元管理します。
* 機密情報や環境固有の設定は `.env` ファイルと環境変数で管理します。

### 2.6 データベース管理 (Database Management)

* データベーススキーマの変更は **Alembic** によるマイグレーションで行います。
* 新しいマイグレーションスクリプトは、`alembic revision -m "変更内容の概要" --autogenerate` で自動生成し、必要に応じて手動で調整します。
* 生成されたマイグレーションスクリプトは必ずレビューし、テスト環境で動作確認を行ってから `develop` ブランチにマージします。
* `alembic upgrade head` で最新のスキーマを適用します。

### 2.7 依存関係管理 (Dependencies Management)

* Pythonの依存ライブラリは、各サービス（`streamlit_app`, `fastapi_server`）の `requirements.txt` に記載します。
* バージョンは原則として固定 (`==`) し、意図しないアップデートによる問題を避けます。
* ライブラリの追加・更新時は、`requirements.txt` を更新し、変更理由をコミットメッセージやプルリクエストに明記します。

### 2.8 ドキュメンテーション (Documentation)

* **README.md (本ファイル)**: プロジェクトの概要、目的、セットアップ方法、開発ルールなどを記載し、常に最新の状態を保ちます。
* **コードコメント**: 関数やクラス、複雑なロジックには、その目的や動作を説明するコメントを適切に記述します。
* **APIドキュメント**: FastAPIの自動生成ドキュメント (`/docs`, `/redoc`) を活用し、API仕様を明確にします。Pydanticモデルによるスキーマ定義を丁寧に行います。
* **設定ファイル**: `app_config.yaml` や `.env.example` には、各設定項目の意味や設定可能な値についてのコメントを記述します。
* 新しい機能や重要な変更については、関連ドキュメントも合わせて更新します。

### 2.9 コミュニケーション・連携 (Communication & Collaboration)

* **Issueトラッキング**: GitHub Issues, Jiraなどのタスク管理ツールを使用して、タスク、バグ、要望を管理します。
* **定期的な情報共有**: 必要に応じてミーティングや朝会などで進捗や課題を共有します。
* **明確なコミュニケーション**: 疑問点や問題点は早めに報告・相談し、チーム内での認識齟齬を防ぎます。

---

この `README.md` は、プロジェクトの進行に合わせて適宜更新してください。
他に「セットアップ手順」「実行方法」「技術詳細」「トラブルシューティング」などのセクションを追加していくと、より充実したドキュメントになります。
