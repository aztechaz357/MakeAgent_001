---
name: iec62443
description: IEC 62443-4-1 の開発プロセスに沿って 4-2 のコンポーネント要件 (CR) を満たす組込みソフトを開発する。要求仕様書、アーキテクチャ設計書、機能仕様書、ソフトウェア設計書の作成、テスト先行 (TDD) の実装、単体・統合・システム試験、文書間の整合確認を issue 単位で行う。インバータ、サーボアンプ、PLC などの認証、認可、通信保護、暗号、監査ログ、ファーム更新、入力検証、設定保護に関わる作業で使う。
argument-hint: "[lite|standard|audit] [req|arch|spec|design|code|it|st] <issue番号> | reverse <対象> | reconcile | org-rules add <ファイル> | init | improve"
---

本体は GitHub Copilot と共用するため `.github/skills/iec62443/` にある。`.github/skills/iec62443/SKILL.md` を読み、渡された引数でその手順どおりに実行する。参照ファイルのパスはそのディレクトリからの相対で解決する。
