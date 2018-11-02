Необходимо создать репозиторий на Github. И при выборе языка выбрать Java и разрешаете создать README.md.

![img](https://media.githubusercontent.com/media/rmuhamedgaliev-mkdev/quest-static/master/s_3A115BA58734691B1302BD91AB4772A86C50A833094189F3EF82362B5514A161_1519463776248_Create%2Ba%2BNew%2BRepository%2B2018-02-24%2B12-14-05.png)

Нажимете “Create repository” и открывается страница с новым репозиторием. 

![img](https://media.githubusercontent.com/media/rmuhamedgaliev-mkdev/quest-static/master/s_3A115BA58734691B1302BD91AB4772A86C50A833094189F3EF82362B5514A161_1519463970456_rmuhamedgalievjava-play%2B2018-02-24%2B12-19-17.png)

Затем клонируем репозиторий к себе на компьютер. Это можно как при помощи приложения Github так и консольными утилитами.
Адрес репозитория находится за кнопкой “Clone or download”

![img](https://media.githubusercontent.com/media/rmuhamedgaliev-mkdev/quest-static/master/s_3A115BA58734691B1302BD91AB4772A86C50A833094189F3EF82362B5514A161_1519464411089_rmuhamedgalievjava-play%2B2018-02-24%2B12-25-07.png)

Затем необходимо создать ветку `develop`, которая предназначена для предрелизных версий продукта. Команда создания ветки 

```
git checkout -b develop
git push origin develop
```

Далее необходимо создать ветку для задания:

```
git checkout -b 1_repository-setup 
git push origin 1_repository-setup
```

Интерактивный курс по Git доступен по [ссылке](https://try.github.io/levels/1/challenges/1)

Все последующие изменения относительно задания будут вестись в данной ветке. Как задание будет выполнено необходимо оформить [pull request в develop](https://help.github.com/articles/about-pull-requests/). 

Работа будет вестись при помощи [GitHub Flow](https://habr.com/post/189046/).

![img](https://media.githubusercontent.com/media/rmuhamedgaliev-mkdev/quest-static/master/s_3A115BA58734691B1302BD91AB4772A86C50A833094189F3EF82362B5514A161_1519465040548_tty.gif)

Затем инициализируем проект при помощи gradle.

```
./gradlew init —type java-application
./gradlew build
./gradlew run
```

Конфигурационный файл `build.gradle` должен содержать [wrapper](https://docs.gradle.org/current/userguide/gradle_wrapper.html) конфигурацию. Для этого добавьте в конец файла `build.gradle` следующую функцию.

```
wrapper {
    gradleVersion = '4.10.2' // Желаемая версия
}
```

Добавить в коллабарторы ментора [Ринат Мухамедгалиев](https://github.com/rmuhamedgaliev). 

![img](https://media.githubusercontent.com/media/rmuhamedgaliev-mkdev/quest-static/master/Monosnap%202018-04-05%2023-40-01.png)

1. Перейти в репозиторий
2. Настройки
3. Сотрудники
4. Ввести ник ментора `rmuhamedgaliev`

Выдать права на запись в репозиторий и принятие пуллреквестов согласно [документации](https://help.github.com/articles/permission-levels-for-a-user-account-repository/#collaborator-access-on-a-repository-owned-by-a-user-account)

Базовая настройка репозитория закончена. Теперь можно создавать свои пакеты и классы приложения.
Все манипуляции производились в репозитории https://github.com/rmuhamedgaliev/java-play