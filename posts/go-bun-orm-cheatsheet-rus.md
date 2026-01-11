# Шпаргалка по Bun ORM (Go)  

Доменная модель: User, Post, Tag, Category (Post ↔ Tag через post_tags)

---

## Оглавление

1. Установка  
2. Подключение DB  
3. Модели (User, Post, Tag, Category, PostTag)  
4. Типы связей в Bun  
5. Миграции  
6. CRUD  
7. Работа со связями (eager loading)  
8. One-to-Many  
9. Belongs-To  
10. Many-to-Many  
11. Управление M2M вручную и через pivot  
12. Обновления и Upsert  
13. Soft Delete  
14. Транзакции  
15. Пагинация  
16. Агрегации и отчёты  
17. Динамическая фильтрация  
18. Optimistic Lock  
19. Hooks  
20. JSON / JSONB  
21. Raw SQL  
22. Индексы и оптимизация  
23. Локинг (FOR UPDATE)  
24. Тестирование (SQLite)  
25. Частые паттерны Relation  
26. Troubleshooting  
27. Быстрые рецепты (insert returning, exists, distinct и т.д.)  
28. Fulltext пример (PostgreSQL)  
29. Диагностика производительности  
30. Пример сервисной функции CreatePost  
31. Минимальный HTTP handler  
32. Чеклист новой модели  
33. Практические советы по проектированию схемы  
34. Итоги  

---

## 1. Установка

````go
go get github.com/uptrace/bun
go get github.com/uptrace/bun/dialect/pgdialect    // или mysql/sqlite
go get github.com/uptrace/bun/extra/bundebug
go get github.com/uptrace/bun/migrate
````

---

## 2. Подключение

````go
import (
  "database/sql"
  "github.com/uptrace/bun"
  "github.com/uptrace/bun/dialect/pgdialect"
  "github.com/uptrace/bun/extra/bundebug"
  _ "github.com/jackc/pgx/v5/stdlib"
)

func NewDB(dsn string) *bun.DB {
  sqldb, err := sql.Open("pgx", dsn)
  if err != nil { panic(err) }
  db := bun.NewDB(sqldb, pgdialect.New())
  db.AddQueryHook(bundebug.NewQueryHook(bundebug.WithVerbose(true)))
  return db
}
````

---

## 3. Модели

````go
package model
import (
  "time"
  "github.com/uptrace/bun"
)

type User struct {
  bun.BaseModel `bun:"table:users,alias:u"`
  ID        int64      `bun:",pk,autoincrement"`
  Email     string     `bun:",unique,notnull"`
  Name      string     `bun:",notnull"`
  Posts     []*Post    `bun:"rel:has-many,join:id=user_id"` // One(User)-to-Many(Post)
  CreatedAt time.Time  `bun:",nullzero,notnull,default:current_timestamp"`
  UpdatedAt time.Time  `bun:",nullzero,notnull,default:current_timestamp"`
}

type Category struct {
  bun.BaseModel `bun:"table:categories,alias:c"`
  ID    int64    `bun:",pk,autoincrement"`
  Name  string   `bun:",unique,notnull"`
  Posts []*Post  `bun:"rel:has-many,join:id=category_id"`
}

type Tag struct {
  bun.BaseModel `bun:"table:tags,alias:t"`
  ID    int64    `bun:",pk,autoincrement"`
  Name  string   `bun:",unique,notnull"`
  Posts []*Post  `bun:"m2m:post_tags,join:Tag=Post"`
}

type Post struct {
  bun.BaseModel `bun:"table:posts,alias:p"`
  ID         int64       `bun:",pk,autoincrement"`
  UserID     int64       `bun:"user_id,notnull"`
  User       *User       `bun:"rel:belongs-to,join:user_id=id"`          // Belongs-To(User)
  CategoryID *int64      `bun:"category_id"`
  Category   *Category   `bun:"rel:belongs-to,join:category_id=id"`
  Title      string      `bun:",notnull"`
  Body       string      `bun:",type:text"`
  Tags       []*Tag      `bun:"m2m:post_tags,join:Post=Tag"`             // Many-to-Many(Tag)
  Metadata   map[string]any `bun:"metadata,type:jsonb"`
  CreatedAt  time.Time
  UpdatedAt  time.Time
  DeletedAt  time.Time `bun:",soft_delete,nullzero"`
  Version    int64     `bun:"version,default:1"`
}

