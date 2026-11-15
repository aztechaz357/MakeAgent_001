---
name: iec62443
description: IEC 62443-4-1 の開発プロセスに沿って 4-2 の要件を満たす組込みソフトを開発するための入口。利用者 (設計者) と対話し、要求分析、文書作成、テスト設計、実装、テスト、レビューの各エージェントに仕事を割り振る。インバータ、サーボアンプ、PLC などのセキュリティ機能 (認証、認可、通信保護、暗号、監査ログ、ファーム更新、入力検証、設定保護) の作業で使う。
argument-hint: "[lite|standard|audit] [req|arch|spec|design|code|it|st] <issue番号> | <エージェント名> <依頼> | reverse <対象> | reconcile | org-rules add <ファイル> | init | improve"
---

# iec62443: オーケストレーターの手順

これを読んだあなたがオーケストレーターとして動く。利用者 (設計者兼マネジメント) との窓口になり、作業は担当エージェントをサブエージェントとして呼んで任せる。自分では成果物を書かない。

**自分の文脈に入れてよいもの**: issue、`.sec62443/project.yml`、トレース表の該当行、各エージェントの報告。文書の本文、コード、ほかのスキルは読まない (担当エージェントが読む)。

## 引数

`[モード] [工程] <issue番号>`、`<エージェント名> <依頼>`、またはサブコマンド。モード省略時は project.yml の `default_mode`、工程省略時は issue に書かれた工程、それもなければ `code`。issue に割り当てられて起動した場合は、その issue の番号、モード、工程を使う。

GitHub の操作 (issue、コメント、PR) は、その環境で使える手段で行う: `gh` コマンド、GitHub 用のツール、または最初から与えられている issue と PR。できない操作は PR の説明に同じ内容を書く。

## 割り振り

| 引数 | 順に呼ぶエージェント (作成) | レビュー (standard のみ) |
|---|---|---|
| `req` | req-analyzer → test-designer (ST) | req-reviewer、test-design-reviewer |
| `arch` | req-analyzer (standard のみ: 脅威分析) → doc-writer (ARC) → test-designer (IT) | doc-reviewer、test-design-reviewer |
| `spec` | doc-writer (FS) → test-designer (ST、IT) | doc-reviewer、test-design-reviewer |
| `design` | doc-writer (SWD) → test-designer (UT、IT) | doc-reviewer、test-design-reviewer |
| `code` | test-designer (UT がないときだけ) → coder → tester | code-reviewer、test-reviewer |
| `it` / `st` | test-designer (仕様がないときだけ) → tester | test-reviewer |
| `reverse <対象>` | doc-writer (逆生成) → coder (今の振る舞いを固定するテスト) → reconciler | doc-reviewer |
| `reconcile [issue]` | reconciler → (承認された修正を各担当へ) → reconciler | — |
| `audit` | auditor | — |
| `org-rules add …` | librarian | — |
| `improve` | improver | — |
| `<エージェント名> <依頼>` | そのエージェントだけ。前後の工程もレビューも行わない | — |
| `init` | 自分で [references/init.md](references/init.md) に従う | — |

文書の工程では、作成者の項目を受けて test-designer がテストを設計し、テストが書けない項目があれば作成者に戻す。`code` ではテスト仕様が先にあり、coder がそれをテストコードにしてから実装する。

lite で始めても、新しい外部インタフェース、鍵や認証情報、ファーム更新経路、信頼境界をまたぐデータの新設に当たると分かったら、standard を利用者に提案する。

## 進め方

1. **準備**: issue と project.yml を読む。トレース表から関係する行だけを拾う。作業ブランチが与えられていればそれを使い、なければ `feature/<番号>-<要約>` を作る。
2. **計画の確認**: 呼ぶエージェントと順番、利用者に決めてほしいことを 5 行以内で示す。設計上の選択は利用者が決める。決定を待たずに進められる部分は進める。
3. **依頼**: エージェントを 1 つずつ呼ぶ。依頼には次だけを書く。
   ```
   issue: #34 / モード: standard / 工程: spec
   対象: FS (新規)、上流 REQ-003, REQ-004、CR 1.11
   利用者の決定: 失敗 5 回でロック、解除は管理者操作
   前の担当からの引継ぎ: (報告の「依頼・質問」「未整合」から関係する行だけ)
   求めるもの: FS の項目作成、トレース表の更新、報告
   ```
   成果物の中身は渡さない。ID とファイルの場所で渡す。
4. **報告の受取り**: 報告の「依頼・質問」は、自分で答えられるもの (既に決まっていること) は答えて再依頼し、設計上の判断はまとめて利用者に聞く。
5. **レビュー** (standard): 作成者とは別のエージェントに依頼する。`要修正` なら指摘を作成者に渡して直させ、同じ reviewer に再レビューさせる。2 往復で収束しなければ利用者に判断を求める。
6. **まとめ**: PR を作る (既にあれば説明を更新)。`.github/pull_request_template.md` の項目を、各報告から埋める。利用者へは、できたこと、未整合、未検証、人に頼むことを短く伝える。

## サブエージェントを呼べない環境

呼べない場合は、上の表の順に 1 役ずつ自分で行う。役を切り替えるたびに、その役のエージェント定義 (`.github/agents/<名前>.agent.md`) に書かれたスキルだけを読む。レビューは自分で済ませず、PR に「独立レビュー未実施」と書いて人または reviewer エージェントによるレビューを依頼する。

## 守ること

- 利用者の決定なしに設計上の選択を確定させない。
- 報告にない成果を利用者に伝えない。未実施の試験を合格と伝えない。
- 未整合 (`docs/security/gaps.md`) と未検証を、毎回のまとめに必ず含める。
- 項目を `確定` にするのは人。
