# エージェントの組織と作業の流れ

役割ごとにエージェントを分け、それぞれが自分の役割に要るスキルだけを読む。オーケストレーターは成果物の中身を読まず、ID と報告だけを扱う。これでどのエージェントの文脈も小さく保つ。

## 組織図

```mermaid
flowchart TD
    U(["利用者<br>設計者兼マネジメント"])
    O["orchestrator<br>窓口と統率"]
    U <-->|"依頼、決定、承認"| O

    subgraph MAKE["作成"]
        RA["req-analyzer<br>要求分析"]
        DW["doc-writer<br>設計文書"]
        TDS["test-designer<br>テスト設計"]
        CO["coder<br>実装"]
        TE["tester<br>テスト実施"]
    end

    subgraph REVIEW["レビュー (作成者から独立)"]
        RR["req-reviewer"]
        DR["doc-reviewer"]
        TDR["test-design-reviewer"]
        CR["code-reviewer"]
        TR["test-reviewer"]
    end

    subgraph SUPPORT["支援"]
        RC["reconciler<br>整合"]
        AU["auditor<br>監査"]
        LI["librarian<br>規定の管理"]
        IM["improver<br>改善"]
    end

    O --> RA & DW & TDS & CO & TE
    O --> RR & DR & TDR & CR & TR
    O --> RC & AU & LI & IM

    RR -.->|レビュー| RA
    DR -.->|レビュー| DW
    TDR -.->|レビュー| TDS
    CR -.->|レビュー| CO
    TR -.->|レビュー| TE
```

- エージェントどうしは直接やり取りしない。依頼と報告はすべて orchestrator を通る。
- 成果物 (文書、テスト、コード) はファイルで受け渡す。orchestrator が渡すのは ID とファイルの場所だけ。

## エージェントとスキルの割当て

| エージェント | sec-base | 役割のスキル | 編集できるもの |
|---|---|---|---|
| orchestrator | — | iec62443 | PR、初期設定 |
| req-analyzer | ○ | sec-requirements | REQ、トレース表、台帳 |
| doc-writer | ○ | sec-docs | ARC、FS、SWD、トレース表、台帳 |
| test-designer | ○ | sec-test-design | UT、IT、ST の仕様、トレース表、台帳 |
| coder | ○ | sec-coding | コード、テストコード、トレース表、台帳 |
| tester | ○ | sec-test-exec | テスト結果、手順書、トレース表、台帳 |
| req-reviewer | ○ | sec-review、sec-requirements | なし |
| doc-reviewer | ○ | sec-review、sec-docs | なし |
| test-design-reviewer | ○ | sec-review、sec-test-design | なし |
| code-reviewer | ○ | sec-review、sec-coding | なし |
| test-reviewer | ○ | sec-review、sec-test-exec | なし |
| reconciler | ○ | sec-reconcile | トレース表、台帳 |
| auditor | ○ | sec-audit、sec-reconcile | 監査結果 |
| librarian | ○ | sec-org-rules | 規定の索引 |
| improver | ○ | sec-improve | 固有ルール、学びの記録 |

`sec-org-rules` は、`project.yml` の `org_rules_dir` が設定されているときに全エージェントが読む。

### 割当てを変える

| 範囲 | 方法 |
|---|---|
| 1 つのプロジェクトだけ、スキルを足す | `.sec62443/project.yml` の `agent_skills` に書く。例: `agent_skills: {coder: [.github/skills/our-rtos/SKILL.md]}` |
| 全体で変える | `.github/agents/<名前>.agent.md` の「スキル」の行を直し、`python tools/sync_agents.py` で Claude Code 用を作り直す |
| エージェントを足す | `.github/agents/` に定義を足して同じく生成し、`.github/skills/iec62443/SKILL.md` の割り振りの表に加える |
| スキルを足す | `.github/skills/<名前>/SKILL.md` を作り、使わせたいエージェントに割り当てる |

## 作業の流れ

### 文書の工程 (例: `standard spec`)

テストを先に設計し、テストが書けない項目は作成者に戻す。

