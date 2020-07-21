## Замена PSR-7

Изначально фреймворк Furious использует [родной компонент PSR-7](https://github.com/Furious-PHP/psr7)

Попробуем заменить родной компонент на [реализацию PSR-7 от Laminas (Zend)](https://github.com/laminas/laminas-diactoros)

1 Изменяем зависимости в composer.json

Было

    ...
    "psr/container": "^1.0",
    "furious/psr7": "^1.3",
    "furious/router": "1.0.*",
    ...
    
Стало
    
    ...
    "psr/container": "^1.0",
    "laminas/laminas-diactoros": "^2.3"
    "furious/router": "1.0.*",
    ...

2 Меняем ServerRequestFactory в public/index.php

Было
    
    use Furious\Psr7\Factory\ServerRequestFactory;
    
Стало

    use Laminas\Diactoros\ServerRequestFactory;
      
3 Создаем ResponseFactoryAdapter в src/Framework/Http/Psr7/

Пример

    <?php
    
    declare(strict_types=1);
    
    namespace Framework\Http\Psr7;
    
    use Laminas\Diactoros\Response\EmptyResponse;
    use Laminas\Diactoros\Response\HtmlResponse;
    use Laminas\Diactoros\Response\JsonResponse;
    use Laminas\Diactoros\Response\TextResponse;
    use Laminas\Diactoros\Response\XmlResponse;
    use Psr\Http\Message\ResponseInterface;
    
    final class LaminasResponseFactory implements ResponseFactory
    {
        public function html(string $html, int $code = 200): ResponseInterface
        {
            return new HtmlResponse($html, $code);
        }
    
        public function json(array $data, int $code = 200): ResponseInterface
        {
            return new JsonResponse($data, $code);
        }
    
        public function xml($data, int $code = 200): ResponseInterface
        {
            return new XmlResponse($data, $code);
        }
    
        public function text(string $text, int $code = 200): ResponseInterface
        {
            return new TextResponse($text, $code);
        }
    
        public function empty(): ResponseInterface
        {
            return new EmptyResponse();
        }
    }

4 Инжектим другой ResponseFactory в config/dependencies.php

Было

    $container->set(ResponseFactory::class, function (Container $container) {
        return $container->get(FuriousResponseFactory::class);
    });
    
Стало

    $container->set(ResponseFactory::class, function (Container $container) {
        return $container->get(LaminasResponseFactory::class);
    });
    
    
Готово!

Как видите, поменять PSR-7 компонент в фреймворке Furious очень просто.

А теперь бонус:

Давайте посмотрим как поменялись Action'ы (и Middleware) после того как мы поменяли компонент:

Было:

    <?php
    
    declare(strict_types=1);
    
    namespace App\Http\Action;
    
    use Framework\Http\Psr7\ResponseFactory;
    use Psr\Http\Message\ResponseInterface;
    use Psr\Http\Message\ServerRequestInterface;
    use Psr\Http\Server\RequestHandlerInterface;
    
    final class HomeAction implements RequestHandlerInterface
    {
        private ResponseFactory $response;
    
        /**
         * HomeAction constructor.
         * @param ResponseFactory $response
         */
        public function __construct(ResponseFactory $response)
        {
            $this->response = $response;
        }
    
        public function handle(ServerRequestInterface $request): ResponseInterface
        {
            $name = $request->getQueryParams()['name'] ?? 'guest';
    
            $message = 'Hello, ' . $name . '!';
    
            return $this->response->json([
                'content' => $message
            ]);
        }
    }
    
Стало:

    <?php
    
    declare(strict_types=1);
    
    namespace App\Http\Action;
    
    use Framework\Http\Psr7\ResponseFactory;
    use Psr\Http\Message\ResponseInterface;
    use Psr\Http\Message\ServerRequestInterface;
    use Psr\Http\Server\RequestHandlerInterface;
    
    final class HomeAction implements RequestHandlerInterface
    {
        private ResponseFactory $response;
    
        /**
         * HomeAction constructor.
         * @param ResponseFactory $response
         */
        public function __construct(ResponseFactory $response)
        {
            $this->response = $response;
        }
    
        public function handle(ServerRequestInterface $request): ResponseInterface
        {
            $name = $request->getQueryParams()['name'] ?? 'guest';
    
            $message = 'Hello, ' . $name . '!';
    
            return $this->response->json([
                'content' => $message
            ]);
        }
    }

Как видите, они не поменялись!

Таким образом вы можете менять компоненты как перчатки, не боясь что 
какой то компонент забросят, так как вы всегда его можете поменять за 5 минут!