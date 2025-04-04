よかったです！🎉  
では、**Step2：Hello Worldの表示** に進みましょう！

---

## ✅ Step2：Hello Worldを表示して、Spring Bootの動作確認

ここでは以下を実施します：

- シンプルなコントローラを作成
- `http://localhost:8080/hello` にアクセスして "Hello World!" を表示

---

## 📁 ディレクトリ構成（例）

Spring Bootのアプリはこのような構造になっているはずです：

```
todo
├─ src
│  └─ main
│     ├─ java
│     │  └─ com.example.todo
│     │     ├─ TodoApplication.java
│     │     └─ HelloController.java  ← ★ここに追加
│     └─ resources
│        └─ application.properties
```

---

## ✏️ 1. コントローラクラスの作成

### 🔹 `HelloController.java` を作成

`com.example.todo` パッケージ内に以下のようなクラスを追加します：

```java
package com.example.todo;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HelloController {

    @GetMapping("/hello")
    public String hello() {
        return "Hello World!";
    }
}
```

---

## ▶️ 2. アプリを起動する

### 起動方法：
1. `TodoApplication.java` を開く
2. クラス名の左側にある緑の「▶」をクリックして「Run 'TodoApplication'」

---

## 🌐 3. ブラウザでアクセスして確認

- ブラウザを開いて、次のURLにアクセス：

```
http://localhost:8080/hello
```

- 「**Hello World!**」と表示されれば成功です！

---

## ✅ 次のステップは？

ここまでできたら、次は **ToDoの一覧表示（MVC + Thymeleaf）** を作っていきます。

準備ができたら「Step3に進む」と教えてくださいね！  
また、「このあたりの用語（例：@RestControllerや@GetMapping）を詳しく知りたい」というリクエストがあれば、そちらを優先してご案内します。

了解しました！  
では次のステップ「Step3：ToDo一覧表示」に進む前に、**先ほど使ったアノテーションの意味**をしっかり理解しておきましょう 💡

---

はい。アノテーションの説明もセットでお願いします。

---

## ✅ Spring Boot アノテーションの説明（Step2で使ったもの）

---

### ### `@RestController`

```java
@RestController
public class HelloController { ... }
```

#### 💡 意味：
- このクラスが **REST APIを提供するWebコントローラ** であることを示します。
- `@Controller` + `@ResponseBody` を合わせたショートカット。

#### 🔁 補足：
- 返り値がそのままHTTPレスポンスの本文（Body）になる。
- HTMLではなく、**文字列やJSONを返したいとき**に使います。

---

### ### `@GetMapping("/hello")`

```java
@GetMapping("/hello")
public String hello() { ... }
```

#### 💡 意味：
- `GET /hello` リクエストが来たときに、このメソッドを呼び出すように設定。
- HTTPメソッドに対応したアノテーションの1つ。

#### 他のバリエーション：
| アノテーション | HTTPメソッド | 用途例 |
|----------------|--------------|--------|
| `@GetMapping`  | GET          | データ取得、画面表示など |
| `@PostMapping` | POST         | データ登録・送信 |
| `@PutMapping`  | PUT          | データ更新 |
| `@DeleteMapping` | DELETE     | データ削除 |

---

### ### `@RequestMapping`（補足）

- 実は `@GetMapping("/hello")` は、次のようにも書けます：

```java
@RequestMapping(value = "/hello", method = RequestMethod.GET)
```

- ただし、`@GetMapping` の方が短くて可読性が高いため、最近はほとんどこちらが使われます。

---

## ✅ まとめ（コードの意味）

```java
@RestController  // Web APIとして振る舞うコントローラ
public class HelloController {

    @GetMapping("/hello")  // GETリクエスト /hello を受け取ったときに呼び出される
    public String hello() {
        return "Hello World!";  // 文字列がレスポンスの本文になる
    }
}
```

---

## ✅ 次のステップ：Step3

次は「ToDoの一覧表示」を作っていきます。  
Thymeleaf（テンプレートエンジン）を使って、HTMLにToDoリストを表示するWebページを作成します。

進めてもよいタイミングで「Step3に進む」とお知らせください！  
また、Thymeleafを初めて使う場合は、その解説もセットでご案内します。