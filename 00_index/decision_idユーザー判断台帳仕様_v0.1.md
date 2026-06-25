# decision_idユーザー判断台帳仕様 v0.1

## 0. 目的

本仕様は、後方支援軍において、AIが自動で進めてはならない判断事項を `decision_id` で管理するための仕様である。

`decision_id` は、情報公開請求、外部発信、政策評価断定、法務官引渡、中核軍引渡、前線部隊引渡、STOP解除、archive変更、GitHubマージ、既存ファイル削除、インシデント終了判断など、ユーザー判断が必要な事項を記録するためのIDである。

本仕様の目的は、AIによる整理・提案・下書きと、ユーザーによる判断・承認・保留・却下を明確に分離し、後から「何を、なぜ、いつ、どの選択肢で判断したか」を追跡できる状態にすることである。

---

## 1. `decision_id` の定義

- `decision_id` は、ユーザー判断を記録するためのIDである。
- `decision_id` はAI判断ではなく、ユーザー判断を記録する。
- AIは選択肢、根拠、未確認事項、リスク、次工程を整理できるが、ユーザー判断を代替しない。
- 情報公開請求、外部発信、評価断定、法的断定、引渡、STOP解除、PRマージ等は、必要に応じて `decision_id` を発行する。
- `decision_id` は判断そのものの根拠ではなく、判断履歴を追跡する索引である。

---

## 2. ユーザー判断台帳の位置づけ

- 後方支援軍は情報整理、根拠確認、未確認事項整理、警告、停止、引渡準備を行う。
- 最終判断者はユーザーである。
- `decision_id` は、AIの自動進行を止め、ユーザー判断を明示的に記録するための制御IDである。
- `decision_id` なしにUSER_DECISION事項を進めてはならない。
- 判断記録は `case_id`、`request_id`、`handoff_id`、`change_id`、`incident_id` と接続する。

---

## 3. `decision_id` 命名規則

| 形式 | 用途 | 例 |
|---|---|---|
| `DEC_YYYYMMDD_連番` | 汎用判断 | `DEC_20260625_001` |
| `DEC_REQUEST_連番` | 情報公開請求判断 | `DEC_REQUEST_001` |
| `DEC_PUBLIC_連番` | 外部発信判断 | `DEC_PUBLIC_001` |
| `DEC_POLICY_連番` | 政策評価判断 | `DEC_POLICY_001` |
| `DEC_LEGAL_連番` | 法務官確認判断 | `DEC_LEGAL_001` |
| `DEC_HANDOFF_連番` | 引渡判断 | `DEC_HANDOFF_001` |
| `DEC_GITHUB_連番` | PRマージ・GitHub公開判断 | `DEC_GITHUB_001` |
| `DEC_ARCHIVE_連番` | archive変更判断 | `DEC_ARCHIVE_001` |
| `DEC_STOP_連番` | STOP解除判断 | `DEC_STOP_001` |
| `DEC_INCIDENT_連番` | インシデント対応判断 | `DEC_INCIDENT_001` |

- 必ず `DEC_` で始める。
- 一度発行した `decision_id` は別判断に再利用しない。
- 判断対象や個人名を長くIDへ埋め込まない。
- 詳細は台帳項目に記録する。

---

## 4. 判断対象

`decision_id` の判断対象には、少なくとも以下を含む。

- 情報公開請求を提出するか
- 情報公開請求文案を提出用最終案にするか
- 外部照会又は確認依頼を行うか
- 外部発信するか
- 政策評価を断定調でまとめるか
- 法務官へ引き渡すか
- ハイエク中核軍へ引き渡すか
- 前線部隊へ引き渡すか
- ナイチンゲール型資料化担当官へ引き渡すか
- STOPを解除するか
- `gap_id` を `request_id` 化するか
- `warning_id` を残したまま進めるか
- GitHub PRをマージするか
- archive配下を変更するか
- 既存ファイルを削除又は上書きするか
- インシデント対応を終了扱いにするか
- 公開区分をPUBLIC扱いにするか
- 資料をGitHubへ追加するか
- 黒塗り後資料を公開可能とするか

---

## 5. 判断区分

