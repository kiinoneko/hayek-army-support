# secret_management_APIキー管理仕様 v0.1

## 0. 目的

本仕様は、後方支援軍において、APIキー、パスワード、トークン、秘密鍵、OAuth認証情報、Webhook secret、Cookie、セッション情報、Cloudflare Workers等の環境変数、Google API認証情報、GitHub token等の秘密情報を、GitHub、チャット、公開資料、台帳、PDF、スクリーンショット、PR本文へ誤って混入させないための管理仕様である。

本仕様は `incident_idインシデント台帳仕様_v0.1.md` と接続し、秘密情報の誤公開を未然に防止し、万一混入した場合は `INC_SECRET`、`STOP_SECRET`、`decision_id`、`change_id` により、封じ込め・無効化・再発防止まで追跡できるようにすることを目的とする。

---

## 1. secret管理の定義

- secretとは、漏えいした場合に不正アクセス、権限悪用、個人情報流出、外部サービス操作、課金被害、なりすまし、データ改ざんにつながる情報である。
- secretはGitHub public repositoryに置いてはならない。
- secretはチャット本文、PR本文、README、Markdown、PDF、スクリーンショット、台帳、テンプレートへ直接記載してはならない。
- 後方支援軍では、secretを「資料」ではなく「鍵」として扱う。
- 鍵は軍令ではない。GitHubには鍵の使い方だけを書き、鍵そのものは置かない。

---

## 2. secretに該当するもの

| 種別 | 例 |
|---|---|
| APIキー | OpenAI API key、Google API key、Jina API key、Cloudflare API token |
| アクセストークン | GitHub token、OAuth access token |
| リフレッシュトークン | OAuth refresh token |
| パスワード | 管理画面パスワード、アプリパスワード |
| 秘密鍵 | SSH private key、JWT private key、service account key |
| Webhook secret | GitHub webhook secret、署名検証用secret |
| Cookie / session | セッションCookie、認証Cookie |
| DB接続情報 | database URL、DB password |
| メール認証情報 | SMTP password、Gmail app password |
| `.env` 実ファイル | 実値入り `.env` |
| 認証JSON | Google service account JSON等 |
| 個人認証情報 | 2FA backup code、recovery code |

---

## 3. secretではないもの

| 種別 | 扱い |
|---|---|
| API名 | 公開可 |
| サービス名 | 公開可 |
| 環境変数名 | 公開可 |
| ダミー値 | 公開可 |
| `.env.example` | 実値がなければ公開可 |
| 公開URL | 公開資料であれば可 |
| 仕様説明 | secretを含まなければ可 |

- `OPENAI_API_KEY` という変数名は公開可。
- 実際のキー文字列は公開不可。
- `sk-...`、`ghp_...`、`AIza...` 等の実値らしき文字列はsecret候補として扱う。
- 判断に迷うものは `STOP_SECRET` とする。

---

## 4. 保存してよい場所・禁止場所

| 場所 | secret保存可否 | 備考 |
|---|---|---|
| GitHub public repository | 禁止 | 実値は絶対に置かない |
| GitHub private repository | 原則禁止 | secret managerを使う |
| GitHub Secrets | 可 | Actions等で使う場合 |
| Cloudflare Workers Secrets | 可 | Workers環境変数として管理 |
| OS環境変数 | 可 | ローカル実行用 |
| パスワードマネージャ | 可 | 推奨 |
| Google Drive | 原則禁止 | 実値入りファイル保存は避ける |
| チャット本文 | 禁止 | AIに貼らない |
| PR本文 | 禁止 | ダミー値のみ |
| README / Markdown | 禁止 | 手順と変数名のみ |
| スクリーンショット | 禁止 | secretが写る場合は黒塗り必須 |

---

## 5. GitHubでの禁止事項

- `.env` 実ファイルをコミットしない。
- APIキー、トークン、パスワードをMarkdownに書かない。
- service account JSONを置かない。
- private keyを置かない。
- `.pem`、`.key`、秘密情報入り `.json` を追加しない。
- スクリーンショットにAPIキーが写っている場合は追加しない。
- secretを削除しただけで安全扱いしない。
- すでにGitHubへ入ったsecretは無効化・再発行を検討する。
- secret混入は `INC_SECRET` として扱う。

