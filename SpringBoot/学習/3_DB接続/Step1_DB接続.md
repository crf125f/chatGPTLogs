ありがとうございます、Maven をお使いとのことですので、  
さきほどの内容を **Maven 向けに仕切り直して**ご案内します！

---

# 🧩 ステップ1：DBからのデータ取得（JPA導入・Maven編）

---

## ✅ 目標

- H2データベースを使って ToDo を保存＆取得  
- Spring Data JPA 経由でDB操作を行う

---

## ✅ 準備手順（Mavenプロジェクト）

---

### ① pom.xml に以下の依存を追加

```xml
<dependencies>
    <!-- Spring Boot Starter Web（既にある場合はOK） -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>

    <!-- Spring Data JPA -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>

    <!-- H2 Database（開発用） -->
    <dependency>
        <groupId>com.h2database</groupId>
        <artifactId>h2</artifactId>
        <scope>runtime</scope>
    </dependency>
</dependencies>
```

---

### ② `application.properties` に設定追加

```properties
# データソース設定（H2）
spring.datasource.url=jdbc:h2:mem:todoapp;DB_CLOSE_DELAY=-1
spring.datasource.driver-class-name=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=

# JPA設定
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true

# H2コンソール有効化
spring.h2.console.enabled=true
```

> http://localhost:8080/h2-console でアクセス可能になります

---

### ③ `Todo` エンティティの作成（`model` パッケージ）

```java
package com.example.todo.model;

import jakarta.persistence.*;

@Entity
public class Todo {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private int id;

    private String title;

    private boolean done;

    // Getter / Setter
}
```

---

### ④ リポジトリの作成（`repository` パッケージ）

```java
package com.example.todo.repository;

import com.example.todo.model.Todo;
import org.springframework.data.jpa.repository.JpaRepository;

public interface TodoRepository extends JpaRepository<Todo, Integer> {
}
```

---

### ⑤ サービスクラス（`service` パッケージ）

```java
package com.example.todo.service;

import com.example.todo.model.Todo;
import com.example.todo.repository.TodoRepository;
import org.springframework.stereotype.Service;

import java.util.List;

@Service
public class TodoService {

    private final TodoRepository todoRepository;

    public TodoService(TodoRepository todoRepository) {
        this.todoRepository = todoRepository;
    }

    public List<Todo> getTodoList() {
        return this.todoRepository.findAll();
    }
}
```

---

### ⑥ コントローラーで呼び出す

```java
@GetMapping("/todos")
public String showTodoList(Model model) {
    List<Todo> todos = todoService.getTodoList();
    model.addAttribute("todos", todos);
    model.addAttribute("todo", new Todo());     //空のTodoEntityをlist画面に渡す。
    return "list";
}
```

---

## ✅ 確認ステップ

- ブラウザで `http://localhost:8080/todos` にアクセス  
- 必要に応じて `h2-console` でも DBの中身を確認

---

次に進む前に、ここまでの準備が完了していれば、  
**「DTO導入による表示用データの整理」** に進めます！

進めても大丈夫そうですか？必要に応じてpom.xml全文などもご提供可能です。

素晴らしいです！表示確認できたとのことで、次のステップに進みましょう。

---

# 🧩 ステップ2：DTO導入（EntityとViewの分離）

---

## ✅ このステップの目的

- `Todo` エンティティはDB専用とし、  
- 画面表示には `TodoDto` を使うようにして、  
- **ControllerやViewをシンプルに保つ**

---

## ✅ やること一覧

1. `TodoDto` を `model` パッケージに作成  
2. `TodoService` で Entity → DTO に変換  
3. `Controller` では DTO を渡すように変更  
4. `list.html` は今まで通り表示可能

---

## ✅ 1. TodoDto.java の作成（`model` パッケージ）

```java
package com.example.todo.model;

public class TodoDto {
    private int id;
    private String title;
    private boolean done;

    public TodoDto(int id, String title, boolean done) {
        this.id = id;
        this.title = title;
        this.done = done;
    }

    // Getter・Setter
    public int getId() {
        return id;
    }

    public String getTitle() {
        return title;
    }

    public boolean isDone() {
        return done;
    }
}
```

---

## ✅ 2. TodoService の修正

```java
public List<TodoDto> getTodoDtoList() {
    List<Todo> entities = todoRepository.findAll();

    return entities.stream()
        .map(todo -> new TodoDto(todo.getId(), todo.getTitle(), todo.isDone()))
        .toList();
}
```

> DTOを返す新メソッドとして `getTodoDtoList()` を追加。  
> 既存の `getTodoList()` を置き換えてもOKです。

---

## ✅ 3. Controller の修正

```java
@GetMapping("/todos")
public String showTodoList(Model model) {
    model.addAttribute("todo", new Todo()); // 入力用に引き続き使用
    model.addAttribute("todos", todoService.getTodoDtoList()); // DTOに変更
    return "list";
}
```

---

## ✅ 4. View側（list.html）はそのままでOK

DTOとEntityのプロパティ名が同じであれば、テンプレート側の修正は不要です：

```html
<tr th:each="todo : ${todos}">
    <td th:text="${todo.id}">ID</td>
    <td th:text="${todo.title}">タイトル</td>
    <td th:text="${todo.done} ? '✔' : '×'">状態</td>
</tr>
```

---

## ✅ 効果

- Entityが画面に出なくなる（疎結合化）
- 表示用データに特化した構造ができる
- 後々のテスト・保守が楽になる

---

このステップを反映して動作確認できたら、  
次は **入力フォーム用の TodoForm 導入（バリデーション付き）** に進むのが自然です。

進めても大丈夫ですか？必要があれば `list.html` 側のコード確認・調整も可能です！