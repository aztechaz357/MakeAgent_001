# MakeAgent_001

IEC 62443-4-1 の開発プロセスに沿って、IEC 62443-4-2 のコンポーネント要件を満たす組込みソフト機能を実装するためのスキルとエージェント。インバータ、サーボアンプ、PLC などを対象とし、Claude Code と GitHub Copilot の両方で使う。

> 開発を支援する道具であり、適合や認証を保証するものではない。要件マップは独自要約で、規格原本との照合が必要 (issue #4)。規格本文は含まない。

## 使い方

Claude Code でも GitHub Copilot (VS Code のエージェントモード) でも、チャットで同じコマンドを使う。

| 操作 | コマンド |
|---|---|
| 初期設定 | `/iec62443 init` |
| issue を実装 | `/iec62443 12` または `/iec62443 standard 12` |
| 改善提案 | `/iec62443 improve` |
| 独立レビュー | エージェント `sec-reviewer` を選ぶ (standard では自動で呼ばれる) |

GitHub 上で Copilot に issue を割り当てて動かす場合は、「セキュリティ機能」テンプレートで起票する。issue に書かれたモードで同じ手順が動く。

### ツールごとの読込み先

| | Claude Code | Copilot (VS Code) | Copilot (GitHub 上) |
|---|---|---|---|
| 常時指示 | `CLAUDE.md` → `AGENTS.md` | `AGENTS.md` | `AGENTS.md` |
| スキル | `.claude/skills/` (本体への入口) | `.github/skills/` (本体) | 両方 |
| エージェント | `.claude/agents/` | `.github/agents/` | `.github/agents/` |

手順の本体は `.github/skills/iec62443/` の 1 か所だけで、ほかは本体を指す数行のファイル。修正は本体に対して行う。

## モード

| モード | 用途 | 消費 |
|---|---|---|
| lite (既定) | 日常の実装。CR の特定、実装、確認観点、テスト、トレース 1 行 | 小 |
| standard | 新規のセキュリティ機能や外部インタフェースの変更。脅威分析、設計メモ、独立レビューを追加 | 中 |
| audit | リリース前の棚卸し。4-1 の証跡と CR の充足状況の不足を一覧にする | 大 |

## 目標セキュリティレベル (SL) の切替

どの SL まで満たすかは、適用先プロジェクトの `.sec62443/project.yml` の `target_sl` で決まる。スキルは要件マップから、必要な SL がこの値以下の CR と強化要件 (RE) だけを選ぶ。

```yaml
target_sl: 1   # 1〜4。SL1 でよければ 1、SL2 が必要なら 2 (既定)
```

| やりたいこと | 方法 |
|---|---|
| 製品全体を SL1 でよいことにする | `target_sl: 1` にする |
| 製品全体を SL2 以上にする | `target_sl: 2` (または 3、4) にする |
| 一部の CR だけ扱いを変える | `.sec62443/rules.md` に CR、扱い、理由を 1 行で書く (下の例) |
| 1 つの issue だけ変える | issue の本文に「この issue は SL1 で実装」のように理由付きで書く |

`rules.md` の例:

```
- CR 1.1 は SL2 の RE(1) まで実装する (全体は SL1 だが、保守ツール接続は個人を特定したいため)
- CR 2.2 は対象外 (無線インタフェースを持たないため)
- EDR 3.14 RE(1) は次期ハードで対応。現行機は SL1 相当 (セキュアブート非対応の MCU のため)
```

注意:

- `target_sl` を変えても、実装済みの機能は自動では見直されない。上げた後は `/iec62443 audit` で不足を洗い出す。
- SL を下げる、CR を対象外にする、という判断は製品のリスク評価に基づいて人が行い、理由を残す。エージェントは `rules.md` と issue に書かれた判断に従うだけで、自分では下げない。

## 規格本文の参照 (任意)

このリポジトリには規格本文を含めていない。既定では要件マップ (独自要約) だけで動く。購入済みの規格を適用先のローカルに置くと、スキルは該当する要件の本文を読んで受入基準を作る。

1. 規格を AI サービスに読ませてよいか、ライセンス条件と社内規程を確認する。
2. 適用先の `.sec62443/project.yml` に `standard_docs: .sec62443/standards` を設定する (`/iec62443 init` で尋ねられる)。
3. そのディレクトリを `.gitignore` に入れる (`init` が追加する)。
4. 要件 1 つを 1 ファイルにして置く。全文を 1 ファイルで置くと毎回の消費が大きくなる。

```
.sec62443/standards/
  4-2/CR-1.1.md
  4-2/EDR-3.14.md
  4-1/SR.md
```

- 置いたファイルは git に入らないので、開発者ごとに用意する。GitHub 上で動く Copilot からは見えず、その場合は要件マップだけで動く。
- ファイルがある要件は本文を正とし、ない要件は要件マップで進めて issue に「本文未参照」と記録する。
- 本文と要件マップが食い違うと `lessons.md` に記録され、`/iec62443 improve` で要件マップの修正提案になる。
- 本文は issue、PR、文書に書き写されない (ID と要約のみ)。

## 構成

```
AGENTS.md                         両ツール共通の常時指示 (数行)
CLAUDE.md                         AGENTS.md を取り込むだけ
.github/skills/iec62443/          スキル本体。SKILL.md と、モードに応じて読む references/
.github/agents/                   Copilot 用の独立レビューエージェント
.github/ISSUE_TEMPLATE/ ほか      issue / PR テンプレート
.claude/skills/iec62443/          Claude Code 用の入口 (本体を指すだけ)
.claude/agents/                   Claude Code 用の独立レビューエージェント
```

適用先のプロジェクトで `init` を実行すると作られるもの:

```
.sec62443/project.yml             目標 SL、製品種別、言語、ツール
.sec62443/rules.md                プロジェクト固有ルール (改善ループで育つ)
.sec62443/lessons.md              学びの記録
.sec62443/standards/              規格本文の抜粋 (任意、git 管理外)
docs/security/trace.csv           issue、CR、実装、テストの対応表
```

## 改善ループ

1. 作業中に手順どおりに進まなかった点を `lessons.md` に 1 行残す
2. `improve` が学びを「固有 / 一般 / 一過性」に分ける
3. 固有はそのプロジェクトの `rules.md` へ、一般はこのリポジトリへの issue (ラベル `improvement-loop`) へ

## 適用先への導入

現時点では手動 (自動化は issue #5)。`AGENTS.md`、`CLAUDE.md`、`.claude/`、`.github/` を適用先にコピーし、`init` を実行する。

## 開発

issue を起票し、`feature/<issue番号>-<要約>` ブランチで作業して PR を出す。