type PostTag struct {
  bun.BaseModel `bun:"table:post_tags,alias:pt"`
  PostID    int64     `bun:",pk"`
  TagID     int64     `bun:",pk"`
  CreatedAt time.Time `bun:",nullzero,notnull,default:current_timestamp"`
}
````

---

## 4. Типы связей в Bun (концептуально)

Bun не использует автоматическое рефлексивное «угадывание» связей (как GORM), вы явно описываете их через теги.

Основные типы:
1. has-many (One-to-Many): Модель A имеет множество B. В модели A поле `[]*B` + тег `rel:has-many,join:<local>=<foreign column>`  
2. belongs-to: Модель указывает на владельца. В дочерней модели поле `Owner *Owner` + `rel:belongs-to,join:<fk>=<owner_pk>` и отдельное поле FK (`OwnerID`)  
3. has-one (аналог belongs-to в другую сторону) — редко нужен, можно опустить  
4. m2m (Many-to-Many): `m2m:<pivot_table>,join:<ThisModelField>=<OtherModelField>`; требуется промежуточная таблица (модель опциональна)  

Как Bun строит JOIN:  
- Для has-many: основной запрос — родитель; вложенный relation будет отдельным SELECT (eager, не один JOIN) — Bun подгружает через IN (эффективно уменьшает N+1).  
- Для belongs-to: строится JOIN (если relation загружается) по join выражению.  
- Для m2m: выполняется запрос к pivot + затем к связанным данным с IN.

Eager loading — Relation("Field"); Lazy loading нет (нельзя просто обратиться к полю — оно не заполнится автоматически, нужно явно Relation).

---

## 5. Миграции

````go
package migrations
import (
  "context"
  "github.com/uptrace/bun"
  "github.com/uptrace/bun/migrate"
  "yourapp/model"
)
var Migrations = migrate.NewMigrations()

func init() {
  Migrations.MustRegister(func(ctx context.Context, db *bun.DB) error {
    models := []any{
      (*model.User)(nil),
      (*model.Category)(nil),
      (*model.Tag)(nil),
      (*model.Post)(nil),
      (*model.PostTag)(nil),
    }
    for _, m := range models {
      if _, err := db.NewCreateTable().Model(m).
        IfNotExists().
        WithForeignKeys().
        Exec(ctx); err != nil {
        return err
      }
    }
    _, err := db.Exec(`
      CREATE INDEX IF NOT EXISTS idx_posts_user_id_created_at
      ON posts(user_id, created_at DESC);
    `)
    return err
  }, func(ctx context.Context, db *bun.DB) error {
    db.NewDropTable().Model((*model.PostTag)(nil)).IfExists().Exec(ctx)
    db.NewDropTable().Model((*model.Post)(nil)).IfExists().Exec(ctx)
    db.NewDropTable().Model((*model.Tag)(nil)).IfExists().Exec(ctx)
    db.NewDropTable().Model((*model.Category)(nil)).IfExists().Exec(ctx)
    db.NewDropTable().Model((*model.User)(nil)).IfExists().Exec(ctx)
    return nil
  })
}
````

---

## 6. CRUD (кратко)

````go
user := &model.User{Email: "a@ex.com", Name: "Alice"}
_, _ = db.NewInsert().Model(user).Exec(ctx)

var u model.User
_ = db.NewSelect().Model(&u).Where("u.email = ?", "a@ex.com").Scan(ctx)

u.Name = "Alice Updated"
_, _ = db.NewUpdate().Model(&u).Column("name", "updated_at").WherePK().Exec(ctx)

