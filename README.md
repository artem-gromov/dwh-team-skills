# DWH Team Skills

Приватный хаб скиллов команды DWH для Hermes Agent.

## Установка

```bash
# Добавить как tap (источник скиллов)
hermes skills tap add artem-gromov/dwh-team-skills

# Установить конкретный скилл
hermes skills install artem-gromov/dwh-team-skills/switch-chatgpt-credential
```

## Структура

```
<skill-name>/
  SKILL.md          # Обязательный — YAML frontmatter + markdown
  references/       # Справочные материалы (опционально)
  scripts/          # Скрипты (опционально)
  templates/        # Шаблоны (опционально)
```

## Добавление нового скилла

1. Создай директорию `<skill-name>/`
2. Положи внутрь `SKILL.md` с frontmatter:
   ```yaml
   ---
   name: my-skill
   description: "Краткое описание"
   ---
   ```
3. Закоммить и запушь — участники команды смогут установить через `hermes skills install`

## Скиллы

| Скилл | Описание |
|---|---|
| switch-chatgpt-credential | Переключение ChatGPT OAuth аккаунтов |
