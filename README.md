# MainWP API - PHP Client

A PHP wrapper for the MainWP REST API. Easily interact with the MainWP REST API securely using this library. If using a HTTPS connection this library uses BasicAuth, else it uses Oauth to provide a secure connection to MainWP.

[![CI status](https://github.com/thanghv/dev-mainwp-api-php/actions/workflows/ci.yml/badge.svg?branch=trunk)](https://github.com/thanghv/dev-mainwp-api-php/actions/workflows/ci.yml)
[![Scrutinizer Code Quality](https://scrutinizer-ci.com/g/thanghv/dev-mainwp-api-php/badges/quality-score.png?b=master)](https://scrutinizer-ci.com/g/thanghv/dev-mainwp-api-php/?branch=master)
[![PHP version](https://badge.fury.io/ph/mainwp%2Fmainwp.svg)](https://packagist.org/packages/thanghv/dev-mainwp-api-php)

## Installation

Create composer.json as bellow if want to get package from github

```
{
  "repositories": [
      {
          "type": "vcs",
          "url": "https://github.com/thanghv/dev-mainwp-api-php.git"
      }
  ],
  "require": {
      "thanghv/dev-mainwp-api-php": "dev-main"
  }
}

```

```
composer require thanghv/dev-mainwp-api-php
```

## Getting started

Generate API credentials (Consumer Key & Consumer Secret) following this instructions <http://docs.mainwp.com/document/mainwp-rest-api/>
.

Check out the MainWP API endpoints and data that can be manipulated in <https://mainwp.github.io/mainwp-rest-api-docs/>.

## Setup

Setup for the new WP REST API integration (MainWP 5.2 or later):

```php
require __DIR__ . '/vendor/autoload.php';

use MainWP\Dashboard\Client;

$mainwp = new Client(
  'http://example.com',
  'XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX',
  [
    'version' => 'v2',
  ],
);

or

$mainwp = new Client(
  'http://example.com',
  false,
  [
    'version' => 'v2',
    'version' => 'ck_XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX',
    'version' => 'cs_XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX',
  ]
);

```

## Client class

```php
$mainwp = new Client($url, $options, $consumer_apikey );

or

$options['consumer_key'] = $consumer_key;
$options['consumer_secret'] = $consumer_secret;

$mainwp = new Client($url, $options );

```

### Options

| Option             | Type     | Required | Description                                        |
| ------------------ | -------- | -------- | -------------------------------------------------- |
| `url`              | `string` | yes      | Your Store URL, example: http://mydashboard.dev/   |
| `consumer_apikey`  | `string` | no	   | Your API consumer Bearer token key                 |
| `options`          | `array`  | no       | Extra arguments (see client options table)         |

#### Client options

| Option                   | Type     | Required | Description                                                                                                                                            |
| ------------------------ | -------- | -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `version`                | `string` | no       | API version, default is `v2`                                                                                                                           |
| `timeout`                | `int`    | no       | Request timeout, default is `15`                                                                                                                       |
| `verify_ssl`             | `bool`   | no       | Verify SSL when connect, use this option as `false` when need to test with self-signed certificates, default is `true`                                 |
| `auth_method`            | `string  | no       | Use Bearer Token `bearer`, oAuth `oauth`, or Basic 'basic' auth for requests, default is `bearer`                                                      |                    |
| `consumer_key`    	   | `string` | no    	 | Your API consumer key                      																											  |
| `consumer_secret` 	   | `string` | no   	 | Your API consumer secret                   																											  |
| `follow_redirects`       | `bool`   | no       | Allow the API call to follow redirects                                                                                                                 |
| `query_string_auth`      | `bool`   | no       | Force Basic Authentication as query string when `true` and using under HTTPS, default is `false`                                                       |
| `oauth_timestamp`        | `string` | no       | Custom oAuth timestamp, default is `time()`                                                                                                            |
| `user_agent`             | `string` | no       | Custom user-agent, default is `MainWP API Client-PHP`                                                                                                  |
| `method_override_header` | `bool`   | no       | If true will mask all non-GET/POST methods (PUT/DELETE/etc.) by using POST method with added `X-HTTP-Method-Override: METHOD` HTTP header into request |
| `extension_api`          | `string` | no       | Use for MainWP extensions REST API prefix,                                                                                                             |

## Client methods

### GET

```php
$mainwp->get($endpoint, $parameters = []);
```

### POST

```php
$mainwp->post($endpoint, $data);
```

### PUT

```php
$mainwp->put($endpoint, $data);
```

### DELETE

```php
$mainwp->delete($endpoint, $parameters = []);
```

### OPTIONS

```php
$mainwp->options($endpoint);
```

#### Arguments

| Params       | Type     | Description                                                  |
| ------------ | -------- | ------------------------------------------------------------ |
| `endpoint`   | `string` | MainWP API endpoint, example: `sites` or `sites/sync/12` |
| `data`       | `array`  | Only for POST and PUT, data that will be converted to JSON   |
| `parameters` | `array`  | Only for GET and DELETE, request query string                |

#### Response

All methods will return arrays on success or throwing `HttpClientException` errors on failure.

```php
use MainWP\Dashboard\HttpClient\HttpClientException;

try {
  // Array of response results.
  $results = $mainwp->get('sites');
  echo '<pre><code>' . print_r($results, true) . '</code><pre>'; // JSON output.

  // Last request data.
  $lastRequest = $mainwp->http->getRequest();
  echo '<pre><code>' . print_r($lastRequest->getUrl(), true) . '</code><pre>'; // Requested URL (string).
  echo '<pre><code>' .
    print_r($lastRequest->getMethod(), true) .
    '</code><pre>'; // Request method (string).
  echo '<pre><code>' .
    print_r($lastRequest->getParameters(), true) .
    '</code><pre>'; // Request parameters (array).
  echo '<pre><code>' .
    print_r($lastRequest->getHeaders(), true) .
    '</code><pre>'; // Request headers (array).
  echo '<pre><code>' . print_r($lastRequest->getBody(), true) . '</code><pre>'; // Request body (JSON).

  // Last response data.
  $lastResponse = $mainwp->http->getResponse();
  echo '<pre><code>' . print_r($lastResponse->getCode(), true) . '</code><pre>'; // Response code (int).
  echo '<pre><code>' .
    print_r($lastResponse->getHeaders(), true) .
    '</code><pre>'; // Response headers (array).
  echo '<pre><code>' . print_r($lastResponse->getBody(), true) . '</code><pre>'; // Response body (JSON).
} catch (HttpClientException $e) {
  echo '<pre><code>' . print_r($e->getMessage(), true) . '</code><pre>'; // Error message.
  echo '<pre><code>' . print_r($e->getRequest(), true) . '</code><pre>'; // Last request data.
  echo '<pre><code>' . print_r($e->getResponse(), true) . '</code><pre>'; // Last response data.
}
```

## Release History

- 2025-2-6 - 1.0.0 - Stable release.