_, _ = db.NewDelete().Model(&u).WherePK().Exec(ctx)
````

---

## 7. Eager Loading Relation

````go
var posts []model.Post
err := db.NewSelect().
  Model(&posts).
  Relation("User").
  Relation("Category").
  Relation("Tags").
  Where("p.user_id = ?", userID).
  OrderExpr("p.created_at DESC").
  Limit(20).
  Scan(ctx)
````

---

## 8. One-to-Many

Пример: User → Post (один пользователь имеет много постов).

В родителе (User):  
````go
Posts []*Post `bun:"rel:has-many,join:id=user_id"`
````

Расшифровка join:id=user_id:  
- Левая часть (id) — локальный столбец в таблице users  
- Правая часть (user_id) — внешний ключ в таблице posts  

Подгрузка:
````go
var users []model.User
err := db.NewSelect().
  Model(&users).
  Relation("Posts", func(q *bun.SelectQuery) *bun.SelectQuery {
    return q.OrderExpr("p.created_at DESC").Limit(5)
  }).
  Scan(ctx)
````

Особенности:
- Bun выполнит 2 запроса: один к users, потом один к posts с WHERE user_id IN (...)
- Если нужно фильтровать по атрибутам Posts, можно использовать WhereExists / Join (но Relation остаётся для загрузки):
````go
var usersWithRecent []model.User
err := db.NewSelect().
  Model(&usersWithRecent).
  WhereExists(`
    SELECT 1 FROM posts p
    WHERE p.user_id = u.id AND p.created_at > now() - interval '7 days'
  `).
  Relation("Posts", func(q *bun.SelectQuery) *bun.SelectQuery {
    return q.Where("p.created_at > now() - interval '7 days'")
  }).
  Scan(ctx)
````

Кастомное имя FK (если бы было author_id):
````go
Posts []*Post `bun:"rel:has-many,join:id=author_id"`
````

Удаление каскадно?  
- На уровне БД можно задать FOREIGN KEY ... ON DELETE CASCADE (через миграцию)  
- Soft delete не каскадит (вы решаете вручную логикой приложения).  

Оптимизация:  
- Ограничивайте Relation лимитом (например топ N)  
- Если нужно количество, используйте COUNT вместо загрузки всех:  
````go
count, _ := db.NewSelect().Model((*model.Post)(nil)).Where("user_id = ?", uid).Count(ctx)
````

---

## 9. Belongs-To

Пример: Post → User:  
````go
UserID int64  `bun:"user_id"`
User   *User  `bun:"rel:belongs-to,join:user_id=id"`
````

Почему нужно отдельное поле UserID:  
- Bun использует его для WHERE / INSERT / UPDATE  
- Поле User — для загрузки структуры владельца.

Подгрузка:
````go
var post model.Post
_ = db.NewSelect().
  Model(&post).
  Relation("User", func(q *bun.SelectQuery) *bun.SelectQuery {
    return q.Column("u.id", "u.name") // узкая выборка
  }).
  Where("p.id = ?", id).
  Scan(ctx)
````

Если FK NULLABLE (CategoryID *int64):  
- Если CategoryID == nil, Relation("Category") вернёт Category = nil  
- Проверяйте на nil (пустые связи не создают ошибки).

Смена владельца:
````go
_, _ = db.NewUpdate().
  Model((*model.Post)(nil)).
  Set("user_id = ?", newUserID).
  Where("id = ?", postID).
  Exec(ctx)
````

Несуществующий FK — получите ошибку только если включены внешние ключи на уровне БД.

---

## 10. Many-to-Many

Пример: Post ↔ Tag через post_tags:

В Post:
````go
Tags []*Tag `bun:"m2m:post_tags,join:Post=Tag"`
````

В Tag:
````go
Posts []*Post `bun:"m2m:post_tags,join:Tag=Post"`
````

