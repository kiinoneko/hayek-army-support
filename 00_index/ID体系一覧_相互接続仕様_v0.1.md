# ID体系一覧・相互接続仕様 v0.1

## 目的

本仕様書は、後方支援軍で使用する各種IDを一覧化し、相互接続ルールを定義するための仕様書である。

`case_id`、`source_id`、`gap_id`、`warning_id`、`stop_id`、`actor_id`、`quote_id`、`policy_id`、`request_id`、`decision_id`、`handoff_id`、`incident_id`、`change_id` を一枚の地図として整理し、案件・資料・発言・政策・判断・請求・引渡・事故・変更管理を相互に追跡可能にする。

IDが増えても台帳間の接続を失わず、後から「どの案件が、どの資料に基づき、どの穴を持ち、誰が関与し、何を発言し、どの政策に関係し、どの判断を経て、どこへ引き渡されたか」を確認できる状態を作ることを目的とする。

## 概要

本仕様書は、各IDを単なる番号ではなく、後方支援軍の調査・判断・請求・引渡・変更管理をつなぐ管理索引として扱う。読み手は、案件の中心、根拠資料、未確認事項、注意事項、停止事項、関与者、発言、政策、情報公開請求、ユーザー判断、引渡、事故、PR変更を日本語の運用文脈で確認できる。

## 必須事項

- 本文の見出し、説明文、箇条書き、表の説明は日本語中心で記述する。
- ID名、接頭辞、コード表記は、台帳間で機械的に照合するため原表記を維持する。
- 各IDは孤立させず、案件、政策、資料、判断、請求、引渡、事故、変更管理のいずれかに接続する。
- 根拠、未確認、注意、停止、判断の役割を混同しない。

## 1. ID体系一覧の定義

### 表記方針

本文は日本語を中心に記述する。ただし、`case_id`、`source_id`等のID名、`SRC_`、`GAP_`等の接頭辞、`USER_DECISION`、`case_log`、`archive`等の運用上の固定表記は、台帳間の照合性を保つため原表記を維持する。

ID体系一覧とは、後方支援軍の各台帳・仕様書・調査メモ・判断記録・PR変更管理で使用するIDの役割、命名規則、接続先、接続理由、禁止事項を統一的に整理した管理仕様である。

各IDは単独で意味を完結させるものではなく、案件、根拠資料、未確認事項、ユーザー判断、請求、引渡、事故、変更管理を相互接続するための索引として使用する。

## 2. IDの基本原則

- 各IDは必ず固有の役割を持つ。
- IDを混同しない。
- `source_id`は根拠資料を示すIDであり、他のIDの根拠を支える。
- `gap_id`は未確認事項を示すIDであり、根拠の代替ではない。
- `warning_id`は注意事項を示すIDであり、停止ではない。
- `stop_id`は停止事項を示すIDであり、自動進行してはならない。
- `decision_id`はユーザー判断を記録するIDであり、AI判断で代替しない。
- `actor_id`、`quote_id`、`policy_id`は公的責任線を追跡するためのIDであり、個人攻撃や断定の根拠にしない。
- IDは必ず`case_id`又は`policy_id`等と接続し、孤立させない。

## 3. ID一覧表

| ID | 名称 | 役割 | 主要接続先 |
|---|---|---|---|
| `case_id` | 案件ID | 案件全体を管理する中心ID | `source_id`, `gap_id`, `decision_id`, `handoff_id` |
| `source_id` | 根拠資料ID | 資料、文書、URL、PDF、議事録等を管理 | `case_id`, `quote_id`, `policy_id`, `request_id` |
| `gap_id` | 情報ギャップID | 未確認事項、不足資料、本文未確認等を管理 | `case_id`, `request_id`, `decision_id` |
| `warning_id` | 警告ID | 注意事項、比較注意、確認不足を管理 | `case_id`, `source_id`, `policy_id` |
| `stop_id` | 停止ID | 自動進行禁止の理由を管理 | `case_id`, `decision_id` |
| `actor_id` | 政治行政アクターID | 関与者、発言者、同意人事、職責を管理 | `quote_id`, `policy_id`, `case_id` |
| `quote_id` | 発言・言質ID | 発言、答弁、公約、SNS投稿等を管理 | `actor_id`, `source_id`, `policy_id` |
| `policy_id` | 政策・事業ID | 政策、事業、制度、契約、補助金等を管理 | `source_id`, `actor_id`, `quote_id`, `request_id` |
| `request_id` | 情報公開請求ID | 請求前調査、文案、提出、開示結果を管理 | `gap_id`, `decision_id`, `source_id` |
| `decision_id` | 判断ID | `USER_DECISION`を管理 | `case_id`, `request_id`, `handoff_id` |
| `handoff_id` | 引渡ID | 中核軍、法務官、前線部隊等への引渡を管理 | `case_id`, `decision_id` |
| `incident_id` | インシデントID | 誤公開、誤断定、個人情報混入等を管理 | `case_id`, `change_id`, `decision_id` |
| `change_id` | 変更管理ID | PR、マージ、差し戻し、仕様更新を管理 | PR番号, `incident_id`, `decision_id` |

