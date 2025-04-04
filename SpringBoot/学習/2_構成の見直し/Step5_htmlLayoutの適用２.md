了解しました！  
それでは、**改善ステップ⑤：編集処理のHTML構造を共通レイアウト対応に見直す** に進みます。

---

# ✅ 改善ステップ⑤：`edit.html` を共通レイアウト構成に対応させる

---

## 🎯 改善の目的

| 課題 | 解決方法 |
|------|----------|
| `edit.html` が独立したHTML構造を持ち、共通レイアウト（`layout.html`）と統一感がない | `layout.html` をベースに `edit.html` を差し込み式に変更し、ヘッダー・フッターの再利用を実現する |

---

## ✅ 実装手順（編集画面のテンプレート構造変更）

---

### 🔹① 現在の `layout.html` の構成確認（差し込み先）

```html
<main th:replace="~{::section}">
    <!-- 各画面の本文がここに入る -->
</main>
```

↑これに差し込むには、`edit.html` に `<section>` を定義する必要があります。

---

### 🔹② `edit.html` の変更後コード（共通レイアウト対応）

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org"
      th:replace="layout :: layout">
<head>
    <title>ToDo編集</title>
</head>
<body>
<section>
    <h2>ToDo編集</h2>

    <form th:action="@{'/todos/' + ${todo.id} + '/edit'}" method="post" th:object="${todo}">
        <label>タイトル：</label>
        <input type="text" th:field="*{title}" />
        <div th:if="${#fields.hasErrors('title')}" th:errors="*{title}" style="color: red;"></div>
        <br/>

        <label>完了？：</label>
        <input type="checkbox" th:field="*{done}" />
        <br/><br/>

        <button type="submit">更新</button>
        <a th:href="@{/todos}">戻る</a>
    </form>
</section>
</body>
</html>
```

---

## ✅ 処理漏れチェックリスト

| チェック項目 | 内容 | 状態 |
|--------------|------|------|
| `layout.html` が定義されているか | ✅ すでに導入済み |
| `edit.html` の `<html>` に `th:replace="layout :: layout"` があるか | ✅ |
| 本文を `<section>` タグで囲っているか | ✅ |
| `<head>` 内に `<title>` を記述しているか | ✅（画面タイトルとして反映） |

---

## ✅ 効果まとめ

| Before | After |
|--------|-------|
| 単独構造で共通部分の重複が多い | `layout.html` に差し込む形で共通化＆統一感アップ |
| 表示見た目が他ページとバラバラ | 同じヘッダー・フッターが使われ、UIが一貫 |

---

このステップで、編集処理の**画面側の構造改善が完了**しました。  
次に進む場合は  
▶️「改善ステップ⑥に進みたいです」  
とお伝えください！

次は、**編集処理のバリデーションエラー発生時にもFlashメッセージや画面遷移が正しく動作するか**の確認と微調整を予定しています。