```mermaid
sequenceDiagram
    actor U as 利用者
    participant O as orchestrator
    participant DW as doc-writer
    participant TD as test-designer
    participant DR as doc-reviewer
    participant TDR as test-design-reviewer

    U->>O: /iec62443 standard spec 34
    O->>U: 計画と、決めてほしいこと
    U->>O: 設計上の決定
    O->>DW: FS の項目を作成 (上流の ID、決定)
    DW-->>O: 報告 (FS-012、未整合 G-007)
    O->>TD: FS-012 のテストを設計
    TD-->>O: 報告 (ST-031、IT-008。FS-012 の解除条件が未決でテスト不能)
    O->>U: 解除条件の確認
    U->>O: 管理者操作で解除
    O->>DW: FS-012 を修正
    DW-->>O: 報告
    O->>TD: テストを更新
    TD-->>O: 報告
    par 独立レビュー
        O->>DR: FS-012 をレビュー
        DR-->>O: 承認 / 要修正
    and
        O->>TDR: ST-031、IT-008 をレビュー
        TDR-->>O: 承認 / 要修正
    end
    Note over O,TDR: 要修正なら作成者に戻して再レビュー (2 往復まで)
    O->>U: まとめ (できたこと、未整合、未検証、人への依頼) と PR
```

### 実装の工程 (例: `standard code`)

```mermaid
sequenceDiagram
    actor U as 利用者
    participant O as orchestrator
    participant TD as test-designer
    participant CO as coder
    participant TE as tester
    participant CR as code-reviewer
    participant TR as test-reviewer

    U->>O: /iec62443 standard code 35
    opt 単体テスト仕様がない
        O->>TD: SWD-020 の UT を設計
        TD-->>O: 報告 (UT-041〜045)
    end
    O->>CO: UT-041〜045 を先にテストコードにして実装
    Note over CO: 失敗を確認 → 実装 → 整理
    CO-->>O: 報告 (実装の場所、自分で実行した結果)
    O->>TE: 独立に実行して記録
    TE-->>O: 報告 (合否、未検証(実機要))
    alt 不合格がある
        O->>CO: 不合格の修正
        CO-->>O: 報告
        O->>TE: 再実行
        TE-->>O: 報告
    end
    par 独立レビュー
        O->>CR: 差分をレビュー
        CR-->>O: 承認 / 要修正
    and
        O->>TR: テスト結果をレビュー
        TR-->>O: 承認 / 要修正
    end
    O->>U: まとめと PR
```

### 順序どおりに進まないとき (コード先行と整合)

```mermaid
sequenceDiagram
    actor U as 利用者
    participant O as orchestrator
    participant DW as doc-writer
    participant CO as coder
    participant RC as reconciler
    participant X as 各担当

    U->>O: /iec62443 reverse src/auth.c
    O->>DW: コードから SWD、FS を逆生成
    DW-->>O: 報告 (SWD-030 逆生成、台帳に 上流なし)
    O->>CO: 今の振る舞いを固定するテスト
    CO-->>O: 報告
    O->>RC: ずれの洗い出し
    RC-->>O: 直し方の案 (REQ に追加、テスト不足 など)
    O->>U: 案の一覧と、どちらを正とするかの確認
    U->>O: 承認
    loop 承認された修正ごと
        O->>X: 修正 (req-analyzer、doc-writer、test-designer、coder)
        X-->>O: 報告
    end
    O->>RC: 確認して台帳を更新
    RC-->>O: 解消、残り、判断待ちの件数
    O->>U: まとめ (「確定」にするのは利用者)
```

### 1 つのエージェントだけに任せる

```mermaid
sequenceDiagram
    actor U as 利用者
    participant O as orchestrator
    participant A as 指定したエージェント

    alt orchestrator 経由
        U->>O: /iec62443 code-reviewer src/auth.c を見て
        O->>A: 依頼をそのまま渡す
        A-->>O: 報告
        O->>U: 報告を伝える
    else 直接
        U->>A: エージェントを選んで依頼
        A-->>U: 報告
    end
```
