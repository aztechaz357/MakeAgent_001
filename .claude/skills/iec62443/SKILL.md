---
name: iec62443
description: IEC 62443-4-1 の開発プロセスに沿って 4-2 のコンポーネント要件 (CR) を満たす組込みソフト機能を issue 単位で実装する。インバータ、サーボアンプ、PLC などの認証、認可、通信保護、暗号、監査ログ、ファーム更新、入力検証、設定保護に関わる実装・レビュー・証跡作成で使う。引数は「[lite|standard|audit] <issue番号>」「init」「improve」。
---

# iec62443

引数: `init` | `improve` | `[モード] <issue番号>`。モード省略時は `.sec62443/project.yml` の `default_mode`。

読むファイルは下の表で指定されたものだけにする。指定のない参照ファイルは開かない。

## サブコマンド

- `init`: [references/init.md](references/init.md) に従い `.sec62443/` を作る。
- `improve`: [references/improve.md](references/improve.md) に従い改善提案を出す。

## モード

| モード | 用途 | 実施する工程 | 追加で読む参照 |
|---|---|---|---|
| lite | 日常の実装。消費を最小にする | 1, 2, 4, 5, 7, 8 | cr-map.md の該当 FR の節だけ |
| standard | 新規のセキュリティ機能、外部インタフェースの追加・変更 | 1〜8 すべて | 上に加え standard.md、profiles.md の該当製品の節 |
| audit | リリース前や審査前の棚卸し。コードは変更しない | audit.md の手順のみ | audit.md |

lite で始めても、次のどれかに当たると分かった時点で standard を提案して確認を取る: 新しい外部インタフェース、鍵や認証情報の扱い、ファーム更新経路、信頼境界をまたぐデータの新設。

## 工程 (括弧内は 4-1 のプラクティス)

1. **準備**: `gh issue view <番号>` で issue を読む。`.sec62443/project.yml` と `.sec62443/rules.md` を読む。`feature/<番号>-<要約>` ブランチにいなければ作る。
2. **要件 (SR)**: [references/cr-map.md](references/cr-map.md) から該当 CR を選び、目標 SL で必要な RE まで含める。CR ごとに検証可能な受入基準を 1〜3 行で書き、issue にコメントする。該当 CR がないと判断したらその理由を書く。
3. **設計 (SD)** — standard のみ: [references/standard.md](references/standard.md) に従い脅威と設計判断を記録する。
4. **実装 (SI)**: 実装する。変更した関数ごとに [references/cert-c.md](references/cert-c.md) の観点を確認する。暗号は自作せず、project.yml の `crypto_lib` を使う。制御周期や割込み応答に影響する変更は影響の見積りを残す。
5. **検証 (SVV)**: 受入基準 1 つにつき最低 1 つ、正常系と拒否される側のテストを書いて実行する。実機でしか確認できない項目は「未検証 (実機要)」と明記する。standard では standard.md の追加試験も行う。
6. **独立レビュー** — standard のみ: エージェント `sec-reviewer` に差分を渡し、指摘を反映する。
7. **トレース**: `docs/security/trace.csv` に 1 行追記する (列は init.md を参照)。
8. **PR と学び**: PR テンプレートを埋めて PR を作る。手順どおりに進まなかった点、プロジェクト固有の事情で判断した点があれば `.sec62443/lessons.md` に 1 行追記する。なければ書かない。

## 守ること

- 4-2 の適合を断定しない。「受入基準を満たすテストが通った」「未検証」のように根拠で述べる。
- cr-map.md は独自要約で、規格原本との照合が済んでいない項目がある。SL の割当が判断を左右するときは、原本の確認を人に依頼する。
- セキュリティ機能を弱める変更 (認証の省略、検証の無効化、デバッグ用バックドア) は、issue に明示の承認がない限り行わない。
