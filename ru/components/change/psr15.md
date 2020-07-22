## Замена PSR-15

Изначально фреймворк Furious использует [родной компонент PSR-15](https://github.com/Furious-PHP/psr15)

Попробуем заменить родной компонент на [реализацию от Laminas](https://github.com/laminas/laminas-stratigility)

1 Изменяем зависимости в composer.json

Было

    ...
    "furious/psr15": "^1.0",
    ...

Стало

    ...
    "laminas/laminas-stratigility": "^3.2",
    ...

2 Создаем LaminasPipelineAdapter

Пример:
    
    <?php
    
    declare(strict_types=1);
    
    namespace Framework\Http\Pipeline;
    
    use Laminas\Stratigility\MiddlewarePipe;
    use Psr\Http\Message\ResponseInterface;
    use Psr\Http\Message\ServerRequestInterface;
    use Psr\Http\Server\MiddlewareInterface;
    use Psr\Http\Server\RequestHandlerInterface;
    
    final class LaminasPipelineAdapter implements MiddlewarePipeInterface
    {
        private MiddlewarePipe $pipeline;
    
        public function __construct()
        {
            $this->pipeline = new MiddlewarePipe();
        }
    
        public function pipe(MiddlewareInterface $middleware): void
        {
            $this->pipeline->pipe($middleware);
        }
    
        public function handle(ServerRequestInterface $request): ResponseInterface
        {
            return $this->pipeline->handle($request);
        }
    
        public function process(ServerRequestInterface $request, RequestHandlerInterface $handler): ResponseInterface
        {
            return $this->pipeline->process($request, $handler);
        }
    }
    
3 Инжектим LaminasPipelineAdapter

До:
    
    $container->set(Application::class, function (Container $container) {
        return new Application(
            $container->get(MiddlewareResolver::class),
            $container->get(Router::class),
            $container->get(NotFoundHandler::class),
            $container->get(FuriousPipelineAdapter::class)
        );
    });
    
После:

    $container->set(Application::class, function (Container $container) {
        return new Application(
            $container->get(MiddlewareResolver::class),
            $container->get(Router::class),
            $container->get(NotFoundHandler::class),
            $container->get(LaminasPipelineAdapter::class)
        );
    });
    
    
Готово!