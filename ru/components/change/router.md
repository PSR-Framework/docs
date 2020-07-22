## Замена Router'a

Изначально фреймворк Furious использует [родной компонент Router'а](https://github.com/Furious-PHP/router)

Попробуем заменить родной компонент на [реализацию от Aura](https://github.com/auraphp/Aura.Router)

1 Изменяем зависимости в composer.json

Было

    ...
    "furious/router": "1.0.*",
    ...

Стало

    ...
    "aura/router": "^3.1",
    ...
    
    
2 Создаем AuraRouterAdapter

Пример:
    
    <?php
    
    declare(strict_types=1);
    
    namespace Framework\Http\Router;
    
    use Aura\Router\Exception\ImmutableProperty;
    use Aura\Router\Exception\RouteAlreadyExists;
    use Aura\Router\Exception\RouteNotFound;
    use Aura\Router\RouterContainer;
    use Framework\Http\Router\Exception\RequestNotMatchedException;
    use Framework\Http\Router\Exception\UnableToFoundRouteException;
    use InvalidArgumentException;
    use Psr\Http\Message\ServerRequestInterface;
    
    final class AuraRouterAdapter implements Router
    {
        private RouterContainer $aura;
    
        public function __construct()
        {
            $this->aura = new RouterContainer;
        }
    
        public function match(ServerRequestInterface $request): Result
        {
            $matcher = $this->aura->getMatcher();
            if ($route = $matcher->match($request)) {
                return new Result($route->name, $route->handler, $route->attributes);
            }
    
            throw new RequestNotMatchedException($request);
        }
    
        public function generate($name, array $params): string
        {
            $generator = $this->aura->getGenerator();
            try {
                return $generator->generate($name, $params);
            } catch (RouteNotFound $e) {
                throw new UnableToFoundRouteException($name, $params);
            }
        }
    
        /**
         * @param Route $data
         * @throws ImmutableProperty
         * @throws RouteAlreadyExists
         */
        public function addRoute(Route $data): void
        {
            $route = new \Aura\Router\Route();
            $route->name($data->name);
            $route->path($data->path);
            $route->handler($data->action);
    
            foreach ($data->options as $name => $value) {
                switch ($name) {
                    case 'tokens':
                        $route->tokens($value);
                        break;
                    case 'defaults':
                        $route->defaults($value);
                        break;
                    case 'wildcard':
                        $route->wildcard($value);
                        break;
                    default:
                        throw new InvalidArgumentException('Undefined option "' . $name . '"');
                }
            }
    
            if ($methods = $data->methods) {
                $route->allows($methods);
            }
    
            $this->aura->getMap()->addRoute($route);
        }
    }
    
3 Инжектим AuraRouterAdapter

Было:

    $container->set(Router::class, function (Container $container) {
        return $container->get(FuriousRouterAdapter::class);
    });

Стало:

    $container->set(Router::class, function (Container $container) {
        return $container->get(AuraRouterAdapter::class);
    });
    
Готово!