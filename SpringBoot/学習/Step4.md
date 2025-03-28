承知しました！  
では、**Step4：ToDo登録機能の追加**を、はじめから丁寧に解説＋コード提示していきます。  
加えて、**`@NotBlank`を使うための準備（依存関係の追加）**についても明記します。

---

## ✅ Step4：ToDo登録フォームを追加する（完全版）

### 🎯 ゴール

- 画面からToDo（タイトル）を入力・登録できるようにする
- フォーム入力値 → Controllerで受け取り → Serviceで保持 → 一覧に表示
- バリデーション（未入力チェック）も追加

---

## ① 事前準備：`@NotBlank` を使えるようにする

### ✅ pom.xml に依存関係を追加

`pom.xml` に以下の依存関係が **ない場合は追加してください**：

```xml
<!-- バリデーション用（@NotBlank などが使える） -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-validation</artifactId>
</dependency>
```

> ✅ Spring Boot 3以降は `jakarta.validation` パッケージを使用します。

---

## ② `Todo.java`（モデル）を修正

```java
package com.example.todo.model;

import jakarta.validation.constraints.NotBlank;

public class Todo {

    @NotBlank(message = "タイトルは必須です")  // 🔽 入力チェックを追加
    private String title;

    private boolean done;

    public String getTitle() {
        return title;
    }

    public void setTitle(String title) {
        this.title = title;
    }

    public boolean isDone() {
        return done;
    }

    public void setDone(boolean done) {
        this.done = done;
    }
}
```

---

## ③ `TodoService.java` に追加機能を実装

```java
package com.example.todo.service;

import com.example.todo.model.Todo;
import org.springframework.stereotype.Service;

import java.util.ArrayList;
import java.util.List;

@Service
public class TodoService {

    private List<Todo> todoList = new ArrayList<>();

    public List<Todo> getTodoList() {
        return todoList;
    }

    public void addTodo(Todo todo) {  // 🔽 追加されたToDoをリストに保存
        todoList.add(todo);
    }
}
```

---

## ④ `TodoController.java` にフォーム処理を追加

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

@Controller
public class TodoController {

    @Autowired
    private TodoService todoService;

    @GetMapping("/todos")
    public String showTodos(Model model) {
        model.addAttribute("todos", todoService.getTodoList());
        model.addAttribute("todo", new Todo());  // 🔽 フォーム用の空オブジェクト
        return "list";
    }

    @PostMapping("/todos")
    public String addTodo(@ModelAttribute("todo") @Valid Todo todo,
                          BindingResult bindingResult,
                          Model model) {

        if (bindingResult.hasErrors()) {
            // 🔽 入力エラー時は一覧とともにフォームを再表示
            model.addAttribute("todos", todoService.getTodoList());
            return "list";
        }

        todoService.addTodo(todo);
        return "redirect:/todos";  // 🔽 登録後はリダイレクト（再読み込み防止）
    }
}
```

---

## ⑤ `list.html` にフォームを追加

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>ToDo List</title>
</head>
<body>
<h1>ToDo List</h1>

<!-- 🔽 ToDo登録フォーム -->
<form action="/todos" method="post" th:object="${todo}">
    <input type="text" th:field="*{title}" placeholder="ToDoを入力してください"/>
    <div th:if="${#fields.hasErrors('title')}" th:errors="*{title}" style="color: red"></div>
    <button type="submit">追加</button>
</form>

<!-- 🔽 一覧表示 -->
<table>
    <thead>
    <tr>
        <th>タイトル</th>
        <th>完了</th>
    </tr>
    </thead>
    <tbody>
    <tr th:each="todo : ${todos}">
        <td th:text="${todo.title}">タイトル</td>
        <td th:text="${todo.done}">完了</td>
    </tr>
    </tbody>
</table>

</body>
</html>
```

---

## ✅ 動作確認チェックリスト

| 項目 | 内容 |
|------|------|
| `/todos` にアクセスできるか？ | フォームと一覧が表示される |
| ToDoを入力して追加ボタン | 一覧に追加される |
| 入力せずに送信 | エラーメッセージが表示される |
| ブラウザリロード | 重複登録されない（POST-Redirect-GET） |

---

ご確認のうえ、次に進む準備ができたら  
➡️「Step5に進みたいです」と言ってくださいね！

気になる箇所・再確認したいところがあれば、どこでも掘り下げます 💡

かしこまりました！  
以下に、**Step4で追加・変更した箇所**をすべてリストアップし、**一つひとつ丁寧に「何をしているのか／なぜ必要なのか」**を解説していきます。

