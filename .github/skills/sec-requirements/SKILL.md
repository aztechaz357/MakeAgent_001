---
name: sec-requirements
description: IEC 62443-4-2 の該当 CR と強化要件の選定、脅威分析、製品セキュリティ要求仕様書 (REQ) の作成と要求の不備の洗い出し。req-analyzer と req-reviewer が使う。
user-invocable: false
---

# 要求の分析

先に `.github/skills/sec-base/SKILL.md` を読んでおく。

## CR の選定

1. [references/cr-map.md](references/cr-map.md) の該当する FR の節だけを読み、依頼に当てはまる CR を選ぶ。project.yml の `target_sl` で必要な RE まで含める。
2. `standard_docs` が空でなければ [references/standards.md](references/standards.md) に従い、選んだ CR の本文も読む。
3. `rules.md` や issue に SL の例外や対象外の指定があればそれに従う。
4. 該当する CR がないと判断したら、その理由を報告に書く。

cr-map.md は独自要約で、規格原本との照合が済んでいない項目がある。SL の割当が判断を左右するときは、原本の確認を「依頼・質問」に書く。

## 要求の分析

依頼、issue、既にある上流の資料を読み、次を洗い出す。

- **抜け**: 選んだ CR に対して要求がない。異常時、失敗時、権限ごとの振る舞いが決まっていない
- **あいまいさ**: 数値や条件がない。複数に読める
- **矛盾**: 要求どうし、制御性能や機能安全の要求との衝突
- **実現の前提**: ハードや利用環境に求めること

利用者 (設計者) が決めるべきことは、選択肢と推奨を付けて「依頼・質問」に書く。自分で決めて進めるときは仮定として未整合台帳に載せる。

## 製品セキュリティ要求仕様書 (REQ)

- 項目: 守る資産と利用者の役割、CR ごとの要求、想定する利用環境と利用者に求める条件、対象外とその理由。
- 何を守るかを書き、どう実現するかは書かない。
- 下流 (機能仕様、設計、コード) から足りない要求を指摘されたときは、`仮` の項目として追加する。

## standard で加えること

- [references/threat.md](references/threat.md) の脅威分析。
- [references/profiles.md](references/profiles.md) の該当製品の節。

## レビューで見ること (req-reviewer)

- 選んだ CR と RE に漏れがないか。対象外の理由が妥当か
- 各項目が検証できる形か。1 項目 1 要求か
- 脅威 (standard) に対して要求があるか
- 仮定と未決事項が未整合台帳に載っているか
