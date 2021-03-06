# Hashing

- [Introduction](#introduction)
- [Configuration](#configuration)
- [Basic Usage](#basic-usage)

<a name="introduction"></a>
## Introduction

The Laravel `Hash` [facade](/docs/{{version}}/facades) provides secure Bcrypt and Argon2 hashing for storing user passwords. If you are using the [Laravel Jetstream](https://jetstream.laravel.com) authentication scaffolding, Bcrypt will be used for registration and authentication by default.

> {tip} Bcrypt is a great choice for hashing passwords because its "work factor" is adjustable, which means that the time it takes to generate a hash can be increased as hardware power increases.

<a name="configuration"></a>
## Configuration

The default hashing driver for your application is configured in the `config/hashing.php` configuration file. There are currently three supported drivers: [Bcrypt](https://en.wikipedia.org/wiki/Bcrypt) and [Argon2](https://en.wikipedia.org/wiki/Argon2) (Argon2i and Argon2id variants).

> {note} The Argon2i driver requires PHP 7.2.0 or greater and the Argon2id driver requires PHP 7.3.0 or greater.

<a name="basic-usage"></a>
## Basic Usage

You may hash a password by calling the `make` method on the `Hash` facade:

    <?php

    namespace App\Http\Controllers;

    use App\Http\Controllers\Controller;
    use Illuminate\Http\Request;
    use Illuminate\Support\Facades\Hash;

    class UpdatePasswordController extends Controller
    {
        /**
         * Update the password for the user.
         *
         * @param  Request  $request
         * @return Response
         */
        public function update(Request $request)
        {
            // Validate the new password length...

            $request->user()->fill([
                'password' => Hash::make($request->newPassword)
            ])->save();
        }
    }

<a name="adjusting-the-bcrypt-work-factor"></a>
#### Adjusting The Bcrypt Work Factor

If you are using the Bcrypt algorithm, the `make` method allows you to manage the work factor of the algorithm using the `rounds` option; however, the default is acceptable for most applications:

    $hashed = Hash::make('password', [
        'rounds' => 12,
    ]);

> {note} As Laravel `Hash` facade utilizes PHP's `password_hash` function, in combination with the `bcrypt` driver only the first 72 characters of the plain text are hashed. For more information, check out the [official PHP documentation](https://www.php.net/manual/en/function.password-hash.php).

<a name="adjusting-the-argon2-work-factor"></a>
#### Adjusting The Argon2 Work Factor

If you are using the Argon2 algorithm, the `make` method allows you to manage the work factor of the algorithm using the `memory`, `time`, and `threads` options; however, the defaults are acceptable for most applications:

    $hashed = Hash::make('password', [
        'memory' => 1024,
        'time' => 2,
        'threads' => 2,
    ]);

> {tip} For more information on these options, check out the [official PHP documentation](https://secure.php.net/manual/en/function.password-hash.php).

<a name="verifying-a-password-against-a-hash"></a>
#### Verifying A Password Against A Hash

The `check` method allows you to verify that a given plain-text string corresponds to a given hash:

    if (Hash::check('plain-text', $hashedPassword)) {
        // The passwords match...
    }

<a name="checking-if-a-password-needs-to-be-rehashed"></a>
#### Checking If A Password Needs To Be Rehashed

The `needsRehash` function allows you to determine if the work factor used by the hasher has changed since the password was hashed:

    if (Hash::needsRehash($hashed)) {
        $hashed = Hash::make('plain-text');
    }
