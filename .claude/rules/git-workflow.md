# Работа с Git

## Структура веток

### Основные ветки

```
main                    # Стабильная, релизная версия
└── develop             # Ветка активной разработки
```

### Временные ветки

```
feature/TASK-ID-краткое-описание    # Новая функциональность
bugfix/TASK-ID-краткое-описание     # Исправление ошибки
hotfix/краткое-описание              # Срочное исправление в production
release/X.Y.Z                        # Подготовка релиза
```

### Примеры именования

```
feature/TASK-123-api-orders-endpoint
feature/REFACTOR-45-audit-modules
bugfix/TASK-456-fix-null-reference
hotfix/critical-auth-bypass
release/2.1.0
```

---

## Workflow разработки

### Feature-разработка

```
1. git checkout develop
2. git pull origin develop
3. git checkout -b feature/TASK-123-описание
4. ... разработка ...
5. git add <файлы>
6. git commit -m "feat(scope): описание"
7. git push -u origin feature/TASK-123-описание
8. Создать Pull Request в develop
9. После merge удалить feature-ветку
```

### Hotfix

```
1. git checkout main
2. git pull origin main
3. git checkout -b hotfix/описание
4. ... исправление ...
5. git commit -m "fix: описание критического исправления"
6. git push -u origin hotfix/описание
7. PR в main И в develop
8. После merge удалить hotfix-ветку
```

---

## Формат коммитов

### Структура сообщения

```
<тип>(<scope>): <краткое описание>

<подробное описание, если нужно>

Refs: TASK-123
```

### Типы коммитов

| Тип | Когда использовать | Пример |
|-----|-------------------|--------|
| `feat` | Новая функциональность | `feat(api): добавить endpoint заказов` |
| `fix` | Исправление ошибки | `fix(auth): исправить проверку токена` |
| `refactor` | Рефакторинг без изменения поведения | `refactor(orders): упростить валидацию` |
| `docs` | Документация | `docs: обновить API спецификацию` |
| `test` | Тесты | `test(orders): добавить тесты создания` |
| `chore` | Рутинные задачи | `chore: обновить зависимости` |
| `perf` | Оптимизация производительности | `perf(query): оптимизировать запрос остатков` |

### Scope для 1С проектов

| Scope | Описание |
|-------|----------|
| `api` | HTTP-сервисы, REST API |
| `auth` | Авторизация, аутентификация |
| `orders` | Модуль заказов |
| `catalog` | Справочники, номенклатура |
| `reports` | Отчёты |
| `forms` | Формы |
| `common` | Общие модули |

### Примеры коммитов

```
feat(api): добавить endpoint получения статуса заказа

Реализован GET /v1/orders/{id}/status
Возвращает текущий статус и историю изменений

Refs: TASK-123
```

```
fix(orders): исправить расчёт скидки для VIP клиентов

Скидка применялась дважды при определённых условиях.
Добавлена проверка на повторное применение.

Refs: TASK-456
```

---

## Правила работы с ветками

### Обязательные правила

1. **НЕ работать напрямую в main/develop**
2. **Одна задача = одна ветка**
3. **Rebase перед merge** (если нет конфликтов)
4. **Squash коммитов** в один логический при merge
5. **Удалять ветки** после merge

### Рекомендации

| Ситуация | Действие |
|----------|----------|
| Конфликты при rebase | Использовать merge |
| Долгоживущая ветка | Периодически синхронизировать с develop |
| Несколько задач в ветке | Разделить на отдельные ветки |
| Незаконченная работа | Использовать WIP: в сообщении коммита |

---

## Pull Request

### Шаблон описания PR

```markdown
## Описание
[Краткое описание изменений]

## Тип изменения
- [ ] Новая функциональность
- [ ] Исправление ошибки
- [ ] Рефакторинг
- [ ] Документация

## Связанные задачи
- TASK-123

## Чек-лист
- [ ] Код соответствует стандартам проекта
- [ ] Добавлены/обновлены тесты
- [ ] Документация обновлена
- [ ] Проверено в тестовой базе

## Скриншоты (если применимо)
```

### Правила ревью

1. **Минимум 1 approve** перед merge
2. **Все комментарии** должны быть resolved
3. **CI/CD проверки** должны пройти
4. **Squash and merge** — предпочтительный способ

---

## Работа с конфликтами

### При rebase

```bash
git checkout feature/TASK-123
git fetch origin
git rebase origin/develop
# Если конфликты:
# 1. Разрешить конфликты в файлах
# 2. git add <файлы>
# 3. git rebase --continue
git push --force-with-lease
```

### При merge

```bash
git checkout feature/TASK-123
git fetch origin
git merge origin/develop
# Разрешить конфликты
git add <файлы>
git commit
git push
```

---

## Специфика 1С проектов

### .gitignore для 1С

```gitignore
# Временные файлы 1С
*.log
*.tmp
1Cv8.1CD
1Cv8tmp/
*.epf.bak
*.erf.bak

# Кеш EDT
.metadata/
.project
.settings/

# Пользовательские настройки
*.user
*.suo

# Системные файлы
Thumbs.db
.DS_Store
```

### Структура репозитория расширения

```
extension-name/
├── src/
│   ├── CommonModules/
│   │   └── МодульИмя/
│   │       └── Module.bsl
│   ├── Documents/
│   ├── Catalogs/
│   └── ...
├── .claude/
├── CLAUDE.md
└── README.md
```

---

## Теги и релизы

### Формат тегов

```
v1.2.3              # Версия релиза
v1.2.3-rc.1         # Release candidate
v1.2.3-beta.1       # Beta версия
```

### Создание релиза

```bash
git checkout main
git pull origin main
git tag -a v1.2.3 -m "Release 1.2.3: описание"
git push origin v1.2.3
```

---

## Автоматизация

### Git hooks (рекомендуемые)

| Hook | Назначение |
|------|------------|
| `pre-commit` | Проверка синтаксиса .bsl файлов |
| `commit-msg` | Валидация формата сообщения коммита |
| `pre-push` | Запуск тестов |

### Пример pre-commit для 1С

```bash
#!/bin/bash
# Проверка синтаксиса изменённых .bsl файлов

FILES=$(git diff --cached --name-only --diff-filter=ACM | grep '\.bsl$')

if [ -n "$FILES" ]; then
    echo "Проверка синтаксиса..."
    # Вызов проверки через MCP или bsl-language-server
fi
```
