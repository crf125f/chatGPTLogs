了解しました！  
それでは、**Step6-4：編集フォームにバリデーションとエラー表示を追加する** に進みます。  

今回は、「ToDoの編集時にも未入力などのエラーを検知し、適切に画面に戻してメッセージを表示する」処理を実装します。

---

# ✅ Step6-4：編集フォームにバリデーションとエラー表示を追加する

---

## 🎯 処理の目的（なぜ必要か？）

- 編集フォームで `タイトルが空のまま更新された` といった **不正な入力を防ぎたい**
- ユーザーに **具体的なエラーメッセージを表示したい**
- 登録時（Step4）と同様に、**`@Valid` + `BindingResult` を使って検証する**

---

## ✅ 実装の流れ（前提：タイトルには `@NotBlank` が付与されている）

```
[ユーザーが編集フォームで空欄のまま「更新」]
        ↓
[POST /todos/{id}/edit に送信される]
        ↓
[Controllerでバリデーション (@Valid)]
        ↓
[エラーがある → 編集画面に戻す＋メッセージ表示]
        ↓
[エラーがない → 更新 → リダイレクト]
```

---

## ✅ 実装手順（メソッドの実装 → 呼び出しの順）

---

### 🔹① Service側のメソッドは既に完成済みなので変更なし

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

---

### 🔹② Controller：バリデーション付きの更新処理に変更

#### 📄 `TodoController.java`

```java
@PostMapping("/todos/{id}/edit")
public String updateTodo(@PathVariable int id,
                         @Valid @ModelAttribute("todo") Todo updatedTodo,
                         BindingResult bindingResult,
                         Model model) {

    if (bindingResult.hasErrors()) {
        return "edit";  // エラーがあればそのまま編集画面に戻す
    }

    this.todoService.updateTodo(id, updatedTodo);
    return "redirect:/todos";
}
```

#### 📘 処理内容の説明：

- `@Valid` でフォーム入力内容を検証
- `BindingResult` にエラー情報が入る
- `hasErrors()` が `true` の場合、再度 `edit.html` を表示
- `edit.html` に `th:errors` を記述しておくことで、メッセージも表示可能

---

### 🔹③ 編集テンプレート：エラーメッセージを表示

#### 📄 `edit.html`

```html
<form th:action="@{'/todos/' + ${todo.id} + '/edit'}" method="post" th:object="${todo}">
    <label>タイトル：</label>
    <input type="text" th:field="*{title}" />
    <div th:if="${#fields.hasErrors('title')}" th:errors="*{title}" style="color: red;"></div>
    <br/>

    <label>完了？：</label>
    <input type="checkbox" th:field="*{done}" />
    <br/>

    <button type="submit">更新</button>
</form>
```

#### 📘 処理内容の説明：

- `th:errors="*{title}"` により、`title` フィールドのエラーがあれば表示
- `#fields.hasErrors()` で条件付き表示が可能
- 入力欄は `th:field` なので、バリデーション連携もバッチリ

---

## ✅ 処理漏れチェック・前提確認

| 項目 | 内容 | 状態 |
|------|------|------|
| `@Valid` の導入 | Controllerに追加済み | ✅ |
| `BindingResult` の使用 | メソッド引数で受け取り済み | ✅ |
| エラー時に画面を再表示できるか | `return "edit"` にて対応 | ✅ |
| HTMLにエラー表示の記述があるか | `th:errors` で実装済み | ✅ |
| `@NotBlank` アノテーションがあるか | Step4ですでに付与済み | ✅ |

---

## ✅ Step6-4 のまとめ

| 項目 | 内容 |
|------|------|
| 処理内容 | 編集フォームにバリデーションを追加し、未入力エラーを検出＆表示 |
| 実装箇所 | Controllerのバリデーション処理、HTMLのエラーメッセージ表示 |
| 状態 | 編集機能に対する入力チェックが完了し、ユーザーにとって安心なUIに |
| 今後の拡張 | 複数項目の検証、独自メッセージ、共通レイアウトなど

---

これで Step6 はすべて完了です！🎉  
次に進む場合は、▶️「Step7に進みたいです」とお伝えください。  
もしくは「Step6をまとめて見直したい」などのご要望も歓迎です！