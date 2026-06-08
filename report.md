# Лабораторная работа №2. Изучение систем контроля версий на примере Git

## Настройка и первый коммит. 
Создан публичный репозиторий `lab2` с лицензией MIT. Настройка GIT:
```
git config --global user.name sabirovatv
git config --global user.email sabirovatamara77@gmail.com
```
Добавлен файл `README.md` и коммит:
```
git add README.md
git commit -m "added README.md"
git push origin main
```
Вывод: 
```
[main 1a2b3c4] added README.md 1 file changed, 3 insertions(+) create mode 100644 README.md
```

## Создание файла `gitignore`.
Сам файл `gitignore`:
```
*build*/
*install*/
*.swp
.idea/
```
Запушим файл:
```
git add .gitignore
git commit -m "added .gitignore"
git push origin main
```
Вывод:
```
[main a49c55b] added .gitignore 1 file changed, 4 insertions(+) create mode 100644 .gitignore
```

## Добавим исходный код.
Создадим папки ```sources/, include/, examples/```
Добавим в репозиторий:
```
git add sources/ include/ examples/
git commit -m "added sources"
git push origin main
```
Вывод: 
```
[main 722bf39] added sources 4 files changed, 31 insertions(+) create mode 100644 ...
```

## Домашнее задание
# Программа `Hello world`
Создадим файл `hello_world.cpp`  с намеренно некачественным кодом:
```
#include <iostream>
using namespace std;
int main() {
    cout << "Hello world" << endl;
    return 0;
}
```
Коммит: 
```
git add hello_world.cpp
git commit -m "hello_world bad code"
git push origin main
```
Вывод:
```
[main 781e958] hello_world bad code 1 file changed, 8 insertions(+) create mode 100644 hello_world.cpp
```
Логика изменена: теперь программа через `stdin` считывает имя и печатает `Hello world from @name`. Повторно выполнять `git add` не требуется, так как файл уже находится под версионным контролем (команда add нужна только для впервые добавленных файлов).
Тем не менее, для надёжности выполнена та же команда:
```
git add hello_world.cpp
git commit -m "feat: request name from stdin, print Hello world from @name"
git push origin main
```
Вывод:
```
[main 2614473] feat: request name from stdin, print Hello world from @name 1 file changed, 4 insertions(+), 1 deletion(-)
```
Создаём patch1
Формирование локальной ветки `patch1`
```
git checkout -b patch1
git push origin patch1
```
```
Switched to a new branch 'patch1'
```
Правка кода и внесение поясняющих комментариев
Внутри ветки patch1 сделано два последовательных коммита:
`fix: remove using namespace std` — устранение нежелательного объявления пространства имён, `docs: add inline comments` — добавлены комментарии прямо в коде
```
git add hello_world.cpp
git commit -m "fix: remove using namespace std"
git push origin patch1
```
```
[patch1 60496ef] fix: remove using namespace std 1 file changed, 5 insertions(+), 5 deletions(-)
```
```
git add hello_world.cpp
git commit -m "docs: add inline comments"
git push origin patch1
```
```
[patch1 3a42940] docs: add inline comments 1 file changed, 2 insertions(+)
```
Открытие Pull Request
Создан PR №1 с заголовком `patch1 -> main: fix code style`. В описании перечислены изменения. Конфликты отсутствуют.
```
gh pr create --title "patch1 -> main: fix code style" \
  --body "- Remove using namespace std\n- Add inline comments" \
  --head patch1 --base main
```
Интеграция PR и очистка ветки
Запрос на слияние успешно принят, ветка patch1 удалена как локально, так и на сервере.
```
gh pr merge patch1 --merge --delete-branch
```
```
Updating 2614473..aa029d4
Fast-forward
 hello_world.cpp | 12 +++++++-----
 1 file changed, 7 insertions(+), 5 deletions(-)
```
Ветка patch2 и разрешение конфликта
Cоздание patch2 и применение форматирования `clang-format`
От `main` ответвляется новая ветка `patch2`. К файлу `hello_world.cpp` применён автоматический форматтер `clang-format` с профилем Mozilla.
```
git checkout -b patch2
git add hello_world.cpp
git commit -m "style: apply clang-format Mozilla style"
git push origin patch2
```
Вывод:
```
Switched to a new branch 'patch2'
[patch2 dc93e57] style: apply clang-format Mozilla style 1 file changed, 2 insertions(+), 1 deletion(-)
```
Pull Request patch2 → main и возникновение конфликта
Создан PR №2 с описанием `Apply Mozilla clang-format style`. Однако параллельно в ветке main были изменены комментарии, из-за чего появился конфликт.
Изменения в main:
```
git checkout main
git add hello_world.cpp
git commit -m "docs: translate comments to English"
git push origin main
```
```
[main 97211d6] docs: translate comments to English 1 file changed, 2 insertions(+), 2 deletions(-)
```
Cоздание PR:
```
gh pr create --title "patch2 -> main: clang-format" \
  --body "Apply Mozilla clang-format style" \
  --head patch2 --base main
```
Устранение конфликта через `rebase`
Для разрешения конфликта использован rebase:
```
git checkout patch2
git add hello_world.cpp
git commit -m "fix: resolve rebase conflict — Mozilla style + English comments"
git push origin patch2
```
```
[patch2 3d53b5e] fix: resolve rebase conflict — Mozilla style + English comments 1 file changed, 2 insertions(+), 2 deletions(-)
```
Финальное слияние PR patch2 → main
Второй `Pull Request` также принят, ветка `patch2` удалена.
```
gh pr merge patch2 --merge --delete-branch
text
Updating 97211d6..563af60
Fast-forward
 hello_world.cpp | 3 ++-
 1 file changed, 2 insertions(+), 1 deletion(-)
```
