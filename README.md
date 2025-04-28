# 情報収集アプリケーション

## 機能概要

- ユーザーがクエリ（例：`UH-1`）を入力し、Wikipediaの情報と信頼性の高いWeb検索結果を取得
- Next.jsを用いたモダンなUIでフォーム入力と「収集」ボタンを提供
- Pywikibotを使用したWikipediaデータの取得（タイトル、要約、URL）
- Google Custom Search JSON APIを使用した信頼性の高いWeb検索結果（最大5件）
- TypeScript + Next.jsによるレスポンシブなフロントエンド
- FastAPIによる高速なバックエンドAPI
- キャッシュ（cachetools）を使用したパフォーマンス最適化
- 信頼性の高いドメイン（例：`.edu`, `.gov`）に限定したWeb検索結果

## プロジェクト構造

```
demo-fastapi-langgraph-knowledge-retriever/
├── backend/
│   ├── app/
│   │   ├── main.py           # FastAPIアプリケーション
│   │   └── models.py         # Pydanticモデル
│   ├── requirements.txt      # Python依存パッケージ
│   └── .env                  # 環境変数（ローカル用）
├── frontend/
│   ├── components/
│   │   └── CollectForm.tsx   # フォームコンポーネント
│   ├── pages/
│   │   ├── index.tsx         # メインページ
│   ├── styles/               # Tailwind CSS
│   ├── public/               # 静的アセット
│   ├── package.json          # フロントエンド依存関係
│   └── tsconfig.json         # TypeScript設定
└── README.md
```

## セットアップ

### 環境変数の設定

- `backend/.env`ファイルを作成し、以下の環境変数を設定してください：

- Google Custom Search API設定

```.env
GOOGLE_API_KEY=your_google_api_key
SEARCH_ENGINE_ID=your_search_engine_id
```

### バックエンド

```bash
cd backend
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate
pip install -r requirements.txt
uvicorn app.main:app --reload --port 8000
```

### フロントエンド

```bash
cd frontend
npm install
npm run dev
```

## Google Custom Search API設定

- Google Cloud Consoleでプロジェクトを作成または選択
- 「APIとサービス」→「ライブラリ」で「Custom Search API」を有効化
- 「認証情報」→「APIキー」を作成し、.envに設定
- https://cse.google.com/cse/all で検索エンジンを作成し、検索エンジンIDを.envに設定

## 使い方

- バックエンド（ http://localhost:8000 ）とフロントエンド（ http://localhost:3000 ）を起動
- ブラウザでフロントエンド（ http://localhost:3000 ）にアクセス
- フォームにクエリ（例：UH-1）を入力
- 「収集」ボタンをクリック
- Wikipediaの情報（タイトル、要約、URL）とWeb検索結果（タイトル、URL、スニペット）がカード形式で表示


## 技術スタック詳細

### バックエンド

- Python 3.12.9+
- FastAPI: RESTful APIフレームワーク
- Pywikibot: Wikipediaデータ取得ライブラリ
- google-api-python-client: Google Custom Search APIライブラリ
- Pydantic: データバリデーションとシリアライズ
- cachetools: キャッシュ管理
- python-dotenv: 環境変数管理

### フロントエンド

- Next.js 14.x: Reactフレームワーク
- React 18.x: UIライブラリ
- TypeScript: 型安全な開発
- Tailwind CSS: ユーティリティファーストCSSフレームワーク
- ESLint: コード品質管理

## API仕様

## ルートエンドポイント: GET /

### レスポンス

```json
{
  "message": "FastAPI is running"
}
```

## 情報収集: POST /collect

### リクエスト

```json
{
  "query": "UH-1"
}
```

またはフォームデータ：

```
query=UH-1
```

### レスポンス

```json
{
  "query": "UH-1",
  "wikipedia": [
    {
      "title": "Bell UH-1 Iroquois",
      "summary": "The Bell UH-1 Iroquois is a utility military helicopter powered by a single turboshaft engine...",
      "url": "https://en.wikipedia.org/wiki/Bell_UH-1_Iroquois"
    },
  ],
  "web": [
    {
      "title": "Bell UH-1 Iroquois - National Museum of the USAF",
      "url": "https://www.nationalmuseum.af.mil/...",
      "snippet": "The Bell UH-1 Iroquois, commonly known as the Huey..."
    },
  ]
}
```

### エラーレスポンス例

```json
{
  "detail": "クエリが空です"
}
```

## 開発注意事項

- Google Custom Search APIキーと検索エンジンIDの設定が必須（.envファイル）
- Wikipediaデータは英語版（en.wikipedia）を対象。日本語版を追加する場合は、pywikibot.Site("ja", "wikipedia")に変更
- プロダクション環境ではCORS設定を適切に調整（現在はlocalhost:3000のみ許可）
- Google Custom Search APIの無料枠（100クエリ/日）に注意。超過時は課金が発生
- フロントエンドのカスタマイズ時、バックエンドAPIのURL（http://localhost:8000）を環境変数で管理推奨

## トラブルシューティング

- バックエンド起動エラー
    - requirements.txtの依存関係が正しくインストールされているか確認
    - Python 3.11以上を使用しているか確認
    - .envファイルの設定が正しいか確認

- Google Custom Search APIエラー
    - APIキーおよび検索エンジンIDの有効性をGoogle Cloud Consoleで確認
    - クエリ上限（100/日）に達していないか確認
    - ネットワーク接続を確認

- Wikipediaデータ取得エラー
    - Pywikibotのネットワーク接続を確認
    - クエリが無効（例：存在しないページ）でないか確認
    - MediaWiki APIのレート制限に注意

- フロントエンド接続エラー
    - バックエンドが http://localhost:8000 で実行中か確認
    - CORS設定が適切か確認（allow_originsに http://localhost:3000 が含まれている）
    - ブラウザのコンソールログでエラー詳細を確認

- キャッシュ関連の問題
    - cachetoolsのTTL（3600秒）を調整
    - キャッシュサイズ（maxsize=100）が十分か確認


## ライセンスと著作権

このアプリケーションは、教育目的および個人利用のための情報収集を支援するために開発されています。WikipediaのデータはCC BY-SA 3.0ライセンスに従います。表示する際は、適切な帰属（例：「出典: Wikipedia」）を記載してください。Google Custom Search APIの利用は、Googleの利用規約に従います。
