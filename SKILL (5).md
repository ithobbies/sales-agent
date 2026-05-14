---
name: django-backend-task
description: Use this skill for Django models, settings, migrations, admin, services, DRF serializers/views, and backend tests.
---

# Django Backend Task Skill

## Workflow

1. Read task acceptance criteria.
2. Inspect existing apps and settings.
3. Keep changes inside the relevant app.
4. Add or update models/services/serializers/views as needed.
5. Add migrations when models change.
6. Register important models in Django Admin.
7. Add tests.
8. Run relevant Django checks.

## Commands

```bash
cd backend
python manage.py check
python manage.py makemigrations --check --dry-run
python manage.py test
```

## Conventions

- Keep views thin.
- Put business logic in services.
- Keep integrations outside domain apps.
- Avoid raw SQL unless documented.
- Keep ORM portable for future PostgreSQL migration.
- Do not store secrets in settings or code.