## 4. 各IDの役割

### case_id

案件全体を束ねる中心IDである。原則として、調査、分析、請求、引渡、判断、変更管理は`case_id`を起点に接続する。

### source_id

根拠資料を管理するIDである。資料、文書、URL、PDF、議事録、開示資料、公式ページ等を識別し、正式事実、正式数値、正式発言、正式政策整理の根拠を支える。

### gap_id

未確認事項、不足資料、本文未確認、照合未完了などを管理するIDである。`gap_id`は不足状態を示すものであり、根拠の代替にはならない。

### warning_id

注意事項、比較注意、確認不足、文脈注意、推定混入リスクなどを管理するIDである。停止ではないが、無視してよい注意ではない。

### stop_id

自動進行禁止の理由を管理するIDである。`stop_id`が付与された案件又は工程は、対応する`decision_id`等で解除又は方針決定されるまで自動進行してはならない。

### actor_id

政治行政アクター、関与者、発言者、役職者、同意人事、担当部署等を管理するIDである。公的責任線を追跡するために使用し、個人攻撃や断定の根拠にしない。

### quote_id

発言、答弁、公約、SNS投稿、会見発言、議事録上の発言等を管理するIDである。正式な`quote_id`化には`source_id`との接続を必要とする。

### policy_id

政策、事業、制度、契約、補助金、委託、改革項目等を管理するIDである。政策評価断定の根拠そのものではなく、関連資料、発言、請求、判断を束ねる接続点として使用する。

### request_id

情報公開請求の請求前調査、文案、提出、補正、開示結果、不開示理由、審査請求候補等を管理するIDである。原則として`gap_id`を解消する目的で作成する。

### decision_id

`USER_DECISION`を管理するIDである。情報公開請求、引渡、外部発信、GitHub公開、archive変更等、ユーザー判断が必要な事項をAI判断で代替しないために使用する。

### handoff_id

中核軍、法務官、前線部隊、別担当、外部レビュー等への引渡を管理するIDである。引渡理由、引渡先、引渡条件、判断履歴を`case_id`又は`decision_id`に接続する。

### incident_id

誤公開、誤断定、個人情報混入、黒塗り漏れ、根拠不備、運用事故等を管理するIDである。処理済みとする場合も`change_id`及び`decision_id`と接続する。

### change_id

PR、マージ、差し戻し、仕様更新、台帳変更、運用変更等を管理するIDである。重要な変更はPR番号、`decision_id`、必要に応じて`incident_id`と接続する。

## 5. ID命名規則

| ID種別 | 接頭辞 |
|---|---|
| `case_id` | `case_` |
| `source_id` | `SRC_` |
| `gap_id` | `GAP_` |
| `warning_id` | `WARN_` |
| `stop_id` | `STOP_` |
| `actor_id` | `ACTOR_` |
| `quote_id` | `QUOTE_` |
| `policy_id` | `POLICY_` |
| `request_id` | `REQ_` |
| `decision_id` | `DEC_` |
| `handoff_id` | `HANDOFF_` |
| `incident_id` | `INC_` |
| `change_id` | `CHG_` |

### 命名補足

- 接頭辞はID種別を即時判別するために固定する。
- 接頭辞の大文字・小文字は本仕様の表記に従う。
- `case_id`のみ、案件名、地域名、日付、連番を含めるため小文字の`case_`を使用する。
- 同一IDを複数の意味で再利用しない。
- 廃止又は統合したIDは、削除ではなく状態管理又は変更履歴で追跡する。

## 6. ID相互接続ルール

- `case_id`は原則として全ての案件の中心に置く。
- `source_id`は正式事実、正式数値、正式`quote_id`、正式`policy_id`の根拠として接続する。
- `gap_id`は`case_id`、`policy_id`、`request_id`に接続し、未確認事項として管理する。
- `request_id`は`gap_id`を解消する目的で作成する。
- `decision_id`は`USER_DECISION`が必要な事項に接続する。
- `handoff_id`は`decision_id`又は`case_id`に接続する。
- `actor_id`は`quote_id`、`policy_id`、`case_id`に接続する。
- `quote_id`は`actor_id`と`source_id`に接続する。
- `policy_id`は`source_id`、`actor_id`、`quote_id`、`request_id`、`case_id`に接続する。
- `incident_id`は`case_id`、`change_id`、`decision_id`に接続する。
- `change_id`はPR番号、`decision_id`、`incident_id`と接続する。

### 接続例

#### 例1：社協決算分析

| ID種別 | ID |
|---|---|
| `case_id` | `case_taketoyo_shakyo_20260619_001` |
| `source_id` | `SRC_WAM_001` |
| `gap_id` | `GAP_CONTRACT_001` |
| `policy_id` | `POLICY_SHAKYO_001` |
| `request_id` | `REQ_CONTRACT_001` |
| `decision_id` | `DEC_REQUEST_001` |
| `handoff_id` | `HANDOFF_LEGAL_001` |

