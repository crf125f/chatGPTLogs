承知しました！  
では、**改善ステップ①** から順に、1つずつ丁寧に進めていきます。  
本日はまず **「フォームクラス（`TodoForm`）の導入」** に取り組みましょう。

---

# ✅ 改善ステップ①：`TodoForm` の導入（モデルとフォームの責務分離）

---

## 🎯 改善の目的

| 現状の課題 | 解決方法 |
|------------|----------|
| `Todo` クラスがバリデーションとデータ保持の両方を担っている | 画面入力専用の `TodoForm` を作成し、役割を分離する |

---

## ✅ 実装手順（メソッドの実装 → 呼び出しの処理）

---

### 🔹① `form/TodoForm.java` を新規作成

```java
package com.example.todo.form;

import jakarta.validation.constraints.NotBlank;

public class TodoForm {

    @NotBlank(message = "タイトルを入力してください")
    private String title;

    private boolean done;

    public String getTitle() {
        return this.title;
    }

    public void setTitle(String title) {
        this.title = title;
    }

    public boolean isDone() {
        return this.done;
    }

    public void setDone(boolean done) {
        this.done = done;
    }
}
```

---

### 🔹② Controller の `updateTodo()` を修正

#### 📄 `TodoController.java`

```java
import com.example.todo.form.TodoForm;

@PostMapping("/todos/{id}/edit")
public String updateTodo(@PathVariable int id,
                         @Valid @ModelAttribute("todo") TodoForm form,
                         BindingResult bindingResult,
                         Model model) {

    if (bindingResult.hasErrors()) {
        return "edit";
    }

    // フォームの値を使ってTodoを更新
    this.todoService.updateTodo(id, form.getTitle(), form.isDone());
    return "redirect:/todos";
}
```

---

### 🔹③ Serviceの `updateTodo()` メソッドを変更

#### 📄 `TodoService.java`

```java
public void updateTodo(int id, String title, boolean done) {
    for (Todo todo : this.todoList) {
        if (todo.getId() == id) {
            todo.setTitle(title);
            todo.setDone(done);
            break;
        }
    }
}
```

---

### 🔹④ `edit.html` はそのままでOK（`th:object="${todo}"` がフォームを参照）

- `th:field="*{title}"` などは `TodoForm` にバインドされます

---

## ✅ 処理漏れチェックリスト

| チェック項目 | 内容 | 状態 |
|--------------|------|------|
| `TodoForm` を新設しているか | `form` パッケージに追加済み | ✅ |
| `@Valid` 対象が `TodoForm` に変更されているか | Controller修正済み | ✅ |
| `Todo` を操作するServiceが変更に対応済みか | 引数を `(id, title, done)` に変更済み | ✅ |
| テンプレートとバインドが整合しているか | `th:object="${todo}"` のままで動作 | ✅ |

---

## ✅ まとめ

| Before | After |
|--------|-------|
| `Todo` が画面バインディングとデータ保持を兼ねていた | `TodoForm` を分離し、責務を明確化 |
| Controllerがドメインを直接扱っていた | フォーム入力 → Serviceに変換 → ドメイン更新 |

---

このステップが完了すれば、次に  
▶️「改善ステップ②に進みたいです」  
とお知らせください！

ステップ②では、**バリデーションメッセージの外部化（ValidationMessages.propertiesの導入）** を予定しています。