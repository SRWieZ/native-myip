# native-myip

[//]: # ([![Latest Stable Version]&#40;http://poser.pugx.org/knotsphp/publicip/v&#41;]&#40;https://packagist.org/packages/knotsphp/publicip&#41; [![Total Downloads]&#40;http://poser.pugx.org/knotsphp/publicip/downloads&#41;]&#40;https://packagist.org/packages/knotsphp/publicip&#41; [![Latest Unstable Version]&#40;http://poser.pugx.org/knotsphp/publicip/v/unstable&#41;]&#40;https://packagist.org/packages/knotsphp/publicip&#41; [![License]&#40;http://poser.pugx.org/knotsphp/publicip/license&#41;]&#40;https://packagist.org/packages/knotsphp/publicip&#41; [![PHP Version Require]&#40;http://poser.pugx.org/knotsphp/publicip/require/php&#41;]&#40;https://packagist.org/packages/knotsphp/publicip&#41;)

[//]: # (![GitHub Workflow Status &#40;with event&#41;]&#40;https://img.shields.io/github/actions/workflow/status/knotsphp/publicip/test.yml?label=Tests&#41;)
A simple PHP library to get the public IP address of the current machine.

This library uses `dig` or HTTP requests to obtain the public IP address of the current machine by utilizing publicly
available whoami services.

It comes with an opinionated default configuration to use the **fastest** and most **reliable** fetchers and providers.
However, it also includes a flexible API that allows you to use different fetchers and different providers.

## 🚀 Installation

```bash
composer require knotsphp/publicip
```

## 📚 Usage

Easiest way to get the public IP address of the current machine is to use the `PublicIP::get()` method.

```php
use KnotsPHP\PublicIP\Finders\{ PublicIP, PublicIPv4, PublicIPv6 };

$ipv4 = PublicIPv4::get(); // returns your IPv4
$ipv6 = PublicIPv6::get(); // returns your IPv6
$ipv4or6 = PublicIP::get(); // returns either IPv4 or IPv6
```

[//]: # (Talk about the default configuration)

If you want to use a specific fetcher, or a specific provider, you can use the `PublicIPv4::finder()->fetch()` method.

```php
use KnotsPHP\PublicIP\Enums\DnsProvider;
use KnotsPHP\PublicIP\Fetchers\DigFetcher;
use KnotsPHP\PublicIP\Finders\PublicIPv4;

$ipv4 = PublicIPv4::finder()
    ->addFetcher((new DigFetcher())
        ->from(DnsProvider::OpenDNS)))
    ->fetch();
```

### Advanced Usage

You can also use a `Fetcher` directly to get the IP address.

```php
use KnotsPHP\PublicIP\Enums\DnsProvider;
use KnotsPHP\PublicIP\Enums\IpVersion;
use KnotsPHP\PublicIP\Fetchers\DigFetcher;

$ipv4 = (new DigFetcher)->from(DnsProvider::Cloudflare)->fetch(IpVersion::v4);
```

Note that this returns null instead of throwing an exception if the fetcher fails.

## 🏃 Performance

If you are sure that your machine has `dig` installed, you can speed up the process by setting the `isSupported`
property to `true`.

It works both ways; if you are sure that your machine does not have `dig` installed, you can set it to `false` to
prevent unnecessary checks.

```php
use KnotsPHP\PublicIP\Fetcher\DigFetcher;

DigFetcher::$isSupported = true;
```

If you use the `CurlFetcher` or `FileGetContentsFetcher`, you can set the `forceHTTP` property to `true` to use HTTP instead of HTTPS.
Some whoami services do not support HTTPS anyway as they are meant to be used in scripts like this `curl ifconfig.co`.

```php
use KnotsPHP\PublicIP\Fetcher\CurlFetcher;
use KnotsPHP\PublicIP\Fetchers\FileGetContentsFetcher;

CurlFetcher::$forceHTTP = true;
FileGetContentsFetcher::$forceHTTP = true;
```

## 📖 Documentation

`dig` provider list

|            | IP4or6 | IPv4 | IPv6 | Reliable?                                                   |
|------------|--------|------|------|-------------------------------------------------------------|
| Cloudflare | ✅      | ✅    | ✅    | ✅                                                           |
| Google     | ✅      |      |      | ✅                                                           |
| OpenDNS    | ✅      | ✅    | ✅    | ⚠️ says they have blocked France & Portugal but works great |
| Akamai     | ⚠️     | ⚠️   | ⚠️   | ⚠️ last digits of the IP can be wrong                       |

HTTP whoami provider list

|                | IPv4 | IPv6 | IP4or6 | Note          |
|----------------|------|------|--------|---------------|
| ifconfig.co    | ✅    | ✅    | ✅      | Fast          |
| ipify.org      | ✅    | ✅    | ✅      | Open-source   |
| ifconfig.me    | ✅    | ✅    | ✅      |               |
| icanhazip.com  | ✅    | ✅    | ✅      |               |
| cloudflare.com | ✅    | ✅    | ✅      | Use more data |
| ipinfo.io      | ✅    |      |        | ⚠️ only IPv4  |
| amazonaws.com  | ✅    |      |        | ⚠️ only IPv4  |

## 📋 TODO

- PSR-18 HTTP fetcher with a way to choose psr compatible client so other tools can monitor outgoing requests
- Write tests
- Publish v1
- Look for a way to get local networks and IP addresses

## 📦 Alternatives

If you are not pleased with this library, check out the following alternatives:

- https://github.com/martinlindhe/php-myip

You can also contribute to this library by opening an issue or a pull request.

## 👥 Credits

native-myip was created by Eser DENIZ.

## 📝 License

native-myip is licensed under the MIT License. See LICENSE for more information.
