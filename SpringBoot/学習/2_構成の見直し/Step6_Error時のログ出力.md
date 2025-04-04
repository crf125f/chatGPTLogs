了解しました！  
では、**改善ステップ⑥：編集時のエラー対応と画面再表示の安定化** に進みましょう。

---

# ✅ 改善ステップ⑥：編集時のバリデーションエラー発生時に画面が崩れないよう安定させる

---

## 🎯 改善の目的

| 課題 | 解決方法 |
|------|----------|
| バリデーションエラー発生時に、`edit.html` の画面構造が崩れたり、`todo` オブジェクトが見つからずエラーになることがある | `Model` へのデータ設定とテンプレート構成を見直し、常に安定して表示できるようにする |

---

## ✅ 処理の流れ（バリデーションエラー時）

```
[POST /todos/{id}/edit]
   ↓
[バリデーション失敗]
   ↓
return "edit"
   ↓
edit.html を再表示
   ↓
→ ここで `todo` が null だとエラーになる
```

---

## ✅ 実装確認ポイントと変更内容

---

### 🔹① `updateTodo()` の ifエラー内で `todo` を再設定する（※必要な場合）

#### 📄 `TodoController.java`

```java
@PostMapping("/todos/{id}/edit")
public String updateTodo(@PathVariable int id,
                         @Valid @ModelAttribute("todo") TodoForm form,
                         BindingResult bindingResult,
                         Model model,
                         RedirectAttributes redirectAttributes) {

    if (bindingResult.hasErrors()) {
        // ★必要に応じて再度セット（今は@ModelAttributeがあるため基本不要）
        // model.addAttribute("todo", form);
        return "edit";
    }

    this.todoService.updateTodo(id, form.getTitle(), form.isDone());
    redirectAttributes.addFlashAttribute("message", "ToDoを更新しました。");
    return "redirect:/todos";
}
```

> `@ModelAttribute("todo")` を使っている場合、Springが自動的に `form` をModelに登録してくれるため、通常は明示的な `addAttribute` は不要です。

---

### 🔹② `edit.html` での `th:object="${todo}"` をそのまま使える状態に保つ

→ Step5〜6 で既に修正済みであれば、特別な変更は不要です。

---

### 🔹③ 必要に応じて、コントローラーにロギングを追加してエラーの再現を追いやすくする

```java
if (bindingResult.hasErrors()) {
    log.warn("Validation error on update: {}", bindingResult.getAllErrors());
    return "edit";
}
```

---

## ✅ 処理漏れチェックリスト

| チェック項目 | 内容 | 状態 |
|--------------|------|------|
| バリデーションエラー時に `"edit"` を返しているか | ✅ |
| `todo` オブジェクトがModelに存在するか | ✅（`@ModelAttribute` によって自動で保持） |
| `th:field="*{title}"` 等で正しく表示されているか | ✅ |
| HTML構造（共通レイアウト）にエラーが起きていないか | ✅ |

---

## ✅ 効果まとめ

| Before | After |
|--------|-------|
| バリデーションエラー時に `todo` が null になるなどの崩れがち | `@ModelAttribute` によって安定してデータが画面にバインドされる |
| ユーザーが入力を失うリスクあり | 入力値が保持され、安心して修正できるUIに |

---

このステップで、**編集時のエラーパターンに対する安全性とUX（ユーザー体験）の品質が向上**しました。

次に進む場合は、  
▶️「改善ステップ⑦に進みたいです」  
とお知らせください！

次は、**編集処理におけるFlashメッセージの見直し（例：成功時とエラー時を明確に分けて表示）**を予定しています。