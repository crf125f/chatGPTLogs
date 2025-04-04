了解しました！  
それでは、**Step6-3：ToDo編集内容の保存処理（更新処理）**に進みます。  

今回は、前回（Step6-2）で表示した編集フォームの入力結果を、**実際に保存して一覧画面に反映**する処理を実装します。

---

# ✅ Step6-3：ToDoの編集内容を保存して一覧に反映する

---

## 🎯 処理の目的（なぜ必要か？）

前のStep6-2では、ToDo編集用の画面（`edit.html`）に既存データを表示しました。  
このStepでは、**ユーザーが編集したデータを保存して反映させる**ことで、「編集機能」を完結させます。

---

## 🔁 処理の流れ（図解）

```
[編集画面でタイトルなどを修正して「更新」]
        ↓
[POST /todos/{id}/edit]
        ↓
[Controllerでフォーム入力を受け取る]
        ↓
[Serviceで対象ToDoを探し、内容を上書き]
        ↓
[redirect:/todos → 一覧を再表示]
```

---

## ✅ 変更点とコード（処理単位で丁寧に解説）

---

### 🔹【1】Controller に編集処理用 POST メソッドを追加

#### 📄 `TodoController.java`

```java
@PostMapping("/todos/{id}/edit")
public String updateTodo(@PathVariable int id,
                         @ModelAttribute("todo") Todo updatedTodo) {
    this.todoService.updateTodo(id, updatedTodo);
    return "redirect:/todos";
}
```

#### 📘 処理内容の解説：

- `@PathVariable int id`  
　→ URLの `{id}` を受け取る（編集対象のToDoを識別）
- `@ModelAttribute("todo") Todo updatedTodo`  
　→ フォームから送信された内容を受け取る
- `todoService.updateTodo(id, updatedTodo)`  
　→ サービス層に更新処理を依頼
- `redirect:/todos`  
　→ PRGパターンで一覧に戻る

---

### 🔹【2】Service に更新処理メソッドを追加

#### 📄 `TodoService.java`

```java
public void updateTodo(int id, Todo updatedTodo) {
    for (Todo todo : this.todoList) {
        if (todo.getId() == id) {
            todo.setTitle(updatedTodo.getTitle());
            todo.setDone(updatedTodo.isDone());
            break;
        }
    }
}
```

#### 📘 処理内容の解説：

- `todoList` の中から、IDが一致するToDoを見つけ出し、
- `title` と `done` を、フォームから送信された内容で上書きします
- このとき、`id` は更新しません（識別用なので固定）

---

### 🔹【3】edit.html のフォームはそのまま使用OK

#### 📄 `edit.html`（Step6-2で作成済み）

```html
<form th:action="@{'/todos/' + ${todo.id} + '/edit'}" method="post" th:object="${todo}">
    <input type="text" th:field="*{title}" />
    <input type="checkbox" th:field="*{done}" />
    <button type="submit">更新</button>
</form>
```

- すでに `POST /todos/{id}/edit` に送信されるよう設計済み
- `th:field` により、フォームデータが `@ModelAttribute` にマッピングされる

---

## ✅ 処理漏れチェック・前提確認

| 項目 | 内容 | 状態 |
|------|------|------|
| 編集画面が表示されるか | Step6-2 で対応済み | ✅ |
| 編集フォームが入力を送信するか | th:field + POST送信で対応済み | ✅ |
| 入力内容を受け取るControllerがあるか | `updateTodo()` を新規追加 | ✅ |
| Serviceで該当ToDoを更新できるか | `updateTodo(id, updatedTodo)` を追加 | ✅ |
| PRGでリダイレクトしているか | `redirect:/todos` にて対応 | ✅ |

---

## ✅ Step6-3 のまとめ

| 項目 | 内容 |
|------|------|
| 処理内容 | 編集フォームの入力内容を反映させる |
| 主な変更 | ControllerとServiceに編集処理を追加 |
| 状態 | 編集機能が完成。登録 → 表示 → 編集の流れがつながった |
| 次のステップ | バリデーション・エラーハンドリング（Step6-4）

---

次に進む準備ができたら、  
▶️「Step6-4に進みたいです」  
とお伝えください！

Step6の最終ステップとして、**編集時にもバリデーション（未入力チェック）を適用し、エラー表示を行う**方法を学びます。