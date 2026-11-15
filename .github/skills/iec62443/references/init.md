# init: プロジェクトへの初期設定

既にあるファイルは上書きしない。足りないものだけ作る。

1. リポジトリを軽く調べ (ビルド設定、ディレクトリ構成、既存のテスト、既存の仕様書)、分からない項目だけ利用者に質問する。
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
coverage: ""             # 例: "UT ブランチ 100%"。空なら測って報告のみ
interfaces: []           # 例: [EtherCAT, USB, UART保守, JTAG]
standard_docs: ""        # 規格本文の抜粋を置いたディレクトリ。使うなら .sec62443/standards
org_rules_dir: ""        # 社内規定の索引と本文。使うなら .sec62443/org-rules
docs:                    # 文書の名称と場所。社内の呼び方と置き場所に合わせて変える
  REQ: {name: 製品セキュリティ要求仕様書, path: docs/security/requirements.md}
  ARC: {name: 製品セキュリティアーキテクチャ設計書, path: docs/security/architecture.md}
  FS:  {name: 機能仕様書, path: docs/security/functional-spec.md}
  SWD: {name: ソフトウェア設計書, path: docs/security/software-design.md}
  UT:  {name: 単体テスト仕様書, path: docs/security/test/ut.md}
  IT:  {name: 統合テスト仕様書, path: docs/security/test/it.md}
  ST:  {name: システム試験仕様書, path: docs/security/test/st.md}
```

3. 利用者に尋ねて設定する。
   - 規格本文を参照させるか。使う場合は `standard_docs` を設定し、ディレクトリを作って `.gitignore` に追加する。置き方は [standards.md](standards.md) を案内する。
   - 社内規定を参照させるか。使う場合は `org_rules_dir` を設定し、見出し行だけの `index.md` を作る。git で管理するかどうかを尋ね、しない場合は `.gitignore` に追加する。登録は `org-rules add` で行うと案内する。
   - 既にある仕様書や設計書があれば `docs` の場所をそれに合わせる。Markdown 以外 (Word、Excel) の場合は、エージェントが読み書きできる形にする方法を相談する。
4. `.sec62443/rules.md` を見出しだけで作る (「プロジェクト固有ルール」)。
5. `.sec62443/lessons.md` を見出しだけで作る (「学びの記録」)。形式は `- <日付> #<issue> <起きたこと> → <次からどうするか>`。
6. `docs/security/gaps.md` を見出しだけで作る (「未整合台帳」)。
7. `docs/security/trace.csv` をヘッダだけで作る。

```
ID,上流,CR,テスト,実装,状態,issue,PR
```

- `ID`: 文書の項目 (REQ / ARC / FS / SWD) の ID。1 項目 1 行。文書のない作業は空にして、未整合台帳に載せる。
- `上流`: 元になった項目の ID。複数は `;` で区切る。
- `テスト`: 対になるテストケースの ID (UT / IT / ST)。
- `実装`: ソースファイルと関数。文書だけの項目は空。
- `状態`: `仮` / `確定` / `逆生成` / `要見直し` / `廃止` に加え、テストの結果を `検証済` / `未検証(実機要)` / `不合格` で書く (例: `仮;未検証(実機要)`)。

文書のファイルは init では作らない。最初にその工程を実行したときに作る。