Логика join:  
- `m2m:<pivot_table>` — имя таблицы  
- `join:Post=Tag` — левая часть соответствует текущей модели (полю PostID в pivot), правая — целевой модели (TagID).  
Bun строит схему:  
- Ожидает столбцы: post_id, tag_id (snake_case от имен моделей по умолчанию)  
- Если другое имя (например article_id): нужно переопределить pivot модель и использовать кастомное поле.

Кастомный pivot:
````go
type PostTag struct {
  bun.BaseModel `bun:"table:post_tags,alias:pt"`
  PostID  int64 `bun:"post_id"`
  TagID   int64 `bun:"tag_id"`
  Weight  int   `bun:"weight"`
}
````

Подгрузка тегов:
````go
var post model.Post
_ = db.NewSelect().
  Model(&post).
  Relation("Tags", func(q *bun.SelectQuery) *bun.SelectQuery {
    return q.OrderExpr("t.name ASC")
  }).
  Where("p.id = ?", id).
  Scan(ctx)
````

Фильтрация по M2M:
````go
var posts []model.Post
_ = db.NewSelect().
  Model(&posts).
  Join("JOIN post_tags pt ON pt.post_id = p.id").
  Join("JOIN tags t ON t.id = pt.tag_id").
  Where("t.name = ?", "go").
  Relation("Tags").
  Scan(ctx)
````

Оптимизация:
- Индекс на post_tags(post_id), post_tags(tag_id)  
- При фильтрации по названию Tag лучше иметь индекс на tags(name)  

Уникальность:
````sql
ALTER TABLE post_tags
  ADD CONSTRAINT post_tags_unique UNIQUE (post_id, tag_id);
````

---

## 11. Управление M2M вручную

Добавление тегов к посту (upsert tags + вставка pivot):

````go
func AttachTagsToPost(ctx context.Context, db *bun.DB, postID int64, names []string) error {
  return db.RunInTx(ctx, nil, func(ctx context.Context, tx bun.Tx) error {
    var tags []*model.Tag
    for _, n := range names {
      t := &model.Tag{Name: n}
      if _, err := tx.NewInsert().Model(t).
        On("CONFLICT (name) DO NOTHING").
        Exec(ctx); err != nil {
        return err
      }
      if t.ID == 0 {
        if err := tx.NewSelect().Model(t).Where("name = ?", n).Scan(ctx); err != nil {
          return err
        }
      }
      tags = append(tags, t)
    }
    // вставка pivot
    pts := make([]model.PostTag, 0, len(tags))
    for _, t := range tags {
      pts = append(pts, model.PostTag{PostID: postID, TagID: t.ID})
    }
    if len(pts) > 0 {
      if _, err := tx.NewInsert().Model(&pts).
        On("CONFLICT DO NOTHING").
        Exec(ctx); err != nil {
        return err
      }
    }
    return nil
  })
}
````

Удаление конкретного тега:
````go
_, _ = db.NewDelete().
  Model((*model.PostTag)(nil)).
  Where("post_id = ?", postID).
  Where("tag_id = ?", tagID).
  Exec(ctx)
````

Полная замена набора тегов (diff стратегия):
1. Получить текущие tag_ids  
2. Построить множества для добавления/удаления  
3. Insert / Delete pivot партиями  

---

## 12. Обновления и Upsert

````go
_, _ = db.NewInsert().Model(&model.User{Email:"a@ex.com", Name:"A"}).
  On("CONFLICT (email) DO UPDATE").
  Set("name = EXCLUDED.name").
  Exec(ctx)
````

---

## 13. Soft Delete

Поле с тегом `soft_delete`:
````go
DeletedAt time.Time `bun:",soft_delete,nullzero"`
````

Обычный Delete:
````go
_, _ = db.NewDelete().Model(&post).WherePK().Exec(ctx) // выставит deleted_at
````

Игнорировать soft:
````go
_, _ = db.NewDelete().Model(&post).WherePK().ForceDelete().Exec(ctx)
````

Выборка включая удалённые:
````go
db.NewSelect().Model(&posts).WhereAllWithDeleted().Scan(ctx)
````