---

## ✅ Step4：変更箇所の詳細解説

---

### 🔹【1】pom.xml に以下の依存関係を追加

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-validation</artifactId>
</dependency>
```

### 📘 意味と目的：

- `@NotBlank` などの **バリデーション用アノテーション**を使うには、これが必要です。
- Spring Boot 3.x では **`jakarta.validation` パッケージ**に属しており、この依存関係がないとコンパイルエラーになります。

---

### 🔹【2】`Todo.java` にアノテーションを追加

```java
import jakarta.validation.constraints.NotBlank;
```

```java
@NotBlank(message = "タイトルは必須です")
private String title;
```

### 📘 意味と目的：

- `@NotBlank` は、**文字列がnullでも空でもないことをチェック**するバリデーションアノテーションです。
- この例では、ToDoタイトルが未入力で送信された場合に、`"タイトルは必須です"`というエラーを出します。
- **バリデーションメッセージを自動で表示できるようになる**ため、入力チェックに必須です。

---

### 🔹【3】`TodoService.java` に `addTodo()` メソッドを追加

```java
public void addTodo(Todo todo) {
    todoList.add(todo);
}
```

### 📘 意味と目的：

- **新しく入力されたToDoを一覧（`todoList`）に追加する処理**です。
- `todoList` はメモリ上に保持されているListで、画面表示の元データです。
- 永続化（DB保存）はしていないシンプルな構成なので、Listに足すだけでOK。

---

### 🔹【4】`TodoController.java`（GET）に下記を追加

```java
model.addAttribute("todo", new Todo());
```

### 📘 意味と目的：

- `list.html` の `<form th:object="${todo}">` で使う、**空のToDoオブジェクト**を画面に渡しています。
- Spring MVCでは、`th:object` に渡すオブジェクトが `model.addAttribute()` で提供されていないと、画面でバインディングエラーになります。

---

### 🔹【5】`TodoController.java` に POST 処理を追加

```java
@PostMapping("/todos")
public String addTodo(@ModelAttribute("todo") @Valid Todo todo,
                      BindingResult bindingResult,
                      Model model) {
    if (bindingResult.hasErrors()) {
        model.addAttribute("todos", todoService.getTodoList());
        return "list";
    }
    todoService.addTodo(todo);
    return "redirect:/todos";
}
```

### 📘 意味と目的：

- `@PostMapping("/todos")`  
  → フォーム送信時のリクエスト（POST /todos）を受け取るエントリーポイント。

- `@ModelAttribute("todo")`  
  → フォーム入力値を `Todo` オブジェクトにマッピングします（Springが自動でバインド）。

- `@Valid`  
  → `@NotBlank` などのバリデーションアノテーションを**有効にするために必須**です。

- `BindingResult`  
  → バリデーション結果を格納します。エラーがあれば `hasErrors()` が true になります。

- `return "redirect:/todos";`  
  → 登録完了後に GET `/todos` にリダイレクトします（これが「画面リロード時の再投稿防止」になります）。

---

### 🔹【6】`list.html` にフォームを追加

```html
<form action="/todos" method="post" th:object="${todo}">
    <input type="text" th:field="*{title}" placeholder="ToDoを入力してください"/>
    <div th:if="${#fields.hasErrors('title')}" th:errors="*{title}" style="color: red"></div>
    <button type="submit">追加</button>
</form>
```

### 📘 意味と目的：

- `<form action="/todos" method="post" th:object="${todo}">`  
  → 登録先は `/todos`。`th:object` でフォームと `Todo` オブジェクトをバインドします。

- `th:field="*{title}"`  
  → 入力値が `todo.title` に自動的にセットされます。

- `<div th:errors="*{title}">`  
  → `@NotBlank` によるエラーメッセージを表示。`th:if` でエラーがあるときだけ表示します。

---

## ✅ まとめ：このStepで学べるSpringのポイント

| 技術 | 説明 |
|------|------|
| バリデーション | `@NotBlank`、`@Valid`、`BindingResult` |
| フォームバインディング | `th:object` + `th:field` |
| Model操作 | `model.addAttribute()` でフォーム用データを渡す |
| POST-Redirect-GET | フォーム再送信を防ぐ基本設計 |
| エラーメッセージ表示 | `th:errors` と `#fields.hasErrors()` |

---

この形式でStep5も進めることが可能です。  
もしこの説明で「もう少し詳しく知りたい箇所」や「実行時に気になること」があれば、どんどん聞いてくださいね！