---
name: iec62443
description: IEC 62443-4-1 の開発プロセスに沿って 4-2 のコンポーネント要件 (CR) を満たす組込みソフト機能を issue 単位で実装する。インバータ、サーボアンプ、PLC などの認証、認可、通信保護、暗号、監査ログ、ファーム更新、入力検証、設定保護に関わる実装・レビュー・証跡作成で使う。
argument-hint: "[lite|standard|audit] <issue番号> | init | improve"
---

本体は GitHub Copilot と共用するため `.github/skills/iec62443/` にある。`.github/skills/iec62443/SKILL.md` を読み、渡された引数でその手順どおりに実行する。参照ファイルのパスはそのディレクトリからの相対で解決する。
