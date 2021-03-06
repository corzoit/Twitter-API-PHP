## Simple PHP Wrapper for Twitter API v1.1

Twitter-API-PHP is a simple PHP wrapper library for Twitter API v1.1 calls. It has been designed for making the Twitter API as simple to use as possible for developers (on the server or on the client).

It supports:

- [Application-Only Authentication](https://dev.twitter.com/oauth/application-only) (Oauth 2.0).
- [Single-User OAuth](https://dev.twitter.com/oauth/overview/single-user) (Oauth 1.0a).
- [3-legged Authorization](https://dev.twitter.com/oauth/3-legged) (Oauth 1.0a).


### Installation

To install via [Composer](https://getcomposer.org), add the following to your composer.json file and run `composer install`:

```
    {
        "require": {
            "abhishekbhardwaj/twitter-api-php": "~1.1"
        }
    }
```

### Dependencies

```
"php": ">=5.4.0",
"guzzlehttp/guzzle": "~5.0",
"guzzlehttp/oauth-subscriber": "~0.2"
```

### Basic Usage

Some examples are located in the `examples/` directory:

- appExample.php: Example of Application-Only Authentication
- userExample.php: Example of 3-legged Authorization

Detailed explanation can be found below:

#### To use Twitter as an application:

- Create an instance of `Twitter\Config\AppCredentials` class with appropriate API keys.

```php
$credentials = new AppCredentials($consumerKey, $consumerSecret);
```

- Create an instance of `Twitter\Client` with the `AppCredentials` object you just created.

```php
$client = new Client($credentials);
```

- Create an Application Connection to Twitter.

```php
$app = $client->connect();
```

- When using Twitter as an application, you need to use a Bearer Token which can be generated by calling the `createBearerToken()` function.

```php
$app->createBearerToken();

//to access bearer token (for storing in db for later use), you can use:
$app->getCredentials()->getBearerToken();
```

- Now you can send GET requests (since you can only GET as an application from Twitter) to Twitter. [Details available here!]{https://dev.twitter.com/oauth/application-only}

```php
//getting @abhishekwebin's recent timeline.
$response = $app->get('users/show.json', array('screen_name' => 'abhishekwebin'));
```

- Since we use Guzzle internally to talk to Twitter, the `$response` object is an instance of `GuzzleHttp\Message\ResponseInterface` and thus, it has many helpful functions for example:

```php
$response->getHeaders(); //gets the response headers
$response->getStatusCode(); //gets the status code
$response->json(); //parse response as JSON and return decoded data as assoc-array
$response->getBody(); //gets the response body
```

For more on Guzzle, refer to its official documentation [here](http://guzzle.readthedocs.org/en/latest/quickstart.html).


#### To use Twitter as a user:

*this section shows you how to login as a user*

- create an instance of `Twitter\Config\UserCredentials` class with appropriate API keys.

```php
$credentials = new UserCredentials($consumerKey, $consumerSecret, $callbackUrl);
```

- Create an instance of `Twitter\Client` with the `UserCredentials` object you just created.

```php
$client = new Client($credentials);
```

- Create a User Connection to Twitter.

```php
$app = $client->connect();
```

- Get Authorization URL.

```php
$redirectUrl = $app->getRedirectUrlForAuth();
```

- Redirect the user to that URL.

- Once the user logs in, Twitter will redirect the user back to the callbackUrl you specified in your app settings and in the credentials above.

- The callback url will get a temporary oauthToken and oauthVerifier as query parameters. Use them to generate an access token (token and secret):

```php
$accessTokens = $app->getAccessToken($oauthToken, $oauthVerifier);
```

- `$accessTokens` is an assoc-array which contains `oauth_token` and `oauth_token_secret` which basically never expire. You can store these in the db.

- Now you send either GET or POST requests to Twitter on behalf of the authenticated user to the endpoints.


#### `GET`ting from an endpoint:

```php
//getting @abhishekwebin's recent timeline.
$response = $app->get('users/show.json', array('screen_name' => 'abhishekwebin'));
```


#### `POST`ing to an endpoint:

```php
//post a new tweet: 'Test status!' as the currently authenticated users.
$response = $app->post('statuses/update.json', array('status' => 'Test status!'));
```

#### Uploading pictures with a Tweet:

A maximum of 4 pictures can be attached to a tweet. To do so:

```php
//list of pictures to upload. Input array items shouldn't be more than 4.
$media = $app->uploadMedia(array(
    '{{FULL PATH TO PICTURE}}',
    '{{FULL PATH TO PICTURE}}',
    '{{FULL PATH TO PICTURE}}',
    '{{FULL PATH TO PICTURE}}'
));

//post a new status
$response = $app->post('statuses/update.json', array(
    'status' => 'Test status!',
    'media_ids' => $media
));
```

The list of pictures that is passed on to `uploadMedia()` can be URL's or absolute file paths.

### Todo

- Change namespace to something other than `Twitter\*`
- More documentation.

### Changelog

- November 12, 2014: `Version 1.1.2` - Parameters are now optional on GET requests.
- November 9, 2014: `Version 1.1.*` - more stable and also lets you upload images to Twitter.
- November 8, 2014: `Version 1.0.*` - very first version of the library, didn't have support for media uploads.

### Important Links

1. All Twitter API Endpoints can be found [here](https://dev.twitter.com/rest/public).
2. All Twitter Authentication related stuff can be found [here](https://dev.twitter.com/oauth).

### Contribution

If you find any bugs, either post an issue or pull request are always welcome! :)

### License

This library is licensed under the MIT License. See the `LICENSE` file for more details!
