# phpnomad/privacy

[![Latest Version](https://img.shields.io/packagist/v/phpnomad/privacy.svg)](https://packagist.org/packages/phpnomad/privacy)
[![Total Downloads](https://img.shields.io/packagist/dt/phpnomad/privacy.svg)](https://packagist.org/packages/phpnomad/privacy)
[![PHP Version](https://img.shields.io/packagist/php-v/phpnomad/privacy.svg)](https://packagist.org/packages/phpnomad/privacy)
[![License](https://img.shields.io/packagist/l/phpnomad/privacy.svg)](https://packagist.org/packages/phpnomad/privacy)

`phpnomad/privacy` defines a small contract for answering a single question: can the current request be tracked? Application code that emits analytics, user-identifying events, or logs checks the bound strategy first, so consent policy lives in one place instead of being scattered across every call site.

## Installation

```bash
composer require phpnomad/privacy
```

## Overview

- Ships a single interface, `TrackingPermissionStrategy`, with one method: `canTrack(): bool`.
- Lets you centralize tracking consent in one replaceable strategy instead of repeating checks across analytics, event, and logging code.
- Stays platform-agnostic. The strategy you bind decides whether consent comes from a DNT header, a cookie banner, login state, or application-specific rules.
- Integration packages such as `phpnomad/wordpress-integration` ship default strategies you can replace by binding your own in an initializer.
- Has no runtime dependencies. It's a contract-only package that sits between your tracking code and whatever policy you choose to enforce.

## Usage

Implement the interface in your application:

```php
<?php

namespace MyApp\Strategies;

use PHPNomad\Privacy\Interfaces\TrackingPermissionStrategy;

class CookieConsentTrackingStrategy implements TrackingPermissionStrategy
{
    public function canTrack(): bool
    {
        return isset($_COOKIE['consent']) && $_COOKIE['consent'] === 'accepted';
    }
}
```

Bind it in your initializer alongside your other strategies:

```php
<?php

namespace MyApp;

use MyApp\Strategies\CookieConsentTrackingStrategy;
use PHPNomad\Loader\Interfaces\HasClassDefinitions;
use PHPNomad\Privacy\Interfaces\TrackingPermissionStrategy;

class AppInitializer implements HasClassDefinitions
{
    public function getClassDefinitions(): array
    {
        return [
            CookieConsentTrackingStrategy::class => TrackingPermissionStrategy::class,
        ];
    }
}
```

Then any code that emits tracking-sensitive output can ask the bound strategy before doing so.

## Documentation

Full PHPNomad documentation lives at [phpnomad.com](https://phpnomad.com).

## License

MIT. See [LICENSE.txt](LICENSE.txt).
