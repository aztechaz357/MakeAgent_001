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
