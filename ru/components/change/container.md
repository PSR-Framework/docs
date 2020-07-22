## Замена Container

Изначально фреймворк Furious использует [родной компонент Container'а](https://github.com/Furious-PHP/di)

Попробуем заменить родной компонент на [реализацию от Symfony](https://github.com/symfony/dependency-injection)

1 Изменяем зависимости в composer.json

Было

    ...
    "furious/container": "1.0.2",
    ...

Стало

    ...
    "symfony/dependency-injection": "^5.1",
    ...

2 Меняем контейнер в config/container.php и в зависимостях

Было
    
    use Furious\Container\Container;
    
Стало

    use Symfony\Component\DependencyInjection\ContainerBuilder as Container;

3 Меняем зависимости и конфиги

Готово!