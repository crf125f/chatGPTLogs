了解しました！  
それでは、**Step6-2：ToDoの編集画面を表示する機能**に進みます。  
以下の方針に基づいて、**丁寧にステップ解説＋変更点のコード提示＋目的と前提条件の確認**を行います。

---

# ✅ Step6-2：ToDoの編集画面を表示できるようにする

---

## 🎯 処理の目的（なぜ必要か？）

ToDoの内容を変更したいとき、  
✔︎ 編集用の画面を表示し、  
✔︎ そのToDoの情報（タイトルや完了状態）をフォームに表示（バインド）できる必要があります。

> 今回のStepでは「編集対象を画面に表示する」ところまでを扱います。  
次のStep6-3で「編集内容を保存（更新）」します。

---

## 🔁 処理の流れ（図示）

```
[一覧画面で「編集」ボタンをクリック]
        ↓
[GET /todos/{id}/edit に遷移]
        ↓
[ControllerがIDを受け取り、ToDoを取得]
        ↓
[編集画面（edit.html）にToDoを渡す]
        ↓
[フォームに既存の値を表示]
```

---

## ✅ 変更点と処理内容（1つずつ解説）

---

### 🔹【1】編集用テンプレート `edit.html` を新規作成

#### 📄 `src/main/resources/templates/edit.html`

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>ToDo編集</title>
</head>
<body>
<h1>ToDo編集</h1>

<form th:action="@{'/todos/' + ${todo.id} + '/edit'}" method="post" th:object="${todo}">
    <label>タイトル：</label>
    <input type="text" th:field="*{title}" />
    <br/>
    <label>完了？：</label>
    <input type="checkbox" th:field="*{done}" />
    <br/>
    <button type="submit">更新</button>
</form>

<a href="/todos">戻る</a>
</body>
</html>
```

#### 📘 説明：

- `th:object="${todo}"`：編集対象のToDoをフォームにバインド
- `th:field="*{title}"`：既存のタイトルを入力欄に反映
- `th:field="*{done}"`：完了状態をチェックボックスで表示
- `@{'/todos/' + ${todo.id} + '/edit'}`：編集結果をPOSTするURL（Step6-3で実装）

---

### 🔹【2】Controllerに GET `/todos/{id}/edit` を追加

#### 📄 `TodoController.java`

```java
@GetMapping("/todos/{id}/edit")
public String showEditForm(@PathVariable int id, Model model) {
    Todo todo = this.todoService.findById(id);
    model.addAttribute("todo", todo);
    return "edit";
}
```

#### 📘 説明：

- URLパスの `{id}` を `@PathVariable` で受け取り、
- ServiceでIDに一致するToDoを取得し、
- 編集用テンプレート `edit.html` に渡します。

---

### 🔹【3】Serviceに `findById()` を追加

#### 📄 `TodoService.java`

```java
public Todo findById(int id) {
    for (Todo todo : this.todoList) {
        if (todo.getId() == id) {
            return todo;
        }
    }
    throw new IllegalArgumentException("IDが見つかりません: " + id);
}
```

#### 📘 説明：

- ToDo一覧から `id` に一致するToDoを探して返す。
- 見つからなければ例外を投げる（今回は簡易実装、必要に応じて404ページへ）。

---

### 🔹【4】一覧画面に「編集」リンクを追加

#### 📄 `list.html` の行に追加：

```html
<td>
    <a th:href="@{'/todos/' + ${todo.id} + '/edit'}">編集</a>
</td>
```

#### 📘 説明：

- 一覧表示中の各ToDoに、対応する「編集」リンクを付与します。
- クリックすると、該当ToDoの編集フォームに遷移します。

---

## ✅ 処理漏れチェックと前提確認

| 項目 | 内容 | 状態 |
|------|------|------|
| ToDoにIDがあるか | Step4で自動採番実装済み | ✅ |
| IDでToDoを取得できるか | `findById(int id)` 実装済み | ✅ |
| 編集画面があるか | `edit.html` 作成済み | ✅ |
| 編集フォームに値が表示されるか | `th:field` + `th:object` 使用済み | ✅ |
| GET → POST の流れを作れているか | POSTはStep6-3で実装予定 | ⏳（次Step） |

---

## ✅ Step6-2 のまとめ

| 項目 | 内容 |
|------|------|
| 処理内容 | 編集用フォームを表示する |
| 目的 | ユーザーがToDoを修正する準備を整える |
| 主要変更 | `edit.html` 追加、Controllerに `GET /todos/{id}/edit` を実装、Serviceに `findById()` を追加 |
| 状態 | 編集画面表示まで完了、次は編集結果の保存へ |

---

🧩 なぜSpringはこれを自動で注入できるのか？
Spring MVCでは、次のような仕組みでメソッドの引数を処理します：

引数の型	Springがやってくれること
@PathVariable int id	URLの {id} をintに変換して渡す
Model model	表示用のデータを格納する Model を自動生成して渡す
@RequestParam	クエリパラメータを受け取る
@ModelAttribute	フォーム送信データをオブジェクトに詰めて渡す
→ つまり、Springがコントローラーメソッドを解析し、必要なものを揃えてくれる仕組みになっています。

次に進む準備ができたら、  
▶️「Step6-3に進みたいです」  
とお伝えください！

そこでは、**編集された内容を保存して一覧に反映する処理（POST）** を実装します。