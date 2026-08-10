# DWH Team Skills

Приватный хаб скиллов команды DWH для Hermes Agent.

## Структура

```
skills/
  dwh-team/                        # Процессы и workflows команды
    switch-chatgpt-credential/     # Переключение ChatGPT OAuth аккаунтов
      SKILL.md
  integrations/                    # Инструменты и интеграции
    (clickhouse/                   # Пример: сюда кладём скиллы инструментов)
    (gitlab/)
    (jira/)
    (grafana/)
    (airflow/)
```

## Установка

```bash
# Один раз добавить tap
hermes skills tap add artem-gromov/dwh-team-skills

# Установить скилл команды (категория dwh-team)
hermes skills install artem-gromov/dwh-team-skills/dwh-team/switch-chatgpt-credential --category dwh-team

# Установить интеграцию (категория integrations)
# hermes skills install artem-gromov/dwh-team-skills/integrations/clickhouse --category integrations
```

> **Важно:** флаг `--category` обязателен — без него скилл установится в корень `~/.hermes/skills/` без категории. Путь в репо (`dwh-team/...`) не маппится автоматически на локальную категорию.

## Замена локально установленных скиллов на hub-версию

Если скилл уже установлен локально (вручную или через `skill_manage`), его нужно заменить на hub-версию из репо. Это даёт:
- единый источник правды (репо)
- обновления через `hermes skills update`
- возможность устанавливать у других участников команды

### Пошаговая миграция

**1. Посмотри, что установлено локально:**
```bash
hermes skills list
# или напрямую:
ls ~/.hermes/skills/integrations/
ls ~/.hermes/skills/dwh-team/
```

**2. Добавь скилл в репо** (если его там ещё нет):
```bash
# Копируем SKILL.md в репо с правильной категорией
mkdir -p skills/<category>/<skill-name>/
cp ~/.hermes/skills/<category>/<skill-name>/SKILL.md skills/<category>/<skill-name>/
git add -A && git commit -m "add: <skill-name>" && git push
```

**3. Удали локальную версию:**
```bash
hermes skills uninstall <skill-name>
# Подтверди удаление (y)
```

**4. Установи через hub:**
```bash
hermes skills install artem-gromov/dwh-team-skills/<category>/<skill-name> --category <category> --yes
```

**5. Проверь:**
```bash
hermes skills inspect artem-gromov/dwh-team-skills/<category>/<skill-name>
# Должно показать Source: skills-sh, Identifier: skills-sh/artem-gromov/...
```

### Скиллы, которые стоит мигрировать

Установлено локально в `integrations/`:
| Скилл | Категория | Статус |
|---|---|---|
| airflow-debugging | integrations | 🔄 можно мигрировать |
| clickhouse | integrations | 🔄 можно мигрировать |
| clickhouse-dashboard-users | integrations | 🔄 можно мигрировать |
| clickhouse-roles | integrations | 🔄 можно мигрировать |
| confluence | integrations | 🔄 можно мигрировать |
| dbt-release | integrations | 🔄 можно мигрировать |
| dwh-team | integrations | 🔄 можно мигрировать |
| gitlab | integrations | 🔄 можно мигрировать |
| grafana | integrations | 🔄 можно мигрировать |
| jira | integrations | 🔄 можно мигрировать |
| jira-servicedesk | integrations | 🔄 можно мигрировать |
| powerbi-analytics | integrations | 🔄 можно мигрировать |
| rocketchat | integrations | 🔄 можно мигрировать |
| teleport | integrations | 🔄 можно мигрировать |

Отдельно:
| Скилл | Категория | Статус |
|---|---|---|
| dbt-clickhouse-patterns | (root) | 🔄 можно мигрировать в integrations |

> **Примечание:** мигрировать все сразу не обязательно. Начни с тех, которыми пользуется вся команда. Остальные можно добавлять по мере необходимости.

## Добавление нового скилла

1. Выбери категорию: `dwh-team/` для процессов команды, `integrations/` для инструментов
2. Создай директорию `<category>/<skill-name>/`
3. Положи внутрь `SKILL.md` с frontmatter:
   ```yaml
   ---
   name: my-skill
   description: "Краткое описание"
   ---
   ```
4. Закоммить и запушь

## Скиллы

### dwh-team — Процессы команды
| Скилл | Описание |
|---|---|
| switch-chatgpt-credential | Переключение ChatGPT OAuth аккаунтов |

### integrations — Инструменты
| Скилл | Описание |
|---|---|
| _(пусто)_ | Сюда добавляем clickhouse, gitlab, jira и т.д. |
