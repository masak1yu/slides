---
marp: true
theme: default
paginate: true
style: |
  section {
    font-family: 'Helvetica Neue', Arial, sans-serif;
  }
  section.lead {
    text-align: center;
  }
  h1 { color: #c0392b; }
  h2 { color: #2c3e50; border-bottom: 2px solid #c0392b; padding-bottom: 4px; }
  code { background: #f4f4f4; padding: 2px 6px; border-radius: 3px; }
  pre code { background: none; padding: 0; }
  .profile-img {
    position: absolute;
    bottom: 40px;
    right: 40px;
    width: 120px;
    height: 120px;
    border-radius: 50%;
    object-fit: cover;
  }
---

<!-- _class: lead -->

# Railsの基本作法に従いつつもRidgepoleみたいなことがしたい

**masak1yu** / 2026.4.3

---

## self.inspect

<img class="profile-img" src="masak1yu.jpg" />

- **masak1yu**
- 作ったGemの話

---

## どんなGem?

- Schemaのmigrationを少し便利にする

---

## Do you know Ridgepole?

Schemafile を書けばスキーマ管理が楽になるGem

```ruby
# Schemafile
create_table :users do |t|
  t.string  :name, null: false
  t.string  :email, null: false
  t.timestamps
end
```

- こんなふうにSchemafileに正の定義を書いて、DBに**直接**適用する
- DBとの差分はいい感じにGemが吸収してくれる

---

## デメリット

- Railsの作法からは外れる
- 一度使うと元のMigrationに戻すのがちょっと手間
  - schema_migrationsに履歴が残らない

---

## migsupo とは

> Ridgepole と Rails の migration の落とし所

Schemafile と現在の DB の**差分から migration ファイルを自動生成**する gem

---

## 使い方

**1. Schemafile を書く**

```ruby
create_table :articles do |t|
  t.string :title, null: false
  t.text   :body
  t.timestamps
end
```

**2. コマンドを叩く**

```bash
rails db:generate_migration
```

**3. 生成された migration を普通に `rails db:migrate`**

---

## 3つのコマンド

| コマンド | 説明 |
|---------|------|
| `rails db:generate_migration` | migration ファイルを生成 |
| `rails db:generate_migration:diff` | 差分をプレビュー（ファイル生成なし） |
| `rails db:generate_migration:check` | CI 用チェック |

---

## Ridgepole との比較

|  | **migsupo** | Ridgepole |
|--|-------------|-----------|
| スキーマ定義 | Schemafile | Schemafile |
| 変更の反映 | migration ファイル生成 | DB に直接適用 |
| Rails の慣習 | ✅ 守る | ⚠️ 外れる |

---

## カラムリネームも安全に

```ruby
# 設定ファイルでリネームのヒントを指定
config.rename_column_hints = {
  users: { username: :name }
}
```

ヒントなし → DELETE + CREATE として扱う
ヒントあり → `rename_column` migration を生成

**データが消えない**

---

## まとめ

- ✅ Schemafile を書くだけで migration が自動生成
- ✅ Rails の migration ワークフローをそのまま維持
- ✅ Ridgepole からの移行もしやすい構文

---

<!-- _class: lead -->

## `gem 'migsupo'`

**GitHub:** [masak1yu/migsupo](https://github.com/masak1yu/migsupo)

フィードバック・Issue・PR 歓迎
