# 🏗️ Gitomyr Project

**Gitomyr** — це сучасна інфраструктура для університетського веб-застосунку на базі **Laravel 13**, розгорнута за допомогою **Docker**.

Проєкт орієнтований на DevOps-практики:

* автоматизоване тестування
* безпечне керування секретами
* CI/CD пайплайни
* контейнеризовану інфраструктуру

---

# 🛠️ Технологічний стек

* **Backend:** PHP 8.4 (FPM) / Laravel 13
* **Database:** MariaDB 10.11
* **Containerization:** Docker / Docker Compose
* **CI/CD:** GitHub Actions + GHCR (GitHub Container Registry)
* **OS:** оптимізовано для Fedora (SELinux ready)

---

# 🚀 Швидкий старт (Local Development)

## 1. Клонування та налаштування оточення

```bash
git clone https://github.com/Cookraine/gitomyr.git
cd gitomyr

cp src/.env.example src/.env
cp src/.env.example src/.env.testing
```

⚠️ **Важливо**

Для запуску тестів використовується окремий файл `.env.testing`,
де конфігуруються доступи до бази даних **MariaDB**.

**Ніколи не додавайте реальні паролі в `phpunit.xml`.**

---

## 2. Запуск інфраструктури

```bash
docker-compose up -d --build
```

---

## 3. Налаштування прав (для Fedora / RHEL користувачів)

Оскільки проєкт розробляється на **Fedora**, для коректної роботи Docker-контейнера з файловою системою хоста (враховуючи **SELinux**) виконайте:

```bash
# Надання прав UID 33 (www-data в контейнері)
sudo setfacl -R -m u:33:rwx src/
sudo setfacl -Rd -m u:33:rwx src/

# Встановлення міток SELinux (якщо не використовується суфікс :Z у compose)
sudo chcon -Rt svirt_sandbox_file_t src/
```

---

# 🧪 Тестування та автоматизація

## Git Hooks

Проєкт використовує **pre-push hook** для гарантування стабільності коду.

Кожен запуск `git push` автоматично запускає **PHPUnit** тести всередині контейнера.

**Локація:**

```
.git/hooks/pre-push
```

або символічне посилання на:

```
git-hooks/pre-push
```

**Механізм роботи:**

* якщо хоча б **один тест падає** — push **скасовується**

---

## Запуск тестів вручну

```bash
docker exec gitomyr.app php artisan test
```

---

# 🎡 CI/CD пайплайн (GitHub Actions)

Наш пайплайн автоматизує повний цикл доставки коду.

### 1. Lint & Test

* запускає **MariaDB 10.11** у сервісному контейнері
* виконує **Feature та Unit тести**

### 2. Docker Build

Збирає Docker-образ на базі `Dockerfile`.

### 3. Publish to GHCR

Автоматично тегує образ і публікує його у **GitHub Container Registry**:

```
ghcr.io/cookraine/gitomyr-app:latest
```

ℹ️ **Note**

Пайплайн автоматично переводить назву репозиторію в **нижній регістр** для сумісності з Docker Registry.

---

# 📂 Структура репозиторію

```
src/                     Laravel application source code
docker/                  Docker configuration (Dockerfile, PHP settings)
.github/workflows/       CI/CD pipeline configuration
docker-compose.yml       Local infrastructure configuration (SELinux :Z support)
```
