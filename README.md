# Magento Provider for OAuth 2.0 Client

This package provides Magento OAuth 2.0 support for the PHP League's [OAuth 2.0 Client](https://github.com/thephpleague/oauth2-client).

## Installation

To install, use composer:

```
composer require commerceqb/oauth2-magento
```

## Usage

Usage is the same as The League's OAuth client, using `\CommerceQB\OAuth2\Client\Provider\Magent` as the provider.

### Authorization Code Flow

```php
$provider = new CommerceQB\OAuth2\Client\Provider\Magento([
    'clientId'          => '{box-client-id}',
    'clientSecret'      => '{box-client-secret}',
    'redirectUri'       => 'https://example.com/callback-url'
]);

if (!isset($_GET['code'])) {

    // If we don't have an authorization code then get one
    $authUrl = $provider->getAuthorizationUrl();
    $_SESSION['oauth2state'] = $provider->getState();
    header('Location: '.$authUrl);
    exit;

// Check given state against previously stored one to mitigate CSRF attack
} elseif (empty($_GET['state']) || ($_GET['state'] !== $_SESSION['oauth2state'])) {

    unset($_SESSION['oauth2state']);
    exit('Invalid state');

} else {

    // Try to get an access token (using the authorization code grant)
    $token = $provider->getAccessToken('authorization_code', [
        'code' => $_GET['code']
    ]);

    // Optional: Now you have a token you can look up a users profile data
    try {

        // We got an access token, let's now get the user's details
        $user = $provider->getResourceOwner($token);

        // Use these details to create a new profile
        printf('Hello %s!', $user->getId());

    } catch (Exception $e) {

        // Failed to get user details
        exit('Oh dear...');
    }

    // Use this to interact with an API on the users behalf
    echo $token->getToken();
}
```

## Testing

``` bash
$ ./vendor/bin/phpunit
```

## Contributing

Please see [CONTRIBUTING](https://github.com/CommerceQB/oauth2-magento/blob/master/CONTRIBUTING.md) for details.


## Credits

- [Joe Constant](https://github.com/lazyguru)
- [All Contributors](https://github.com/CommerceQB/oauth2-magento/contributors)


## License

The Apache License (Apache 2.0). Please see [License File](https://github.com/CommerceQB/oauth2-magento/blob/master/LICENSE) for more information.
