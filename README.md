# State pattern for any object in Laravel

[![Latest Version on Packagist](https://img.shields.io/packagist/v/dive-be/laravel-stateful.svg?style=flat-square)](https://packagist.org/packages/dive-be/laravel-stateful)

This package adds state support to non-Eloquent objects in Laravel.

⚠️ Minor releases of this package may cause breaking changes as it has no stable release yet.

## What problem does this package solve?

Usually, when defining "states" or "statuses" on objects, enumeration-like values/objects are used to represent the 
current state of the object. While this approach is pragmatic and good enough for simple use cases, it tends to become a
mess rapidly when complex domain logic has to be incorporated. This package solves this problem by using the state pattern
and the concept of state machines.

## Installation

You can install the package via composer:

```bash
composer require dive-be/laravel-stateful
```

## Usage

The best example is a practical example.

### Context

Let's say there is a `CheckoutWizard` class that has many possible states: `AddressSelect`, `ShippingSelect`, `Summary`, `Complete`.
Each of these states should map to the current index of the Wizard in the front-end and each step also has a distinct
color to give visual feedback to the user.

### Abstract state

That's why first, we should create an abstract `CheckoutState` class and define all possible transitions. For example,
it makes absolutely zero sense to go back to an `AddressSelect` state if the `CheckoutWizard` has reached the `Complete` state.
However, it is perfectly fine for the user to go back to a previous step as long as the `Complete` state is not reached.

```php
abstract class CheckoutState extends State
{
    abstract public function color(): string;

    abstract public function step(): int;
    
    public static function config(): Config
    {
        return parent::config()
            ->allowTransition(AddressSelect::class, ShippingSelect::class)
            ->allowTransition(ShippingSelect::class, AddressSelect::class) // go back
            ->allowTransition(ShippingSelect::class, Summary::class)
            ->allowTransition(Summary::class, ShippingSelect::class) // go back
            ->allowTransition(Summary::class, Complete::class);
    }
}
```

Here is what the `AddressSelect` state could look like:

```php
class AddressSelect extends CheckoutState
{
    public function color(): string
    {
        return 'maroon';
    }
    
    public function step(): int
    {
        return 0;
    }
}
```

## Testing

```bash
composer test
```

## Changelog

Please see [CHANGELOG](CHANGELOG.md) for more information on what has changed recently.

## Contributing

Please see [CONTRIBUTING](CONTRIBUTING.md) for details.

## Security

If you discover any security related issues, please email oss@dive.be instead of using the issue tracker.

## Credits

- [Muhammed Sari](https://github.com/mabdullahsari)
- [All Contributors](../../contributors)
- Spatie for their superb [Model States](https://github.com/spatie/laravel-model-states) package that has heavily inspired this package

## License

The MIT License (MIT). Please see [License File](LICENSE.md) for more information.
