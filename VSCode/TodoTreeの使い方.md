### **VSCodeの「Todo Tree」のインストール手順と使い方**

---

## **1. Todo Treeのインストール**
### **手順**
1. **VSCodeを開く**
2. **拡張機能（Extensions）を開く**  
   - `Ctrl + Shift + X` （Windows / Linux）  
   - `Cmd + Shift + X` （Mac）
3. **「Todo Tree」と検索**
4. **「Todo Tree」拡張機能をインストール**
5. **インストール後、VSCodeを再起動（推奨）**

---

## **2. Todo Treeの基本的な使い方**
### **a. コメントに「TODO」や「FIXME」を追加**
Todo Treeは、特定のキーワード（デフォルトは `TODO` や `FIXME`）が含まれるコメントを検出して一覧表示します。

以下のようにコメントを追加すると、自動的に検出されます。

#### **例：JavaScript**
```javascript
// TODO: この関数のリファクタリングが必要
function myFunction() {
    console.log("処理中...");
}

// FIXME: バグが発生する可能性あり
let data = fetchData();
```

#### **例：Python**
```python
# TODO: データの処理を最適化する
def process_data():
    pass

# FIXME: 例外処理が足りない
try:
    risky_function()
except:
    pass
```

---

### **b. Todo Treeのビューを開く**
1. **「Explorer（エクスプローラー）」を開く**
2. **「Todo Tree」パネルを開く**
   - `Ctrl + Shift + P`（Windows / Linux）  
   - `Cmd + Shift + P`（Mac）  
   - **「Todo Tree: Focus on Tree View」** と入力し、Enter
3. **コード内の「TODO」「FIXME」リストが表示される**
4. **クリックすると該当箇所へジャンプできる**

---

## **3. Todo Treeのカスタマイズ**
Todo Treeでは、デフォルトで `TODO` や `FIXME` を検索しますが、カスタマイズ可能です。

### **a. 「settings.json」で設定を変更**
VSCodeの「settings.json」を開いてカスタマイズできます。

1. **VSCodeの設定を開く**
   - `Ctrl + Shift + P`（Windows / Linux）
   - `Cmd + Shift + P`（Mac）
   - 「Preferences: Open Settings (JSON)」と入力し、Enter
2. **以下の設定を追加**
   ```json
   {
       "todo-tree.general.tags": [
           "TODO",
           "FIXME",
           "NOTE",
           "HACK",
           "BUG"
       ],
       "todo-tree.highlights.defaultHighlight": {
           "foreground": "white",
           "background": "red",
           "icon": "alert"
       }
   }
   ```
   - `"todo-tree.general.tags"` に新しいタグを追加
   - `"todo-tree.highlights.defaultHighlight"` で色やアイコンを変更

---

### **b. フィルタ機能を使う**
Todo Treeの「Search Filter」を活用すると、特定のディレクトリやファイルだけを対象にできます。

1. **「Todo Tree」パネルの検索バーにフィルタを入力**
   - 例：`src/**/*.js`（srcフォルダ内のJavaScriptファイルのみ対象）
   - 例：`!node_modules/**`（node_modulesは除外）

---

## **4. Todo Treeの便利な使い方**
### **a. プロジェクト内の重要なTODOを一括管理**
プロジェクトが大きくなると、TODOやFIXMEの管理が難しくなりますが、Todo Treeを使うと「どこに何の課題があるか」が一覧で分かります。

### **b. Gitと組み合わせて「未完了タスク」を追跡**
- **Gitのコミット前に「TODO」を確認**
- `git commit -m "修正完了"` の前に、Todo Treeで未解決のTODOをチェック
- **「未解決のTODOがあればコミットしない」ルールを作るとミス防止に**

---

## **5. まとめ**
| 項目 | 手順 |
|------|------|
| **インストール** | VSCodeの拡張機能で「Todo Tree」を検索してインストール |
| **基本的な使い方** | `// TODO: 〇〇を修正` などをコードに記述すると自動検出 |
| **ビューの開き方** | `Ctrl + Shift + P` → `Todo Tree: Focus on Tree View` |
| **カスタマイズ** | `settings.json` に `"todo-tree.general.tags"` を追加 |
| **フィルタ機能** | 特定のフォルダ・ファイルのみ対象にできる |
| **便利な使い方** | Gitと連携し、未完了のTODOを管理 |

---

**Todo Treeを導入すると、開発中のタスク管理が楽になります。**  
まずはデフォルト設定のまま試し、必要に応じてカスタマイズするのがオススメです！