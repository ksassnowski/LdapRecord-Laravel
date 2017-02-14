# Adldap2 - Laravel

![Built for Laravel](https://img.shields.io/badge/Built_for-Laravel-green.svg?style=flat-square)
[![Build Status](https://img.shields.io/travis/Adldap2/Adldap2-Laravel.svg?style=flat-square)](https://travis-ci.org/Adldap2/Adldap2-Laravel)
[![Scrutinizer Code Quality](https://img.shields.io/scrutinizer/g/Adldap2/Adldap2-laravel/master.svg?style=flat-square)](https://scrutinizer-ci.com/g/Adldap2/Adldap2-laravel/?branch=master)
[![Total Downloads](https://img.shields.io/packagist/dt/adldap2/adldap2-laravel.svg?style=flat-square)](https://packagist.org/packages/adldap2/adldap2-laravel)
[![Latest Stable Version](https://img.shields.io/packagist/v/adldap2/adldap2-laravel.svg?style=flat-square)](https://packagist.org/packages/adldap2/adldap2-laravel)
[![License](https://img.shields.io/packagist/l/adldap2/adldap2-laravel.svg?style=flat-square)](https://packagist.org/packages/adldap2/adldap2-laravel)

## Description

Adldap2 - Laravel allows easy configuration, access, management and authentication to LDAP connections utilizing the root
[Adldap2 Repository](http://www.github.com/Adldap2/Adldap2).

## Requirements

To use Adldap2-Laravel, your server must support:

- PHP 5.6 or greater
- PHP LDAP Extension
- An Active Directory Server

**Note:** OpenLDAP support is experimental, success may vary.

## Index

* [Installation](#installation)
* [Usage](#usage)
* [Auth Driver](#auth-driver)
  * [Upgrading](docs/auth/upgrading.md)
  * [Quick Start - From Scratch](docs/quick-start.md)
  * [Installation & Basic Setup](docs/auth/installation.md)
  * Features
    * [Providers](docs/auth/providers.md)
    * [Scopes](docs/auth/scopes.md)
    * [Rules](docs/auth/rules.md)
    * [Synchronizing Attributes](docs/auth/syncing.md)
    * [Binding to the User Model](docs/auth/binding.md)
    * [Login Fallback](docs/auth/fallback.md)
    * [Multiple Connections](docs/auth/multiple-connections.md)
    * [Password Synchronization](docs/auth/syncing/#password-synchronization)
    * [Importing Users](docs/importing.md)
    * [Developing without an AD connection](docs/auth/fallback.md#developing-locally-without-an-ad-connection)

## Installation

Insert Adldap2-Laravel into your `composer.json` file:

```json
"adldap2/adldap2-laravel": "3.0.*",
```

Or via command line:

```bash
composer require adldap2/adldap2-laravel
```

Then run `composer update`.

Once finished, insert the service provider in your `config/app.php` file:
```php
Adldap\Laravel\AdldapServiceProvider::class,
```

Then insert the facade:
```php
'Adldap' => Adldap\Laravel\Facades\Adldap::class
```

Publish the configuration file by running:
```bash
php artisan vendor:publish --tag="adldap"
```

Now you're all set!

## Usage

You can perform all methods on Adldap through its facade like so:
```php
// Finding a user.
$user = Adldap::search()->users()->find('john doe');

// Searching for a user.
$search = Adldap::search()->where('cn', '=', 'John Doe')->get();

// Authenticating against your LDAP server.
if (Adldap::auth()->attempt($username, $password)) {
    // Passed!
}

// Running an operation under a different connection:
$users = Adldap::getProvider('other-connection')->search()->users()->get();

// Creating a user.
$user = Adldap::make()->user([
    'cn' => 'John Doe',
]);

$user->save();
```

Or you can inject the Adldap contract:

```php
use Adldap\Contracts\AdldapInterface;

class UserController extends Controller
{
    /**
     * @var Adldap
     */
    protected $adldap;
    
    /**
     * Constructor.
     *
     * @param AdldapInterface $adldap
     */
    public function __construct(AdldapInterface $adldap)
    {
        $this->adldap = $adldap;
    }
    
    /**
     * Displays the all LDAP users.
     *
     * @return \Illuminate\View\View
     */
    public function index()
    {
        $users = $this->adldap->search()->users()->get();
        
        return view('users.index', compact('users'));
    }
}
```

To see more usage in detail, please visit the [Adldap2 Repository](http://github.com/Adldap2/Adldap2).
