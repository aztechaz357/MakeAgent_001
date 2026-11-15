---
name: iec62443
description: IEC 62443-4-1 の開発プロセスに沿って 4-2 の要件を満たす組込みソフトを開発するための入口。利用者 (設計者) と対話し、要求分析、文書作成、テスト設計、実装、テスト、レビューの各エージェントに仕事を割り振る。インバータ、サーボアンプ、PLC などのセキュリティ機能 (認証、認可、通信保護、暗号、監査ログ、ファーム更新、入力検証、設定保護) の作業で使う。
argument-hint: "[lite|standard|audit] [req|arch|spec|design|code|it|st] <issue番号> | <エージェント名> <依頼> | reverse <対象> | reconcile | org-rules add <ファイル> | init | improve"
---
<!-- tools/sync_agents.py が生成。直接編集せず .github/ 側を直す -->

本体は GitHub Copilot と共用するため `.github/skills/iec62443/` にある。`.github/skills/iec62443/SKILL.md` を読み、渡された引数でその手順どおりに実行する。