---

## 14. Транзакции

````go
err := db.RunInTx(ctx, nil, func(ctx context.Context, tx bun.Tx) error {
  // операции
  return nil
})
````

Ручное:
````go
tx, _ := db.BeginTx(ctx, &sql.TxOptions{})
defer tx.Rollback()
...
tx.Commit()
````

---

## 15. Пагинация

````go
page, limit := 2, 20
offset := (page-1)*limit
var posts []model.Post
q := db.NewSelect().Model(&posts).
  Relation("User").
  Limit(limit).
  Offset(offset).
  OrderExpr("p.created_at DESC")
_ = q.Scan(ctx)
total, _ := db.NewSelect().Model((*model.Post)(nil)).Count(ctx)
````

---

## 16. Агрегации

````go
var stats []struct{
  Category string `bun:"category"`
  Count    int64  `bun:"count"`
}
_ = db.NewSelect().
  TableExpr("posts AS p").
  ColumnExpr("COALESCE(c.name,'uncategorized') AS category").
  ColumnExpr("COUNT(*) AS count").
  Join("LEFT JOIN categories c ON c.id = p.category_id").
  Where("p.deleted_at IS NULL").
  GroupExpr("category").
  OrderExpr("count DESC").
  Scan(ctx)
````

---

## 17. Динамическая фильтрация

````go
func FilterPosts(q *bun.SelectQuery, f map[string]any) *bun.SelectQuery {
  if v, ok := f["user_id"]; ok { q = q.Where("p.user_id = ?", v) }
  if v, ok := f["category_id"]; ok { q = q.Where("p.category_id = ?", v) }
  if v, ok := f["tag"]; ok {
    q = q.
      Join("JOIN post_tags pt ON pt.post_id = p.id").
      Join("JOIN tags t ON t.id = pt.tag_id").
      Where("t.name = ?", v)
  }
  if v, ok := f["search"]; ok {
    pattern := "%" + v.(string) + "%"
    q = q.WhereGroup(" AND ", func(q *bun.SelectQuery) *bun.SelectQuery {
      return q.Where("p.title ILIKE ?", pattern).
        WhereOr("p.body ILIKE ?", pattern)
    })
  }
  return q
}
````

---

## 18. Optimistic Lock (ручной)

````go
var p model.Post
_ = db.NewSelect().Model(&p).Where("id = ?", id).Scan(ctx)
old := p.Version
p.Title = "New"
res, _ := db.NewUpdate().
  Model(&p).
  Set("title = ?", p.Title).
  Set("version = version + 1").
  Where("id = ?", p.ID).
  Where("version = ?", old).
  Exec(ctx)
rows, _ := res.RowsAffected()
if rows == 0 {
  // конфликт
}
````

---

## 19. Hooks

````go
func (p *Post) BeforeAppendModel(ctx context.Context, q bun.Query) error {
  switch q.(type) {
  case *bun.InsertQuery:
    if p.CreatedAt.IsZero() { p.CreatedAt = time.Now() }
  case *bun.UpdateQuery:
    p.UpdatedAt = time.Now()
  }
  return nil
}
````

---

## 20. JSON / JSONB

Поле:
````go
Metadata map[string]any `bun:"metadata,type:jsonb"`
````

Частичное обновление JSONB:
````go
_, _ = db.NewUpdate().
  Model((*model.Post)(nil)).
  Set(`metadata = metadata || ?::jsonb`, `{"views":10}`).
  Where("id = ?", postID).
  Exec(ctx)
````

---

## 21. Raw SQL

````go
var count int
_ = db.NewRaw("SELECT COUNT(*) FROM posts WHERE user_id = ?", userID).Scan(ctx, &count)
````

---

## 22. Индексы и оптимизация

Рекомендации:
- (user_id, created_at DESC)
- GIN по to_tsvector для полнотекста
- partial index WHERE deleted_at IS NULL
- Индекс на pivot (post_id, tag_id) UNIQUE

