## Консольные команды

В Skeleton изначально отсутвуют консольные команды

В API и Web skeleton используется [компонент от Symfony](https://github.com/symfony/console).

#### Запуск команд

    php bin/console консольная:команда

#### Список готовых команд
* say:hello - Говорит "Hello"
* cycle:diff - Автоматически создает миграцию
* cycle:run - Выполняет миграции
* user:create - Создает нового пользователя

#### Создание своих команд

Для того чтобы создать свою консольную команду нужно создать класс команды
(можно положить в любую папку, 
изначально они находятся в папке src/App/Console/Commands) и добавить команду
в конфиг config/params/console.php:

    <?php
    
    use App\Console\Commands;
    
    return [
        'console' => [
            'commands' => [
                Commands\HelloCommand::class,
                Commands\Cycle\RunMigrationsCommand::class,
                Commands\Cycle\CreateMigrationCommand::class,
                Commands\User\CreateCommand::class,
                ВашаЛюбаяКоманда::class
                ...
            ]
        ]
    ];