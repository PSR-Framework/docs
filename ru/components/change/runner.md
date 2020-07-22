## Замена Http Runner

Изначально фреймворк Furious использует [родной компонент Runner'а](https://github.com/Furious-PHP/http-runner)

Попробуем заменить родной компонент на [реализацию Runner'а от Laminas (Zend)](https://github.com/laminas/laminas-httphandlerrunner)

1 Изменяем зависимости в composer.json
Было

    ...
    "furious/container": "1.0.2",
    "laminas/laminas-diactoros": "^2.3",
    "furious/http-runner": "0.1.4",
    ...

Стало

    ...
    "furious/container": "1.0.2",
    "laminas/laminas-diactoros": "^2.3",
    "laminas/laminas-httphandlerrunner": "^1.2"
    ...
    
2 Меняем Runner в public/index.php

Было

    use Furious\HttpRunner\Runner;
    ...
    $runner->run($response);
    
Стало
    
    use Laminas\HttpHandlerRunner\Emitter\SapiEmitter as Runner;
    ...
    $runner->emit($response);
    
Готово!