Partial index пример:
````sql
CREATE INDEX IF NOT EXISTS idx_posts_not_deleted
ON posts(id)
WHERE deleted_at IS NULL;
````

---

## 23. Локинг

````go
var p model.Post
_ = db.NewSelect().Model(&p).
  Where("p.id = ?", id).
  For("UPDATE").
  Scan(ctx)
````

---

## 24. Тестирование SQLite

````go
import (
  "database/sql"
  _ "github.com/mattn/go-sqlite3"
  "github.com/uptrace/bun"
  "github.com/uptrace/bun/dialect/sqlitedialect"
)
func TestDB() *bun.DB {
  sqldb, _ := sql.Open("sqlite3", "file::memory:?cache=shared")
  db := bun.NewDB(sqldb, sqlitedialect.New())
  ctx := context.Background()
  models := []any{
    (*model.User)(nil),
    (*model.Category)(nil),
    (*model.Tag)(nil),
    (*model.Post)(nil),
    (*model.PostTag)(nil),
  }
  for _, m := range models {
    db.NewCreateTable().Model(m).IfNotExists().Exec(ctx)
  }
  return db
}
````

---

## 25. Частые Relation пути

- Relation("User")
- Relation("Category")
- Relation("Tags")
- Relation("User.Posts") — вложенно (будет доп. запрос)
- Ограничение колонок:
````go
Relation("User", func(q *bun.SelectQuery)*bun.SelectQuery {
  return q.Column("u.id","u.name")
})
````

---

## 26. Troubleshooting (кратко)

| Ситуация | Причина | Исправление |
|----------|---------|-------------|
| Не грузится связь | Ошибочный join | Сверить join:левое=правое |
| Дубликаты при M2M | Нет уникального индекса | Добавить UNIQUE(post_id, tag_id) |
| JSON хранится как TEXT | Нет type:jsonb | Добавить тег |
| Запись "пропала" | soft_delete | WhereAllWithDeleted |
| N+1 на больших наборах | Слишком много Relation глубины | Сократить, использовать отдельные запросы |

---

## 27. Быстрые рецепты

Insert RETURNING:
````go
p := new(model.Post)
_, _ = db.NewInsert().Model(p).
  Value("user_id", 1).
  Value("title", "X").
  Returning("*").
  Exec(ctx)
````

Exists:
````go
exists, _ := db.NewSelect().
  Model((*model.User)(nil)).
  Where("email = ?", email).
  Exists(ctx)
````

Distinct:
````go
var categories []string
_ = db.NewSelect().
  Model((*model.Category)(nil)).
  Column("name").
  Distinct().
  Scan(ctx, &categories)
````

CASE:
````go
var rows []struct{ ID int64; Status string }
_ = db.NewSelect().
  TableExpr("posts AS p").
  ColumnExpr("p.id").
  ColumnExpr(`
    CASE 
      WHEN p.deleted_at IS NOT NULL THEN 'deleted'
      WHEN p.metadata->>'status' = 'draft' THEN 'draft'
      ELSE 'active'
    END AS status`).
  Scan(ctx, &rows)
````

---

## 28. Fulltext (PostgreSQL)

````go
query := "go bun"
var posts []model.Post
_ = db.NewSelect().
  Model(&posts).
  Where("to_tsvector('simple', p.title || ' ' || p.body) @@ plainto_tsquery(?)", query).
  OrderExpr("p.created_at DESC").
  Limit(20).
  Scan(ctx)
````

---

## 29. Диагностика производительности

EXPLAIN ANALYZE:
````go
var explain []string
_ = db.NewRaw("EXPLAIN ANALYZE SELECT * FROM posts WHERE user_id = ?", 10).
  Scan(ctx, &explain)
````

Логгер: bundebug (включён выше).  
Снижайте Relation где не нужно — используйте точечно.

---

## 30. Пример сервисной функции CreatePost (с тегами и категорией)

