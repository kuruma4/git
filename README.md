# Git: шпаргалка, которой пользуюсь каждый день

Короткий справочник по командам, которые реально нужны в работе: без
теории ветвления и без «а вот в 2008 году git был другим».

## Настройка (один раз)

```bash
git config --global user.name "Фёдор Толмачев"
git config --global user.email "you@example.com"
git config --global init.defaultBranch main
git config --global pull.rebase true       # pull через rebase, без лишних merge-коммитов
git config --global core.autocrlf true     # Windows: не ломать переводы строк
```

## Каждый день

```bash
git status                     # что изменено
git diff                       # что именно изменено (до индексации)
git add -p                     # добавлять по кускам, а не всё подряд
git commit -m "feat: ..."      # осмысленное сообщение
git push
```

## Ветки

```bash
git switch -c feature/login    # создать и перейти
git switch main                # перейти
git branch -d feature/login    # удалить локальную (после merge)
git push -u origin feature/x   # первый push новой ветки
```

## Откаты: что чем лечится

| Ситуация | Команда |
|---|---|
| Забыл файл в последнем коммите | `git add file && git commit --amend --no-edit` |
| Плохое сообщение коммита | `git commit --amend -m "новое"` |
| Отменить изменения в файле | `git restore file.py` |
| Убрать файл из индекса, оставить на диске | `git restore --staged file.py` |
| Отменить последний коммит, оставив изменения | `git reset --soft HEAD~1` |
| Отменить последний коммит полностью | `git reset --hard HEAD~1` ⚠️ |
| Вернуть удалённый коммит | `git reflog` → `git reset --hard <hash>` |
| Отменить публичный коммит | `git revert <hash>` (создаёт обратный коммит) |

⚠️ `reset --hard` и `push --force` стирают чужую работу. В ветку, где работают
другие, — только `revert`. Если очень нужно переписать историю своей ветки:

```bash
git push --force-with-lease     # безопаснее, чем --force
```

## Конфликты

```bash
git pull
# правим файлы с маркерами <<<<<<< ======= >>>>>>>
git add конфликтный_файл.py
git commit                     # merge завершается коммитом
```

Полезно перед началом:

```bash
git config --global merge.conflictstyle zdiff3   # показывает общий предок
```

## Спрятать в сторону

```bash
git stash push -m "недоделанный рефакторинг"
git stash list
git stash pop                  # вернуть и удалить из списка
```

## Что сделано и кем

```bash
git log --oneline --graph --all -20
git log -p file.py             # история одного файла с диффами
git blame file.py              # кто и когда менял строки
git log --author="kuruma4" --since="2 weeks ago"
```

## Мусор и чистка

```bash
git clean -nd                   # показать, что будет удалено (dry-run)
git clean -fd                   # удалить неотслеживаемые файлы и папки
git remote prune origin         # убрать ссылки на удалённые ветки
git gc --prune=now             # сжать репозиторий
```

## Правила, которые экономят нервы

1. **`.gitignore` — до первого коммита.** `.env`, `__pycache__`, `*.db`,
   виртуальные окружения, бинарники. Секрет, попавший в историю, придётся
   считать скомпрометированным и менять.
2. **Один коммит — одно изменение.** «Поправил всё» не откатывается.
3. **`git diff` перед `git add`.** Половина проблем видна ещё здесь.
4. **Ветка на задачу.** Даже если работаешь один: `main` всегда рабочий.
5. **Не `push --force` в общие ветки.** Никогда.
6. **`reflog` спасает почти всё.** Если что-то «пропало» — сначала туда.

## Мелочи, которые часто спрашивают

```bash
git rm --cached file           # перестать отслеживать файл, оставив его на диске
git mv old.py new.py           # переименовать с сохранением истории
git show <hash>:file.py        # содержимое файла на момент коммита
git diff main..feature/x       # разница между ветками
git shortlog -sn               # кто сколько коммитов сделал
```

---

Собрано по ходу работы. Дополняется, когда очередная команда снова
понадобилась и снова забылась.

Telegram: [@kuruma44](https://t.me/kuruma44)
