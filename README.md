# OneLogin PHP SDK

This SDK will let you execute all the API methods, version/1, described
at https://developers.onelogin.com/api-docs/1/getting-started/dev-overview.

## Installation
### Hosting
#### Github
The toolkit is hosted on github. You can download it from:
* Lastest release: https://github.com/onelogin/onelogin-php-sdk/releases/latest
* Master repo: https://github.com/onelogin/onelogin-php-sdk/tree/master

#### Composer
The toolkit supports [composer](https://getcomposer.org/). You can find the `onelogin/api` package at https://packagist.org/packages/onelogin/api

In order to import the sdk into your current php project, execute
```
composer require onelogin/api
```

### Dependencies
The SDK has the following dependencies:

* PHP >5.6
* guzzle


## Getting started

You'll need a OneLogin account and a set of API credentials before you get started. 

If you don't have an account you can [sign up for a free developer account here](https://www.onelogin.com/developer-signup).

|||
|---|---|
|client_id|Required: A valid OneLogin API client_id|
|client_secret|Required: A valid OneLogin API client_secret|
|region| Optional: `us` or `eu`. Defaults to `us`   |

```php
<?php

use \OneLogin\api\OneLoginClient;

$client = new OneLoginClient($clientId, $clientSecret, $region);

#Now you can make requests 
client.getUsers()
``` 

For all methods see PHPdoc of this SDK published at:
https://onelogin.github.io/onelogin-php-sdk/index.html

## Usage

### Errors and exceptions

OneLogin's API can return 400, 401, 403 or 404 when there was any issue executing the action. When that happens, the methods of the SDK will include error and errorMessage in the OneLoginClient. Use the getError() and the getErrorDescription() to retrieve them.


### Authentication

By default methods call internally to `getAccessToken` if there is no valid accessToken. You can also get tokens etc directly if needed. 

```php
/* Get an AccessToken */
$token = $client->getAccessToken();

/* Refresh an AccessToken */
$token2 = $client->refreshToken();

/* Revoke an AccessToken */
$client->revokeToken();
```

### Available Methods

```php

/* Get rate limits */
$rateLimit = $client->getRateLimit();

/* Get Custom Attributes */
$customGlobalAttributes = $client->getCustomAttributes();

/* Get Users with no query parameters */
$users = $client->getUsers();

/* Get Users with query parameters */
$queryParameters = array (
    "email" => "user@example.com"
);
$usersFiltered = $client->getUsers($queryParameters);

$queryParameters = array (
    "email" => "usermfa@example.com"
);
$usersFiltered2 = $client->getUsers($queryParameters);

/* Get Users with limit */
$queryParameters = array (
    "limit" => "3"
);
$usersFilteredLimited = $client->getUsers($queryParameters);


/* Get User By ID */
$user = $client->getUser($usersFiltered[0]->id);
$userMFA = $client->getUser($usersFiltered2[0]->id);

/* Update User with specific id */
$user = $client->getUser($user->id);
$updateUserParams = $user->getUserParams();
$updateUserParams["firstname"] = "modified_firstname";
$user = $client->updateUser($user->id, $updateUserParams);

 /* Get Global Roles */
$roles = $client->getRoles();

/* Get Role */
$role = $client->getRole($roles[0]->getId());
$role2 = $client->getRole($roles[1]->getId());
       
/* Assign & Remove Roles On Users */
$newRoleIds = array(
       $role->getId(),
       $role2->getId()
);
$client->assignRoleToUser($user->id, $newRoleIds);
$user = $client->getUser($user->id);
array_pop($newRoleIds);
$client->removeRoleFromUser($user->id, $newRoleIds);
$user = $client->getUser($user->id);

/* Sets Password by ID Using Cleartext */
$password = "Aa765431-XxX";
$client->setPasswordUsingClearText($user->id, $password, $password);

/* Sets Password by ID Using Salt and SHA-256 */
$password = "Aa765432-YyY";
$salt = "11xxxx1";
$hashedSaltedPassword = hash('sha256', $salt . $password);
$client->setPasswordUsingHashSalt($userMFA->id, $hashedSaltedPassword, $hashedSaltedPassword, "salt+sha256", $salt);

/* Set Custom Attribute Value to User */
$customAttributes = array(
    $customGlobalAttributes[0] => "xxxx",
    $customGlobalAttributes[1] => "yyyy"
);
$client->setCustomAttributeToUser($user->id, $customAttributes);

/* Log Out User */
$client->logUserOut($user->id);

/* Lock User */
$client->lockUser($user->id, 1); // Lock the user 1 min

/* Get User apps */
$userApps = $client->getUserApps($user->id);

/* Get User Roles */
$userRolesIds = $client->getUserRoles($user->id);
        
/* Create user */
$newUserParams = array(
    "email" => "testcreate_1@example.com",
    "firstname" => "testcreate_1_fn",
    "lastname" => "testcreate_1_ln",
    "username" => "testcreate_1@example.com"
);
$createdUser = $client->createUser($newUserParams);

/* Delete user */
$removed = $client->deleteUser($createdUser->id);


/* Create Session Login Token */
$sessionLoginTokenParams = array(
    "username_or_email" => "user@example.com",
    "password" => "Aa765431-XxX",
    "subdomain"=> "example-onelogin-subdomain"
);
$sessionTokenData = $client->createSessionLoginToken($sessionLoginTokenParams);

/* Create Session Login Token MFA , after verify */
$sessionLoginTokenMFAParams = array(
    "username_or_email" => "usermfa@example.com",
    "password" => "Aa765432-YyY",
    "subdomain" => "example-onelogin-subdomain"
);
$sessionTokenMFAData = $client->createSessionLoginToken($sessionLoginTokenMFAParams);
$otpCode = "645645"; // We may take that value from OTP device
$sessionTokenData2 = $client->getSessionTokenVerified($sessionTokenMFAData->devices[0]->getID(), $sessionTokenMFAData->stateToken, $otpCode);

/* Get EventTypes */
$eventTypes = $client->getEventTypes();

/* Get Events */
$events = $client->getEvents();

/* Get Event */
$event = $client->getEvent($events[0]->id);
           
/* Create Event */
$eventParams = array(
    "event_type_id" => 000,
    "account_id" => 00000,
    "actor_system" => 00,
    "user_id" => 00000000,
    "user_name" => "test_event",
    "custom_message" => "test creating event :)"
);
$client->createEvent($eventParams);


/* Get Filtered Events */
$eventQueryParameters = array(
  "user_id" => 00000000
);
$events = $client->getEvents($eventQueryParameters);

/* Get Groups */
$groups = $client->getGroups();

/* Get Group */
$group = $client->getGroup($groups[0]->getId());

/* Get SAMLResponse directly */
$appId = "000000";
$samlEndpointResponse = $client->getSAMLAssertion("user@example.com", "Aa765431-XxX", $appId, "example-onelogin-subdomain");

/* Get SAMLResponse after MFA */
$samlEndpointResponse2 = $client->getSAMLAssertion("usermfa@example.com", "Aa765432-YyY", $appId, "example-onelogin-subdomain");
$mfa = $samlEndpointResponse2->getMFA();
$otpToken = "000000";
$samlEndpointResponseAfterVerify = $client->getSAMLAssertionVerifying($appId, $mfa->getDevices()[0]->getID(), $mfa->getStateToken(), $otpToken, null);

/* Generate Invite Link */
$urlLink = $client->generateInviteLink("user@example.com");

/* Send Invite Link */
$sent = $client->sendInviteLink("user@example.com");

/* Get Apps to Embed for a User */
$embedToken = "30e256c101cd0d2e731de1ec222e93c4be8a1578";
$apps = $client->getEmbedApps($embedToken, "user@example.com");
```

## Development

After checking out the repo, run `composer install` to install dependencies. Then, `cd tests` and `phpunit .` to run the tests.

To release a new version, update the version number of the `composer.json` and commit it, then you will be able to update it to composer, creating a previously a new tag on github.

## Contributing

Bug reports and pull requests are welcome on GitHub at https://github.com/onelogin/onelogin-php-sdk. This project is intended to be a safe, welcoming space for collaboration, and contributors are expected to adhere to the [Contributor Covenant](http://contributor-covenant.org) code of conduct.

## License

The gem is available as open source under the terms of the [MIT License](http://opensource.org/licenses/MIT).

## Code of Conduct

Everyone interacting in the OneLogin PHP SDK project’s codebases, issue trackers, chat rooms and mailing lists is expected to follow the [code of conduct](https://github.com/onelogin/onelogin-php-sdk/blob/master/CODE_OF_CONDUCT.md).