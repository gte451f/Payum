<h2 align="center">Supporting Payum</h2>

Payum is an MIT-licensed open source project with its ongoing development made possible entirely by the support of community and our customers. If you'd like to join them, please consider:

- [Become a sponsor](https://www.patreon.com/makasim)
- [Become our client](http://forma-pro.com/)

---

# Refund script.

```php
<?php
//refund.php

use Payum\Core\Request\Refund;
use Payum\Core\Payum;
use Payum\Core\Reply\HttpResponse;
use Payum\Core\Reply\ReplyInterface;

include __DIR__.'/config.php';

/** @var Payum $payum */

$token = $payum->getHttpRequestVerifier()->verify($_REQUEST);
$gateway = $payum->getGateway($token->getGatewayName());

try {
    $gateway->execute(new Refund($token));

    if (false == isset($_REQUEST['noinvalidate'])) {
        $payum->getHttpRequestVerifier()->invalidate($token);
    }

    if ($token->getAfterUrl()) {
        header("Location: ".$token->getAfterUrl());
    } else {
        http_response_code(204);
        echo 'OK';
    }
} catch (HttpResponse $reply) {
    foreach ($reply->getHeaders() as $name => $value) {
        header("$name: $value");
    }

    http_response_code($reply->getStatusCode());
    echo ($reply->getContent());

    exit;
} catch (ReplyInterface $reply) {
    throw new \LogicException('Unsupported reply', null, $reply);
}
```

Back to [examples](index.md).
Back to [index](../index.md).

