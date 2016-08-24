# Travis CI and WordPress Integration
Travis CI WordPress Configuration and Setup for PHP 5.6 and 5.5. With this setup WordPress is fully loaded and run before tests are run. Any WordPress hooks like not be implemeneted like `add_action` and `add_filter`. You can configure it to be other wise but for my needs I have not.

## Local Tests
When doing local tests I use [Laravel Homestead](https://github.com/laravel/homestead) `vagrant box add laravel/homestead`.

Then from the project direcroty like `~/Code/project` I run a few command on the VM to download and install WordPress:

Create the `wordpress` Database:

```
mysql -e "SET NAMES utf8; create database IF NOT EXISTS wordpress;" -uroot
```

Download and install WordPress with `wp-cli.phar`:

```sh
curl -O https://raw.githubusercontent.com/wp-cli/builds/gh-pages/phar/wp-cli.phar
chmod +x wp-cli.phar
./wp-cli.phar core download --allow-root --path=wordpress
./wp-cli.phar core config --allow-root --dbname=wordpress --dbuser=travis --dbhost=127.0.0.1 --path=wordpress
./wp-cli.phar core install --allow-root --admin_name=admin --admin_password=admin --admin_email=admin@example.com --url=http://127.0.0.1 --title=WordPress --path=wordpress
```

In the `Homestead.yaml` file I add the wordpress site:

```
sites:
  - map: tests.wp
    to: /home/vagrant/Code/project/wordpress
```

If you are running tests form the host computer then be sure to edit the `wp-config.php` file to that configuration. Here is what I do because the MySQL port is forwarded on the host computer:

```
/** MySQL hostname */
if( isset($_SERVER['SERVER_NAME']) ) {
    define('DB_HOST', '127.0.0.1');
} else {
    define('DB_HOST', '127.0.0.1:33060'); // Port forwarded by Homestead by default
}
```

## Circle CI
If you need help with Circle CI this is a greate resource http://blog.wppusher.com/continuous-integration-with-wordpress-and-circleci/
