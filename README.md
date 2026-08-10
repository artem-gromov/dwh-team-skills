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
hermes skills install artem-gromov/dwh-team-skills/dwh-team/switch-chatgpt-credential

# Установить интеграцию (категория integrations)
# hermes skills install artem-gromov/dwh-team-skills/integrations/clickhouse
```

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