````go
func CreatePost(ctx context.Context, db *bun.DB, userID int64, catName, title, body string, tagNames []string) (*model.Post, error) {
  var cat model.Category
  if err := db.NewSelect().Model(&cat).Where("name = ?", catName).Scan(ctx); err != nil {
    cat = model.Category{Name: catName}
    if _, err2 := db.NewInsert().Model(&cat).On("CONFLICT (name) DO NOTHING").Exec(ctx); err2 != nil {
      return nil, err2
    }
    if cat.ID == 0 {
      _ = db.NewSelect().Model(&cat).Where("name = ?", catName).Scan(ctx)
    }
  }
  post := &model.Post{
    UserID:     userID,
    CategoryID: &cat.ID,
    Title:      title,
    Body:       body,
    Metadata:   map[string]any{"status":"draft"},
  }
  err := db.RunInTx(ctx, nil, func(ctx context.Context, tx bun.Tx) error {
    if _, err := tx.NewInsert().Model(post).Exec(ctx); err != nil {
      return err
    }
    // теги
    var tags []*model.Tag
    for _, name := range tagNames {
      t := &model.Tag{Name:name}
      if _, err := tx.NewInsert().Model(t).On("CONFLICT (name) DO NOTHING").Exec(ctx); err != nil {
        return err
      }
      if t.ID == 0 {
        if err := tx.NewSelect().Model(t).Where("name = ?", name).Scan(ctx); err != nil {
          return err
        }
      }
      tags = append(tags, t)
    }
    // pivot
    if len(tags) > 0 {
      pts := make([]model.PostTag, 0, len(tags))
      for _, t := range tags {
        pts = append(pts, model.PostTag{PostID: post.ID, TagID: t.ID})
      }
      if _, err := tx.NewInsert().Model(&pts).On("CONFLICT DO NOTHING").Exec(ctx); err != nil {
        return err
      }
    }
    return nil
  })
  if err != nil { return nil, err }
  return post, nil
}
````

---

## 31. HTTP Handler пример

````go
func GetPostHandler(w http.ResponseWriter, r *http.Request) {
  ctx := r.Context()
  idStr := r.URL.Query().Get("id")
  id, _ := strconv.ParseInt(idStr, 10, 64)
  var p model.Post
  err := db.NewSelect().
    Model(&p).
    Relation("User").
    Relation("Tags").
    Relation("Category").
    Where("p.id = ?", id).
    Scan(ctx)
  if err != nil {
    http.Error(w, err.Error(), 404)
    return
  }
  json.NewEncoder(w).Encode(p)
}
````

---

## 32. Чеклист новой модели

1. Имя таблицы + alias  
2. PK + autoincrement / UUID  
3. Внешние ключи (fk поле + belongs-to relation)  
4. has-many или m2m поля с корректным join  
5. JSON поля с type:jsonb  
6. soft_delete если нужно логическое удаление  
7. Миграция + индексы (часто по FK)  
8. Тест: Insert + Select + Relation  
9. Добавить в сервисы + валидацию  

---

## 33. Практические советы по проектированию схемы

- FK всегда делайте NOT NULL если логически обязательна связь (UserID для Post)  
- NULL FK для опциональных связей (CategoryID)  
- M2M: почти всегда лучше добавить pivot модель (для future полей weight, sequence)  
- Если у вас частая фильтрация по тегам — рассмотрите денормализацию (materialized view / inverted index)  
- Не перегружайте одну выборку Relation("*") — лучше несколько узких запросов  
- Для массового импорта: используйте pgx Copy (в обход ORM)  
- Проверяйте планы запросов (EXPLAIN) после добавления сложных фильтров  

---

## 34. Итоги

Связи в Bun просты и эксплицитны:
- One-to-Many через rel:has-many + join:parent_pk=child_fk
- Belongs-To через rel:belongs-to + поле FK
- Many-to-Many через m2m:pivot,join:This=Other + опциональная модель pivot
- Eager loading управляем через Relation(...) — нет ленивой загрузки
- Производительность зависит от узких выборок (Column / Relation с кастомизацией)