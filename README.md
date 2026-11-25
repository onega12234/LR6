# LR6
Лабораторная работа №6

**Дисциплина:** основы программирования
**Студент:** гр. 4517 Онегина А.И.

**Цель работы:** изучить базовые возможности системы управления версиями, получить опыт работы с Git, а также с локальным и удалённым репозиторием.

Репозиторий получен форком из Kurtyanik/LR6. Снимки экрана лежат в папке `shots/`.

## 1. Настройка клиента и клонирование

Git настроен локально для этого репозитория. Имя пользователя задано в формате «Группа Фамилия И.О.». После клонирования в репозитории один удалённый источник `origin` и три коммита исходного автора.

![](shots/01-config.png)

## 2. История операций по каждой ветке

В репозитории две ветки: `master` и `branch1`. Они разошлись после коммита `c08a654`, в котором файл `mergefile.txt` был создан пустым. Затем каждая ветка заполнила файл по-своему, поэтому при слиянии возникнет конфликт.

Команда `git log --oneline --graph --all` показывает обе ветки сразу и точку расхождения. Последние изменения видны через `git show --stat`.

![](shots/02-history.png)

## 3. Слияние веток и разрешение конфликта

Слияние `branch1` в `master` остановилось на конфликте: обе ветки изменили один и тот же файл `mergefile.txt`, и Git не смог выбрать версию сам. Он вставил в файл маркеры: между `«««< HEAD` и `=======` версия текущей ветки, между `=======` и `»»»> branch1` версия из `branch1`.

![](shots/03-conflict.png)

Конфликт разрешён вручную: я записала в файл одну строку, в которой объединены обе версии, а маркеры удалила. После `git add` файл перестал быть конфликтным, и слияние закрылось коммитом.

![](shots/04-resolved.png)

## 4. Удаление побочной ветки

После успешного слияния ветка `branch1` больше не нужна, я удалила её командой `git branch -d branch1`. Ключ `-d`, в отличие от `-D`, удаляет ветку, только если она уже слита, то есть защищает от потери коммитов.

## 5. Коммиты и откат

Сделано три последовательных коммита в файл `notes.txt`, у каждого свой комментарий. Третий коммит намеренно ошибочный.

![](shots/05-commits.png)
![](shots/06-commits.png)

Ошибочный коммит отменён командой `git revert`. Она не стирает историю, а создаёт новый коммит с обратными изменениями, поэтому безопасна для репозитория, уже отправленного на сервер. Строка из файла исчезла, а оба коммита, ошибочный и отменяющий, остались в истории.

![](shots/07-revert.png)

## 7. Лог команд

Команды приведены без результатов выполнения, в том порядке, в котором применялись.

~~~
git clone https://github.com/onega12234/LR6.git
cd LR6
mkdir shots
git config user.name "4517 Онегина А.И."
git config user.email "lyser_ia@mail.ru"
git config --local --get user.name
git config --local --get user.email
git remote -v
git log --oneline -n 4

git checkout -b branch1 origin/branch1
git checkout master
git branch -a
git log --oneline --graph --all
git log --oneline branch1
git show --stat HEAD

git merge branch1
git status --short
cat mergefile.txt
echo "Файл заполнен очень важной информацией из ветки master и информацией из ветки branch1" > mergefile.txt
cat mergefile.txt
git add mergefile.txt
git commit -m "Слияние branch1 в master: конфликт разрешён вручную"
git branch -d branch1
git log --oneline --graph -n 5

echo "Заметки по лабораторной работе №6" > notes.txt
git add notes.txt
git commit -m "Добавлен файл заметок по работе"
printf "Заметки по лабораторной работе №6\nВетка branch1 слита в master, конфликт решён вручную.\n" > notes.txt
git commit -am "Заметки: описано слияние веток"
printf "Заметки по лабораторной работе №6\nВетка branch1 слита в master, конфликт решён вручную.\nЭТА СТРОКА ДОБАВЛЕНА ПО ОШИБКЕ\n" > notes.txt
git commit -am "Ошибочная правка для демонстрации отката"
git log --oneline -n 4
cat notes.txt
git revert --no-edit HEAD
cat notes.txt
git log --oneline -n 3

git checkout -b report
git add README.md shots
git commit -m "Отчёт: цель работы, настройка клиента, история веток"
git commit -m "Отчёт: слияние с конфликтом, удаление ветки, откат"
git commit -m "Отчёт: лог использованных команд"
git log --pretty=format:"%h %ad %an %s" --date=short
git push origin master
git push origin report
git pull --ff-only origin report
git show --stat HEAD
git log --pretty=format:"%h %ad %an %s" --date=short
git add README.md shots/07-github.png
git commit -m "Отчёт: файл через GitHub и история операций"
git push origin report
~~~


