了解しました。  
それではご依頼のとおり、**Step5-1「ToDoの完了状態を切り替える」機能**について、以下の構成で丁寧に解説・提示いたします。

---

# ✅ Step5-1：ToDoの完了状態を切り替える（トグル機能）

---

## 🎯 目的

ToDo一覧画面で「✔／×」をクリックすると、そのToDoの完了状態（`done`）が反転するようにする。

---

## 🧭 処理の流れ（図示）

```
[画面 list.html]
    ↓ POST /todos/{id}/toggle
[Controller (TodoController)]
    ↓ todoService.toggleDone(id)
[Service (TodoService)]
    ↓ id一致のToDoを検索 → doneを反転
    ↓
[redirect:/todos]
    ↓
[画面 list.html に戻る（状態が更新された一覧表示）]
```

---

## ✅ 処理に必要な前提（チェック）

| 要素              | チェック | 説明 |
|-------------------|----------|------|
| `Todo` に `id` フィールド | ✅ 済  | `int id` フィールドあり |
| `addTodo()` で一意なIDを採番 | ✅ 済  | `this.nextId++` でIDを付与（Step4で対応済） |
| `done` フィールド | ✅ 済  | 完了状態の真偽値を保持 |
| `/todos/{id}/toggle` にPOSTできる | 🚧 Step5-2で実装予定 | 本StepではControllerとService側を実装 |

---

## 🔧 変更点①：`TodoService.java` に完了状態トグルメソッドを追加

### ✨ 追加コード（完全なクラス）

```java
package com.example.todo.service;

import com.example.todo.model.Todo;
import org.springframework.stereotype.Service;

import java.util.ArrayList;
import java.util.List;

@Service
public class TodoService {

    private final List<Todo> todoList = new ArrayList<>();
    private int nextId = 1;

    public List<Todo> getTodoList() {
        return this.todoList;
    }

    public void addTodo(Todo todo) {
        todo.setId(this.nextId++);
        this.todoList.add(todo);
    }

    // 🔽 Step5-1：完了状態を切り替える処理
    public void toggleDone(int id) {
        for (Todo todo : this.todoList) {
            if (todo.getId() == id) {
                todo.setDone(!todo.isDone()); // true ⇄ false を反転
                break;
            }
        }
    }
}
```

### 🎯 処理の目的と説明：

- `id` に一致するToDoを `todoList` から見つける
- `done` フィールドの値を反転することで、✔／×を切り替える
- `this` をすべてのインスタンス変数に付けて明示（ご指定どおり）

---

## 🔧 変更点②：`TodoController.java` にPOSTエンドポイントを追加

### ✨ 追加コード（完全なクラス）

```java
package com.example.todo.controller;

import com.example.todo.model.Todo;
import com.example.todo.service.TodoService;
import jakarta.validation.Valid;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.validation.BindingResult;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@Controller
public class TodoController {

    @Autowired
    private TodoService todoService;

    @GetMapping("/todos")
    public String showTodoList(Model model) {
        List<Todo> todos = this.todoService.getTodoList();
        model.addAttribute("todos", todos);
        model.addAttribute("todo", new Todo());
        return "list";
    }

    @PostMapping("/todos")
    public String addTodo(@ModelAttribute("todo") @Valid Todo todo,
                          BindingResult bindingResult,
                          Model model) {
        if (bindingResult.hasErrors()) {
            model.addAttribute("todos", this.todoService.getTodoList());
            return "list";
        }
        this.todoService.addTodo(todo);
        return "redirect:/todos";
    }

    // 🔽 Step5-1：完了状態を切り替えるエンドポイント
    @PostMapping("/todos/{id}/toggle")
    public String toggleTodo(@PathVariable int id) {
        this.todoService.toggleDone(id);
        return "redirect:/todos";
    }
}
```

### 🎯 処理の目的と説明：

- `@PathVariable int id` でURL中の `{id}` を取得
- Serviceに `id` を渡して状態反転
- PRGパターンで `redirect:/todos` にリダイレクト

---

## ✅ Step5-1 のまとめ（処理の目的と整合性チェック）

| 処理 | 実装済 | 目的 |
|------|--------|------|
| 完了状態トグル処理（Service） | ✅ | `done` を true⇄false で切り替え |
| トグル受け口（Controller） | ✅ | 対象ToDoの状態変更を実行 |
| IDでの一意識別（前提） | ✅ | Step4で `this.nextId++` により実装済 |
| 漏れの有無 | なし | 表示・入力・ロジックの整合性がとれている |

---

次のStepでは、**Step5-2（画面側でトグル用ボタンを追加）**を実装します。  
進めてよろしければ「Step5-2に進みたいです」とお伝えください 🙌