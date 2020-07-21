## Настройка база данных

В Skeleton изначально отсутствует ORM

В API и Web skeleton используется [Cycle ORM](https://github.com/cycle/orm)

#### Настройка подключения

Конфиг для подключения к бд находится в папке config/params

Для того чтобы настроить подключение к базе данных необходимо скопировать файл
db.php в db.local.php
    
    use Spiral\Database;
    
    return [
        'db' => [
            'default_connection' => 'postgres',
            'connections' => [
                'postgres' => [
                    'driver'  => Database\Driver\Postgres\PostgresDriver::class,
                    'connection' => 'pgsql:host=postgres;dbname=dbname',
                    'username'   => 'username',
                    'password'   => 'password'
                ]
            ]
        ]
    ];
    
Изначально конфиг подобран под PostgresSQL, но вы можете использовать любую
другую базу данных, которую поддерживает Cycle ORM. [Подробнее](https://github.com/cycle/docs/blob/master/basic/connect.md)

#### Настройка миграций

Вы можете настроить миграции в файле config/params/orm.php
Изначально миграции будут находиться в папке /src/App/Database/Migration/
    
    return [
        'orm' => [
            'migrations' => [
                'directory' => __DIR__ . '/../../src/App/Database/Migration',
                'table' => 'migrations'
            ]
        ]
    ];

#### Схемы
Настроить их можно в файле config/params/schemas.php.
[Подробнее](https://github.com/cycle/docs/blob/master/advanced/schema.md) 