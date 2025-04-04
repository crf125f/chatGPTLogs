了解しました！  
では、**改善ステップ②：バリデーションメッセージの外部化（`ValidationMessages.properties` の導入）** に進みます。

---

# ✅ 改善ステップ②：バリデーションメッセージの外部化（国際化対応）

---

## 🎯 改善の目的

| 課題 | 解決方法 |
|------|----------|
| `@NotBlank(message = "...")` で **直接エラーメッセージを記述している** | `ValidationMessages.properties` を使って外部化・共通化することで、保守性と多言語対応が向上する |

---

## ✅ 実装手順（定義 → 使用するクラスでの利用）

---

### 🔹① メッセージ定義ファイルを作成

#### 📄 `src/main/resources/ValidationMessages.properties`（新規作成）

```properties
NotBlank.todoForm.title=タイトルを入力してください
```

---

### 🔹② `TodoForm` のアノテーションを外部キーで書き換える

#### 📄 `TodoForm.java`

```java
@NotBlank(message = "{NotBlank.todoForm.title}")
private String title;
```

- `{...}` の形式で、メッセージプロパティファイルのキーを参照します

---

### 🔹③ Spring Boot 側でこのメッセージファイルを読み込ませる（※通常は不要）

**Spring Bootはデフォルトで `ValidationMessages.properties` を自動で読み込みます。**  
ただし、名前や場所を変える場合は、`application.properties` に以下を追記：

```properties
spring.messages.basename=ValidationMessages
```

（今回はデフォルト名・場所のままなので設定不要です）

---

## ✅ 処理漏れチェック

| チェック項目 | 内容 | 状態 |
|--------------|------|------|
| `ValidationMessages.properties` が正しく作成されているか | `resources` 配下 | ✅ |
| メッセージキーと `@NotBlank` の指定が一致しているか | `message="{NotBlank.todoForm.title}"` | ✅ |
| メッセージの文字化け対策（UTF-8） | ファイルのエンコーディングがUTF-8 | ✅ or IDEで確認 |
| アプリ再起動後に反映されるか | キャッシュクリアのため、起動し直すこと | ✅ |

---

## ✅ 効果まとめ

| Before | After |
|--------|-------|
| メッセージがJavaコード内に直接書かれていた | `ValidationMessages.properties` に外出し |
| メンテナンスしづらく、再利用不可 | 多言語対応・再利用可能に |

---

次に進む準備ができたら  
▶️「改善ステップ③に進みたいです」  
とお知らせください！

次は、**画面テンプレートの共通レイアウト化（header/footerの分離）** に進む予定です。