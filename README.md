# QR Decoder
This is an example of how to run https://github.com/khanamiryan/php-qrcode-detector-decoder library with a Mac.

I use this with the combination of https://github.com/pcarrier/gauth to manage my 2FA secrets from the command line.

## Install PHP
```
$ brew install php
```

## Install composer
Please refer to the Composer Download instruction as this can change.
https://getcomposer.org/download/ 

```
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
php -r "if (hash_file('sha384', 'composer-setup.php') === 'c5b9b6d368201a9db6f74e2611495f369991b72d9c8cbd3ffbc63edff210eb73d46ffbfce88669ad33695ef77dc76976') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
php composer-setup.php
php -r "unlink('composer-setup.php');"
```

## Download dependencies
```
$ php composer.phar require khanamiryan/qrcode-detector-decoder
```

## Put the image of the QR code you want to decode in the img directory in png format.
e.g. img/okta.png

And make sure to change to update the path in the `qr_decoder.php` file.
```php
$qrcode = new QrReader('./img/okta.png');
```

## Run the script
```
$ php qr_decoder.php
```

You will get an OTP URI grab the secret param value and use it with Gauth :) 

## Tips with gauth
First clone the repo in the folder you want:
```
$ git clone git@github.com:pcarrier/gauth.git
```

I have my ~/bin folder in my $PATH environment. Run this command if you don't have the bin folder in your PATH.
```
$ echo 'export PATH="$HOME/bin:$PATH"' >> ~/.zshrc
```

```
$ cd gauth
```

Build the gauth library in your bin folder.
```
$ go build -o ~/bin/gauth
```

Create your gauth.csv file in ~/.config folder
```
$ mkdir ~/.config
```

```
$ vi ~/.config/gauth.csv
```

Edit your file with:
```
GitHub:s3cr3t
Okta:s3cr3t
```

Where secrets are extracted from the QR code using this PHP script.

Rename the gauth.csv file into gauth.csv.plain.
```
$ mv gauth.csv gauth.csv.plain
```

Next, you can encrypt the gauth.csv file with the following command

You will get asked to set up an encryption password that you will need to remember when you want to decrypt in the future.

```
$ openssl enc -aes-128-cbc -md sha256 -in gauth.csv.plain -out gauth.csv

enter aes-128-cbc encryption password:
Verifying - enter aes-128-cbc encryption password:
```

You can delete the gauth.csv.plain file
```
$ rm gauth.csv.plain
```

If you want to add more secrets, you need decrypt
```
$ openssl enc -d -aes-128-cbc -md sha256 -in gauth.csv -out gauth.csv.plain
```
Edit the gauth.csv.plain file and encrypt again

```
$ openssl enc -aes-128-cbc -md sha256 -in gauth.csv.plain -out gauth.csv
```

and delete the plain file.

The Gauth tool will see that you have an ecrypted file and it will ask for your encryption password.

```
$ gauth
Encryption password:
       prev   curr   next
Okta   408735 037817 788014
GitHub 672872 746853 938315
[=====                        ]
```

