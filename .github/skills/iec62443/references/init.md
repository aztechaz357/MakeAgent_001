# init: プロジェクトへの初期設定

既にあるファイルは上書きしない。足りないものだけ作る。

1. リポジトリを軽く調べ (ビルド設定、ディレクトリ構成、既存のテスト、既存の仕様書)、分からない項目だけ利用者に質問する。
2. `.sec62443/project.yml` を作る。

```yaml
product: inverter        # inverter | servo | plc
component_type: embedded # 4-2 の種別
target_sl: 2             # SL-C 1〜4
default_mode: lite       # lite | standard | audit
tracker: github          # チケットの置き場所。github | redmine | manual
redmine_url: ""          # 例: https://redmine.example.co.jp (tracker: redmine のとき)
redmine_project: ""      # Redmine のプロジェクト識別子
redmine_api_key_env: REDMINE_API_KEY   # API キーを入れておく環境変数の名前 (キーそのものは書かない)
redmine_format: markdown # Redmine の書式。markdown | textile
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
prior_dir: ""            # 過去資産 (以前のプロジェクト、別機種) の索引。使うなら .sec62443/prior
docs:                    # 文書の名称と場所。社内の呼び方と置き場所に合わせて変える
  REQ: {name: 製品セキュリティ要求仕様書, path: docs/security/requirements.md}
  ARC: {name: 製品セキュリティアーキテクチャ設計書, path: docs/security/architecture.md}
  FS:  {name: 機能仕様書, path: docs/security/functional-spec.md}
  SWD: {name: ソフトウェア設計書, path: docs/security/software-design.md}
  UT:  {name: 単体テスト仕様書, path: docs/security/test/ut.md}
  IT:  {name: 統合テスト仕様書, path: docs/security/test/it.md}
  ST:  {name: システム試験仕様書, path: docs/security/test/st.md}
agent_skills: {}         # エージェントに追加で読ませるスキル。例: {coder: [.github/skills/our-rtos/SKILL.md]}
```

3. 利用者に尋ねて設定する。
   - チケットをどこで管理するか (GitHub / Redmine / どちらにも接続しない)。Redmine の場合は URL とプロジェクト識別子を設定し、API キーは利用者が自分で環境変数に入れるよう案内する (キーを尋ねない)。`python tools/redmine.py list` が通るかを確かめ、通らなければ原因を伝えて `manual` にするかを尋ねる。`manual` の場合は `.sec62443/outbox/` を `.gitignore` に追加する。チケットの雛形 (`.github/skills/iec62443/references/tracker.md` の「チケットの書き方」) を Redmine に登録するよう案内する。
   - 規格本文を参照させるか。使う場合は `standard_docs` を設定し、ディレクトリを作って `.gitignore` に追加する。置き方は `.github/skills/sec-requirements/references/standards.md` を案内する。
   - 社内規定を参照させるか。使う場合は `org_rules_dir` を設定し、見出し行だけの `index.md` を作る。git で管理するかどうかを尋ね、しない場合は `.gitignore` に追加する。登録は `org-rules add` で行うと案内する。
   - 以前のプロジェクトや別機種の資産を参照させるか。使う場合は `prior_dir` を設定し、見出し行だけの `index.md` を作る。git で管理するかどうかを尋ね、しない場合は `.gitignore` に追加する。登録は `prior add` で行うと案内する。
   - 既にある仕様書や設計書があれば `docs` の場所をそれに合わせる。Markdown 以外 (Word、Excel) の場合は、エージェントが読み書きできる形にする方法を相談する。
4. 見出しだけのファイルを作る: `.sec62443/rules.md` (「プロジェクト固有ルール」)、`.sec62443/lessons.md` (「学びの記録」)、`docs/security/gaps.md` (「未整合台帳」)。
5. `docs/security/trace.csv` をヘッダだけで作る: `ID,上流,CR,テスト,実装,状態,issue,PR`

文書のファイルは init では作らない。最初にその工程を実行したときに担当エージェントが作る。