---

## 6. Codex / AI利用時の禁止事項

- CodexへAPIキー実値を貼らない。
- AIへパスワードやトークンを貼らない。
- secret入りスクリーンショットをアップロードしない。
- secret入りログを貼らない。
- secret入り `.env` を読ませない。
- 例示する場合は必ずダミー値を使う。

例：

```env
OPENAI_API_KEY=your_openai_api_key_here
GITHUB_TOKEN=your_github_token_here
CLOUDFLARE_API_TOKEN=your_cloudflare_token_here
```

- AIに渡すのは「変数名」「用途」「権限範囲」「設定先」であり、secret実値ではない。
- Codexに作らせるのは `.env.example` であり、`.env` ではない。

---

## 7. `.env` / `.env.example` ルール

| ファイル | GitHub追加 | 内容 |
|---|---|---|
| `.env` | 禁止 | 実値入りのローカル設定 |
| `.env.local` | 禁止 | ローカル実値 |
| `.env.production` | 禁止 | 本番実値 |
| `.env.example` | 可 | ダミー値のみ |
| `.gitignore` | 可 | `.env` を除外するため |

`.gitignore` 例：

```gitignore
.env
.env.*
!.env.example
*.pem
*.key
service-account*.json
credentials.json
token.json
```

`.env.example` 例：

```env
OPENAI_API_KEY=your_openai_api_key_here
GITHUB_TOKEN=your_github_token_here
CLOUDFLARE_API_TOKEN=your_cloudflare_token_here
GOOGLE_CLIENT_ID=your_google_client_id_here
GOOGLE_CLIENT_SECRET=your_google_client_secret_here
```

---

## 8. 環境変数管理ルール

- 実値は環境変数又はsecret managerで管理する。
- コード内に直接書かない。
- ログに出さない。
- エラー表示に出さない。
- フロントエンドに不要なsecretを渡さない。
- public clientで使う値とserver-side secretを区別する。
- Cloudflare Workers等では環境変数又はsecret設定を使う。
- GitHub ActionsではGitHub Secretsを使う。

---

## 9. 権限最小化ルール

- 必要最小権限のtokenを使う。
- 読み取り専用で足りる場合は書き込み権限を付けない。
- repository全体ではなく必要範囲に限定する。
- 有効期限を設定できる場合は設定する。
- 個人tokenの使い回しを避ける。
- 不要になったsecretは廃止する。
- 使用者、用途、作成日、期限を管理する。

---

## 10. secret発行・利用・更新・廃止手順

| 手順 | 内容 |
|---|---|
| 1 | 用途を確認する |
| 2 | 必要最小権限を決める |
| 3 | secretを発行する |
| 4 | secret manager又は環境変数に登録する |
| 5 | `.env.example` には変数名とダミー値のみ記載する |
| 6 | 動作確認する |
| 7 | 使用先を台帳に記録する |
| 8 | 定期的に不要secretを廃止する |
| 9 | 漏えい疑いがあれば即時無効化・再発行を検討する |

---

## 11. secret混入時の初動対応

| 手順 | 内容 |
|---|---|
| 1 | 作業停止 |
| 2 | `STOP_SECRET` 発動 |
| 3 | `incident_id` 発行 |
| 4 | 混入場所を特定 |
| 5 | GitHub、チャット、Drive、ログ等の公開範囲を確認 |
| 6 | secretの無効化・再発行を検討 |
| 7 | 修正PR又はrevertを検討 |
| 8 | `decision_id` が必要か確認 |
| 9 | `change_id` で修正履歴を残す |
| 10 | 再発防止を記録 |

---

## 12. `incident_id` との接続

- secret混入は原則 `INC_SECRET` として扱う。
- APIキー、パスワード、トークン、private keyが関係する場合は重大インシデントである。
- secretを削除しただけで終了扱いにしない。
- 無効化、再発行、影響範囲確認、履歴対応、再発防止を検討する。
- `incident_idインシデント台帳仕様_v0.1.md` と接続する。

