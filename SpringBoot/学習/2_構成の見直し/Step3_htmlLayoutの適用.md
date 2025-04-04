了解しました！  
では、**改善ステップ③：HTMLテンプレートの共通レイアウト化（ヘッダー・フッターの部品化）** に進みましょう。

---

# ✅ 改善ステップ③：テンプレート共通化（ヘッダー・レイアウトの部品化）

---

## 🎯 改善の目的

| 課題 | 解決方法 |
|------|----------|
| 各HTMLファイルに `<html>`, `<head>`, `<body>` を毎回書いている | 共通レイアウトに切り出して `th:replace` や `th:fragment` で再利用する |

---

## ✅ 実装手順（部品の作成 → 各画面への適用）

---

### 🔹① 共通レイアウトファイルを作成

#### 📄 `templates/layout.html`（新規）

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title th:replace="~{::title}">ToDoアプリ</title>
    <link rel="stylesheet" th:href="@{/css/style.css}" />
</head>
<body>
    <header>
        <h1>ToDoアプリ</h1>
        <hr/>
    </header>

    <main th:replace="~{::section}">
        <!-- 各ページの本文 -->
    </main>

    <footer>
        <hr/>
        <p>&copy; 2025 ToDo App</p>
    </footer>
</body>
</html>
```

---

### 🔹② `list.html` を共通レイアウトに差し込む形式に変更

#### 📄 `list.html`（変更後）

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org"
      th:replace="layout :: layout">
<head>
    <title>ToDo一覧</title>
</head>
<body>
<section>
    <table border="1">
        <tr>
            <th>ID</th>
            <th>タイトル</th>
            <th>完了</th>
            <th>操作</th>
        </tr>
        <tr th:each="todo : ${todoList}">
            <td th:text="${todo.id}">1</td>
            <td th:text="${todo.title}">タイトル</td>
            <td>
                <form th:action="@{'/todos/' + ${todo.id} + '/toggle'}" method="post">
                    <button type="submit" th:text="${todo.done} ? '✔' : '✗'"></button>
                </form>
            </td>
            <td>
                <a th:href="@{'/todos/' + ${todo.id} + '/edit'}">編集</a>
                <form th:action="@{'/todos/' + ${todo.id} + '/delete'}" method="post" style="display:inline">
                    <button type="submit">削除</button>
                </form>
            </td>
        </tr>
    </table>
</section>
</body>
</html>
```

> `th:replace="layout :: layout"` によって、`layout.html` の構造が適用されます。

---

### 🔹③ `edit.html` も同様に適用可能

必要に応じて、`edit.html` にも `section` を作成して共通レイアウトに対応させます。

---

## ✅ 処理漏れチェック

| チェック項目 | 内容 | 状態 |
|--------------|------|------|
| `layout.html` が `templates` に存在するか | 共通構造を定義済み | ✅ |
| `th:replace` が `list.html` に記述されているか | レイアウト適用 | ✅ |
| `<section>` タグで本文を囲っているか | 内容の差し込み対象 | ✅ |
| `<head>`, `<body>` を重複していないか | 共通HTMLで定義済み | ✅ |

---

## ✅ 効果まとめ

| Before | After |
|--------|-------|
| 各HTMLに同じタグを繰り返し記述 | 共通テンプレートに切り出して再利用 |
| タイトル・構造が各ページでバラバラ | 一貫性のあるUIへ改善 |

---

次に進む準備ができたら、  
▶️「改善ステップ④に進みたいです」  
とお伝えください！

次は、**Flashメッセージの導入（登録・更新・削除完了を一時的に表示）**を扱う予定です。