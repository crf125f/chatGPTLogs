## 1 Entity更新

下記の構成のEntityクラスを作成
### 1. `todo_entity`
| カラム | 型 | 内容 |
|--------|----|------|
| id     | int | 主キー |
| title  | varchar | タイトル |
| done   | boolean | 完了フラグ |
| created_at | datetime | 作成日時 |
| assignee_id | int | 担当者ID（外部キー） |

---

### 2. `user`
| カラム | 型 | 内容 |
|--------|----|------|
| id     | int | 主キー |
| name   | varchar | ユーザー名 |
| team_id| int | 所属チーム（外部キー） |

---

### 3. `team`
| カラム | 型 | 内容 |
|--------|----|------|
| id     | int | 主キー |
| name   | varchar | チーム名 |

---