---

## 13. `decision_id` との接続

- secret無効化、再発行、履歴削除、公開範囲変更、権限変更はUSER_DECISION対象になり得る。
- 重大secret事故の終了判断には `decision_id` を必要とする。
- AI判断だけで「安全」と断定しない。

---

## 14. `change_id` との接続

- secret混入を修正するPRには `change_id` を接続する。
- `.gitignore` 修正、テンプレート修正、Evals追加、SOP修正は `change_id` で管理する。
- `change_type` は必要に応じて `INCIDENT_FIX` とする。

---

## 15. PRレビュー時のsecret確認

- [ ] `.env` が含まれていない
- [ ] `.env.example` のみで実値がない
- [ ] APIキーらしき文字列がない
- [ ] tokenらしき文字列がない
- [ ] passwordらしき実値がない
- [ ] private keyがない
- [ ] service account JSONがない
- [ ] credentialsファイルがない
- [ ] スクリーンショットにsecretが写っていない
- [ ] ログにsecretが出ていない
- [ ] QRコード化されたURLにsecretが含まれていない
- [ ] GitHub公開してよい内容のみである

---

## 16. ログ・スクリーンショット・QRコード注意

- ログにsecretを出さない。
- エラーメッセージにsecretを出さない。
- スクリーンショット撮影前にsecret欄を隠す。
- QRコード化するURLにtokenやsecretを含めない。
- 署名付きURL、有効期限付きURL、private共有URLは公開前に確認する。
- 原文URLのQRコードは公開可能URLに限定する。
- secret入りURLをQRコード化しない。

---

## 17. secret管理記録テンプレート

```markdown
# secret管理記録テンプレート

secret_id:
secret_type:
用途:
使用サービス:
使用環境:
登録場所:
権限範囲:
作成日:
有効期限:
担当:
公開可否: SECRET
GitHub掲載可否: 不可

## 1. 使用先

| 使用先 | 内容 |
|---|---|

## 2. 参照する変数名

| 環境変数名 | 用途 |
|---|---|

## 3. 実値管理

実値はこの台帳に記載しない。
実値は承認されたsecret manager又は環境変数で管理する。

## 4. 更新・廃止

| 日付 | 内容 | change_id | decision_id |
|---|---|---|---|

## 5. インシデント

| incident_id | 内容 | 対応 |
|---|---|---|
```

---

## 18. WARNING / STOP

| ID | 条件 | 対応 |
|---|---|---|
| `WARN_SECRET_SCOPE` | 権限範囲が広い | 権限縮小を検討 |
| `WARN_SECRET_AGE` | 長期間更新されていない | 更新・廃止確認 |
| `WARN_SECRET_LOCATION` | 保存場所が不明 | 保存場所確認 |
| `STOP_SECRET` | secret混入 | 即停止、incident_id発行 |
| `STOP_ENV_FILE` | `.env` 実ファイル追加 | マージ禁止 |
| `STOP_PRIVATE_KEY` | private key混入 | 即停止 |
| `STOP_TOKEN` | token混入 | 無効化検討 |
| `STOP_QR_SECRET` | secret入りURLをQRコード化 | 公開禁止 |
| `STOP_SCREENSHOT_SECRET` | secret入りスクショ | 公開禁止 |

---

## 19. 禁止事項

- APIキーをGitHubに追加しない。
- パスワードをGitHubに追加しない。
- tokenをGitHubに追加しない。
- private keyをGitHubに追加しない。
- `.env` 実ファイルをGitHubに追加しない。
- secretをチャットに貼らない。
- secretをPR本文に書かない。
- secretをREADMEに書かない。
- secretをスクリーンショットに写したまま共有しない。
- secret入りURLをQRコード化しない。
- secret混入を削除だけで終了扱いにしない。
- secretをログ出力しない。
- secretをフロントエンドへ不要に渡さない。
- 権限過大なtokenを使い回さない。
- secret混入PRをマージしない。
- secret混入時にincident_idを発行せず通常修正扱いにしない。