#### 例2：議員発言調査

| ID種別 | ID |
|---|---|
| `case_id` | `case_chita_council_quote_001` |
| `actor_id` | `ACTOR_COUNCIL_001` |
| `quote_id` | `QUOTE_COUNCIL_001` |
| `source_id` | `SRC_MINUTES_001` |
| `policy_id` | `POLICY_REFORM_001` |
| `warning_id` | `WARN_CONTEXT` |

#### 例3：GitHub PR変更管理

| 項目 | 内容 |
|---|---|
| `change_id` | `CHG_POLICY_001` |
| PR番号 | #27 |
| 対象ファイル | `00_index/policy_id政策事業台帳仕様_v0.1.md` |
| `decision_id` | `DEC_GITHUB_001` |
| 状態 | squash merge済み |

## 7. 中心IDと補助ID

| 区分 | ID | 位置づけ |
|---|---|---|
| 中心ID | `case_id` | 案件全体の中心 |
| 根拠ID | `source_id` | 事実・数値・発言・文書の根拠 |
| 不足ID | `gap_id` | 未確認事項 |
| 制御ID | `warning_id` / `stop_id` | 注意・停止 |
| 人物ID | `actor_id` | 公的関与者 |
| 発言ID | `quote_id` | 発言・言質 |
| 政策ID | `policy_id` | 政策・事業 |
| 請求ID | `request_id` | 情報公開請求 |
| 判断ID | `decision_id` | ユーザー判断 |
| 引渡ID | `handoff_id` | 部隊間引渡 |
| 事故ID | `incident_id` | インシデント |
| 変更ID | `change_id` | PR・仕様変更 |

## 8. source_idとの接続原則

- `source_id`なしに正式事実化しない。
- `source_id`なしに正式数値化しない。
- `source_id`なしに正式`quote_id`化しない。
- `source_id`なしに正式`policy_id`化しない。
- `source_id`は資料名、年度、公開元、取得日、本文確認状態、該当ページを持つ。
- `source_id`はダッシュボードやメモの代替ではない。

## 9. gap_idとの接続原則

- `gap_id`は不足や未確認を示す。
- `gap_id`は根拠ではない。
- `gap_id`がある場合は、解消、保留、`request_id`化、`USER_DECISION`待ちのいずれかに分類する。
- `gap_id`を放置せず、`case_log`又はダッシュボードに表示する。
- `gap_id`を正式判断の根拠にしない。

## 10. USER_DECISIONとの接続原則

- 情報公開請求、法務官引渡、中核軍引渡、前線部隊引渡、外部発信、政策評価断定、GitHub公開、archive変更は`decision_id`で管理する。
- `decision_id`なしに`USER_DECISION`事項を進めない。
- AI判断で`USER_DECISION`を代替しない。
- `decision_id`は`case_id`と接続する。

## 11. ダッシュボードとの関係

- 運用ダッシュボードは各IDを一覧表示する司令盤である。
- ダッシュボードは正式根拠ではない。
- 正式根拠は`source_id`と原資料に戻る。
- 停止（STOP）案件、ユーザー判断（`USER_DECISION`）待ち案件、未解消の`gap_id`を持つ案件はダッシュボードで確認できるようにする。

## 12. ID接続テンプレート

### ID相互接続テンプレート

#### 1. 案件中心接続

| ID種別 | ID | 内容 |
|---|---|---|
| `case_id` |  |  |
| `policy_id` |  |  |
| `source_id` |  |  |
| `gap_id` |  |  |
| `warning_id` |  |  |
| `stop_id` |  |  |
| `actor_id` |  |  |
| `quote_id` |  |  |
| `request_id` |  |  |
| `decision_id` |  |  |
| `handoff_id` |  |  |
| `incident_id` |  |  |
| `change_id` |  |  |

#### 2. 接続理由

| 接続元ID | 接続先ID | 接続理由 | 状態 |
|---|---|---|---|
|  |  |  |  |

#### 3. 現在状態

| 項目 | 内容 |
|---|---|
| 現在状態 |  |
| 停止（STOP）有無 |  |
| ユーザー判断（USER_DECISION）有無 |  |
| 未解消`gap_id` |  |
| 次工程 |  |

## 13. 禁止事項

- IDを根拠資料そのものとして扱わない。
- `source_id`なしで正式事実化しない。
- `gap_id`を根拠として扱わない。
- `warning_id`を無視しない。
- `stop_id`を無視して自動進行しない。
- `decision_id`なしに`USER_DECISION`事項を進めない。
- `actor_id`を根拠に個人責任、違法、不正、癒着、汚職を断定しない。
- `quote_id`未確認の発言を正式言質として扱わない。
- `policy_id`だけで政策評価断定しない。
- `request_id`なしに情報公開請求を管理しない。
- `incident_id`なしにインシデントを処理済みにしない。
- `change_id`なしに重要なPR変更を管理しない。
- PDF原本、個人情報、黒塗り前資料をGitHubに追加しない。
- archive配下を変更しない。
