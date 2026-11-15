# init: プロジェクトへの初期設定

既にあるファイルは上書きしない。足りないものだけ作る。

1. リポジトリを軽く調べ (ビルド設定、ディレクトリ構成、既存のテスト)、分からない項目だけ利用者に質問する。
2. `.sec62443/project.yml` を作る。

```yaml
product: inverter        # inverter | servo | plc
component_type: embedded # 4-2 の種別
target_sl: 2             # SL-C 1〜4
default_mode: lite       # lite | standard | audit
language: C
coding_standard: CERT C
mcu_rtos: ""             # 例: RX72M / FreeRTOS
crypto_lib: ""           # 例: Mbed TLS。空なら暗号実装の前に確認する
static_analysis: ""      # 実行コマンド。空なら省略
test_command: ""         # ホスト上の単体テストの実行コマンド
interfaces: []           # 例: [EtherCAT, USB, UART保守, JTAG]
standard_docs: ""        # 規格本文の抜粋を置いたディレクトリ。使うなら .sec62443/standards
```

   規格本文を参照させるかを利用者に尋ねる。使う場合は `standard_docs` を設定し、そのディレクトリを作って `.gitignore` に追加する (既にあれば追加しない)。ファイルの置き方は [standards.md](standards.md) を案内する。本文のファイルは利用者が置く。

3. `.sec62443/rules.md` を見出しだけで作る (「プロジェクト固有ルール」)。
4. `.sec62443/lessons.md` を見出しだけで作る (「学びの記録」)。形式は `- <日付> #<issue> <起きたこと> → <次からどうするか>`。
5. `docs/security/trace.csv` をヘッダだけで作る。

```
issue,CR,SL,受入基準,設計,実装,テスト,状態,PR
```

`状態` は `検証済` / `未検証(実機要)` / `一部`。`設計` は lite では空でよい。
