`verify` は、JUnit テストで **モックオブジェクト（mock）** の振る舞いを確認するために使用します。主に、モックされたオブジェクトが特定のメソッドを呼び出したかどうかを確認する際に使います。`verify` を使用することで、テストが**期待通りの方法でモックオブジェクトを操作しているか**を確認できます。

---

## ✅ 1. `verify` の基本的な使い方

モックオブジェクトは、Mockito というライブラリを使って作成することが一般的です。`verify` は、モックオブジェクトが呼び出されたメソッドを **検証するための機能**です。

### 例1: メソッドが呼ばれたかどうかを確認

```java
import static org.mockito.Mockito.*;

@Test
void testServiceCall() {
    // モックオブジェクトを作成
    TodoService todoService = mock(TodoService.class);

    // メソッドの呼び出し
    todoService.addTodo(new TodoForm());

    // メソッドが呼ばれたことを確認
    verify(todoService).addTodo(any(TodoForm.class));  // addTodoが1回呼ばれたか確認
}
```

#### 解説：
- `verify(todoService).addTodo(any(TodoForm.class));` は、`todoService` が `addTodo` メソッドを **1回だけ呼び出したこと**を確認します。
- `any(TodoForm.class)` は、`addTodo` メソッドの引数が **`TodoForm` 型のどんなインスタンスでもよい** ということを意味します。

---

## ✅ 2. `verify` の使い道

### 2.1. **メソッドが呼ばれた回数を確認**

`verify` ではメソッドが呼ばれた回数を指定することもできます。

#### 例2: メソッドが特定回数呼ばれたか確認

```java
@Test
void testMultipleCalls() {
    // モックオブジェクトを作成
    TodoService todoService = mock(TodoService.class);

    // メソッドの呼び出し
    todoService.addTodo(new TodoForm());
    todoService.addTodo(new TodoForm());

    // メソッドが2回呼ばれたことを確認
    verify(todoService, times(2)).addTodo(any(TodoForm.class));
}
```

#### 解説：
- `times(2)` は、`addTodo` メソッドが **2回呼ばれたことを確認**します。

### 2.2. **メソッドが呼ばれていないことを確認**

メソッドが全く呼ばれなかったことを確認することもできます。

#### 例3: メソッドが1回も呼ばれていないことを確認

```java
@Test
void testNoCall() {
    // モックオブジェクトを作成
    TodoService todoService = mock(TodoService.class);

    // メソッドは呼ばない

    // メソッドが呼ばれていないことを確認
    verify(todoService, never()).addTodo(any(TodoForm.class));
}
```

#### 解説：
- `never()` は、`addTodo` メソッドが **1回も呼ばれなかった** ことを確認します。

---

## ✅ 3. `verify` と `times` の組み合わせ

`verify` は回数の確認に非常に便利です。回数に関して以下のオプションが利用できます：

- `times(n)` → メソッドが `n` 回呼ばれたことを確認
- `never()` → メソッドが呼ばれなかったことを確認
- `atLeastOnce()` → メソッドが1回以上呼ばれたことを確認
- `atMost(n)` → メソッドが最大 `n` 回呼ばれたことを確認

#### 例4: メソッドが最小回数・最大回数呼ばれたかを確認

```java
@Test
void testAtLeastOnceAndAtMost() {
    // モックオブジェクトを作成
    TodoService todoService = mock(TodoService.class);

    // メソッドの呼び出し
    todoService.addTodo(new TodoForm());
    todoService.addTodo(new TodoForm());

    // メソッドが1回以上呼ばれたことを確認
    verify(todoService, atLeastOnce()).addTodo(any(TodoForm.class));

    // メソッドが最大2回呼ばれたことを確認
    verify(todoService, atMost(2)).addTodo(any(TodoForm.class));
}
```

---

## ✅ 4. `verify` の使い方を踏まえた例（TodoControllerのテスト）

### 例5: `TodoController` の `addTodo` メソッドが呼ばれたか確認

```java
@Test
void testAddTodo() {
    // モックオブジェクトの作成
    TodoService todoService = mock(TodoService.class);
    TodoController todoController = new TodoController(todoService);

    TodoForm todoForm = new TodoForm();
    todoForm.setTitle("New Task");

    BindingResult bindingResult = mock(BindingResult.class);
    when(bindingResult.hasErrors()).thenReturn(false);

    // コントローラのメソッドを呼び出す
    todoController.addTodo(todoForm, bindingResult, model, redirectAttributes);

    // メソッドが1回呼ばれたことを確認
    verify(todoService).addTodo(any(TodoForm.class));  // サービスのaddTodoが1回呼ばれたことを確認
}
```

---

## ✅ まとめ

- **`verify`** は、モックオブジェクトが期待通りにメソッドを呼び出しているかどうかを検証するための重要なツールです。
- メソッドが呼ばれた回数を確認したり、呼ばれていないことを確認したりできます。
- **`Mockito` の `verify`** を使うことで、テストコードが簡潔に、かつ強力に振る舞いをチェックできます。

---

もし具体的な例で疑問点があれば、さらなる詳細をお伝えしますので、お気軽にどうぞ！