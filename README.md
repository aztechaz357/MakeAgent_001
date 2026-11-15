# MakeAgent_001

IEC 62443-4-1 のプロセスで 4-2 の要件を満たす組込みソフト (インバータ、サーボアンプ、PLC など) を開発するための、エージェントとスキルの一式。Claude Code と GitHub Copilot の両方で使える。

> 開発を支援する道具で、適合や認証を保証するものではない。規格本文は含まない。

## まず使う

```
/iec62443 init        最初に 1 回。プロジェクトの設定を作る
/iec62443 12          issue #12 を進める
/iec62443 help ○○     やりたいことを書くと、打つコマンドと担当を教える
```

## やりたいことから引く

| やりたいこと | 入力 |
|---|---|
| 要求仕様書を作る | `/iec62443 req 12` |
| 機能仕様書 (外部仕様書) を作る | `/iec62443 spec 12` |
| テストを先に書いて実装する | `/iec62443 code 12` |
| 既にあるコードから設計書を起こす | `/iec62443 reverse src/auth.c` |
| 文書とコードのつじつまを合わせる | `/iec62443 reconcile` |
| コードだけ見てもらう | `/iec62443 code-reviewer src/auth.c を見て` |
| 以前の機種の資料を参照させる | `/iec62443 prior add <フォルダ> <機種名>` |
| 社内規定を守らせる | `/iec62443 org-rules add <ファイル> 部` |
| リリース前に不足を洗い出す | `/iec62443 audit` |
| GitHub の代わりに Redmine のチケットで進める | `project.yml` に `tracker: redmine` ([詳細](docs/guide.md#チケットの置き場所-github--redmine)) |

全部の一覧: **[逆引き辞典](.github/skills/iec62443/references/howto.md)**

## 誰に任せるか

| 名前 | 日本語名 | 任せること |
|---|---|---|
| orchestrator | 統括 | 窓口。迷ったらここ。担当に割り振ってまとめる |
| req-analyzer | 要求分析担当 | 当てはまる CR、脅威分析、要求仕様書 |
| doc-writer | 文書作成担当 | アーキテクチャ設計書、機能仕様書、ソフトウェア設計書 |
| test-designer | テスト設計担当 | 単体、統合、システム試験のテスト仕様 |
| coder | 実装担当 | テストコードと実装 |
| tester | テスト実施担当 | テストの実行と結果の記録 |
| ○○-reviewer | ○○レビュー担当 | 上の 5 つそれぞれの独立レビュー (req / doc / test-design / code / test) |
| reconciler | 整合担当 | 文書、テスト、コードのつじつま合わせ |
| auditor | 監査担当 | リリース前の棚卸し |
| librarian | 資料管理担当 | 社内規定と過去の資料の登録、検索 |
| improver | 改善担当 | 使ってみた学びをルールとスキルに反映 |

## 詳しく

- [逆引き辞典](.github/skills/iec62443/references/howto.md): やりたいこと → 入力と担当
- [組織図とシーケンス図](docs/organization.md): 誰が何を読み、どの順で動くか
- [詳細ガイド](docs/guide.md): 工程、モード、SL の切替、規格・規定・過去資産の参照、構成、導入

このリポジトリ自体の開発は issue を起票し、`feature/<issue番号>-<要約>` ブランチで PR を出す。