| decision_type | 内容 |
|---|---|
| REQUEST | 情報公開請求、照会、確認依頼 |
| PUBLIC | 外部発信、公開資料化 |
| POLICY | 政策評価断定 |
| LEGAL | 法務官確認、法的論点確認 |
| HANDOFF | 部門間引渡 |
| GITHUB | PRマージ、GitHub公開 |
| ARCHIVE | archive配下変更 |
| STOP_RELEASE | STOP解除 |
| GAP_ACTION | `gap_id` の解消、保留、`request_id` 化 |
| INCIDENT | インシデント対応、終了判断 |
| DOCUMENT | 文案、資料、カード、PDF化判断 |
| OTHER | その他 |

---

## 6. `decision_id` 台帳の必須項目

| 項目 | 内容 | 必須 |
|---|---|---|
| decision_id | 判断ID | 必須 |
| decision_type | 判断区分 | 必須 |
| 判断事項 | 何を判断するか | 必須 |
| 判断対象 | PR、案件、請求文案、資料等 | 必須 |
| case_id | 関連案件 | 原則必須 |
| request_id | 情報公開請求関連 | 必要に応じ必須 |
| handoff_id | 引渡関連 | 必要に応じ必須 |
| change_id | 変更関連 | 必要に応じ必須 |
| incident_id | インシデント関連 | 必要に応じ必須 |
| source_id | 判断根拠資料 | 必要に応じ必須 |
| gap_id | 未確認事項 | 任意 |
| warning_id | 注意事項 | 任意 |
| stop_id | 停止事項 | 任意 |
| 選択肢 | 承認、保留、却下、差し戻し等 | 必須 |
| 判断結果 | 実際の判断 | 必須 |
| 判断理由 | なぜその判断にしたか | 必須 |
| 判断日 | 判断日 | 必須 |
| 判断者 | 原則ユーザー | 必須 |
| 次工程 | 判断後に行う処理 | 必須 |
| 備考 | 注意事項 | 任意 |

---

## 7. `case_id` との関係

- `decision_id` は原則として `case_id` と接続する。
- どの案件に対する判断かを明確にする。
- 複数案件に影響する判断は、関連 `case_id` を複数記録する。
- 案件外の汎用判断であっても、可能な限り管理用 `case_id` を作成する。

---

## 8. `request_id` との関係

- 情報公開請求提出判断は `decision_id` を必須とする。
- `request_id` は請求対象、文案、提出先、状態を管理する。
- `decision_id` は提出可否、提出日、文案確定、保留、差し戻しを管理する。
- `decision_id` なしに情報公開請求を提出済みにしてはならない。
- 請求文案作成と提出判断を混同しない。

---

## 9. `handoff_id` との関係

- 中核軍、法務官、前線部隊、資料化担当官、別AIへ引き渡す場合は、必要に応じて `decision_id` を作成する。
- 引渡前に `source_id`、`gap_id`、`warning_id`、`stop_id` を確認する。
- STOPが残る場合は、引渡資料に停止理由と解除条件を明記する。
- `decision_id` なしに重要引渡を確定しない。

---

## 10. `change_id` との関係

- PRマージ、仕様更新、文案変更、判断変更、既存ファイル変更は `change_id` と接続する。
- 重要変更には `decision_id` と `change_id` を併用する。
- `decision_id` は「判断」を記録し、`change_id` は「変更」を記録する。
- 判断と変更を混同しない。

---

## 11. `incident_id` との関係

- 誤公開、個人情報混入、黒塗り前資料混入、APIキー混入、誤断定、誤マージ等のインシデント対応には `decision_id` が必要となる場合がある。
- インシデント終了判断は `decision_id` と `incident_id` を接続する。
- AI判断だけで重大インシデントを終了扱いにしない。

---

## 12. STOP解除との関係

- `stop_id` はAI判断だけで解除しない。
- STOP解除には、根拠資料、追加確認、又はUSER_DECISIONが必要である。
- STOP解除判断は `DEC_STOP_連番` で管理できる。
- STOP解除後も `warning_id` が残る場合は明記する。
- STOP解除の理由は `change_id` 又は case_log にも接続する。

---

## 13. 判断前に提示すべき情報

| 項目 | 内容 |
|---|---|
| 判断事項 | 何を判断するか |
| 選択肢 | 承認、保留、却下、差し戻し等 |
| 根拠資料 | 関連 `source_id` |
| 未確認事項 | 関連 `gap_id` |
| 警告事項 | 関連 `warning_id` |
| 停止事項 | 関連 `stop_id` |
| リスク | 公開、法務、誤認、個人情報、secret等 |
| 推奨案 | AIの提案。ただし判断ではない |
| 次工程 | 判断後に進む工程 |

---

## 14. 判断後の処理

| 判断結果 | 処理 |
|---|---|
| 承認 | 次工程へ進む。必要に応じて `change_id`、`request_id`、`handoff_id` へ接続 |
| 保留 | HOLD状態にし、追加確認又は再判断へ |
| 却下 | 当該工程を停止し、理由を記録 |
| 差し戻し | 修正指示を作成し、再提出又は再PRへ |
| 条件付き承認 | 条件、期限、必要 `source_id`、`warning_id` を明記 |
| 再調査 | `case_id` を再調査状態へ戻す |

---

## 15. 判断記録テンプレート

```markdown
# decision_idユーザー判断台帳

decision_id:
decision_type:
判断事項:
判断対象:
case_id:
request_id:
handoff_id:
change_id:
incident_id:
source_id:
gap_id:
warning_id:
stop_id:
判断日:
判断者:

## 1. 判断前整理

| 項目 | 内容 |
|---|---|
| 判断事項 |  |
| 選択肢 |  |
| 根拠資料 |  |
| 未確認事項 |  |
| 警告事項 |  |
| 停止事項 |  |
| リスク |  |
| AI推奨案 |  |

## 2. 判断結果

| 項目 | 内容 |
|---|---|
| 判断結果 | 承認 / 保留 / 却下 / 差し戻し / 条件付き承認 / 再調査 |
| 判断理由 |  |
| 条件 |  |
| 次工程 |  |

## 3. 接続ID

| ID種別 | ID | 接続理由 |
|---|---|---|
| case_id |  |  |
| source_id |  |  |
| gap_id |  |  |
| warning_id |  |  |
| stop_id |  |  |
| request_id |  |  |
| handoff_id |  |  |
| change_id |  |  |
| incident_id |  |  |

## 4. 処理後記録

| 項目 | 内容 |
|---|---|
| 状態変更 |  |
| change_id |  |
| case_log更新 |  |
| dashboard更新 |  |
| 備考 |  |
```

---

## 16. WARNING / STOP

| ID | 条件 | 対応 |
|---|---|---|
| `WARN_DECISION_SCOPE` | 判断の影響範囲が広い | 影響範囲を明記 |
| `WARN_DECISION_GAP` | `gap_id` が残る | 判断時に明示 |
| `WARN_DECISION_CONDITION` | 条件付き承認 | 条件を記録 |
| `STOP_DECISION_REQUIRED` | USER_DECISIONが必要 | `decision_id` 作成まで停止 |
| `STOP_REQUEST_DECISION` | 情報公開請求提出判断前 | 提出不可 |
| `STOP_PUBLIC_DECISION` | 外部発信判断前 | 外部発信不可 |
| `STOP_GITHUB_DECISION` | GitHub公開又はPRマージ判断前 | マージ不可 |
| `STOP_ARCHIVE_DECISION` | archive変更判断前 | archive変更不可 |
| `STOP_INCIDENT_DECISION` | インシデント終了判断前 | 終了扱い不可 |

---

## 17. 禁止事項

- AI判断をユーザー判断として記録しない。
- `decision_id` なしにUSER_DECISION事項を進めない。
- 情報公開請求をユーザー判断なしに提出済みにしない。
- 外部発信をユーザー判断なしに行わない。
- 政策評価断定をユーザー判断なしに行わない。
- 法的断定をユーザー判断なしに行わない。
- 重要引渡をユーザー判断なしに確定しない。
- STOPをAI判断だけで解除しない。
- archive配下をUSER_DECISIONなしに変更しない。
- PRをコンフリクト未解消のままマージしない。
- PDF原本、個人情報、黒塗り前資料、APIキーをGitHubに追加しない。
- 判断理由を空欄のまま承認済みにしない。
- 保留、却下、差し戻しを記録せずに消さない。
