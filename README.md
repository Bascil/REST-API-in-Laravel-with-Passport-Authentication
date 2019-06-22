# REST-API-in-Laravel-with-Passport-Authentication
Hi Guys. This example illustrates how to create a RESTful API using Laravel Passport Authentication. In this example, we will see how to use laravel passport authentication in your Laravel application. When a user login via API, a token is generated and sent to the user which is used for authentication purposes. 

I will explain step by step how to create rest api using passport in laravel 5.7

#### Steps :

- Install Laravel 5.7

- Create Database

- Install Passport

- Configure [Laravel Passport](https://laravel.com/docs/5.7/passport)

- Add Product Table and Model

- Create API Routes

- Create Controller Files


### Installation Step by Step:

1- Install Laravel 5.7:  

    > composer create-project --prefer-dist laravel/laravel rest-api

2- Create a Database in MySQL (choose a name, for example: rest-api) then create a new .env file

    DB_CONNECTION=mysql
    DB_HOST=127.0.0.1
    DB_PORT=3306
    DB_DATABASE=rest-api
    DB_USERNAME=root
    DB_PASSWORD=
    
3- Install Passport

    - We need to install Laravel Passport package via the composer dependency manager

      > composer require laravel/passport

    - After successfully installing passport package, we create new passport tables in our database. 

      > php artisan migrate

    - Next, we need to install passport to create token keys for security. 

     > php artisan passport:install
   
4- Passport Configuration:

    - In this step, we configure model, service provider and auth file.

    - In model app/User.php , we add HasApiTokens class of Passport

    ```
    <?php

        namespace App;

        use Illuminate\Notifications\Notifiable;
        use Illuminate\Contracts\Auth\MustVerifyEmail;
        use Laravel\Passport\HasApiTokens;
        use Illuminate\Foundation\Auth\User as Authenticatable;

        class User extends Authenticatable implements MustVerifyEmail {

            use HasApiTokens, Notifiable;

            /**
            * The attributes that are mass assignable.
            *
            * @var array
            */

            protected $fillable = [
                'name', 'email', 'password',
            ];

            /**
            * The attributes that should be hidden for arrays.
            *
            * @var array
            */

            protected $hidden = [
                'password', 'remember_token',
            ];
        }

        ```

    - In app/Providers/AuthServiceProvider.php, we add "Passport::routes()",

        ```
        <?php
        
            namespace App\Providers;

            use Laravel\Passport\Passport;
            use Illuminate\Support\Facades\Gate;
            use Illuminate\Foundation\Support\Providers\AuthServiceProvider as ServiceProvider;

            class AuthServiceProvider extends ServiceProvider {
            /**
            * The policy mappings for the application.
            *
            * @var array
            */

            protected $policies = [
                'App\Model' => 'App\Policies\ModelPolicy',
            ];

            /**
            * Register any authentication / authorization services.
            *
            * @return void
            */

            public function boot()
            {
                $this->registerPolicies();
                Passport::routes();
            }
           }

        ```
    - In config/auth.php, we add api auth configuration

        ```
        <?php

            return [

                .....
                'guards' => [

                    'web' => [
                        'driver' => 'session',
                        'provider' => 'users',
                    ],

                    'api' => [
                        'driver' => 'passport',
                        'provider' => 'users',
                    ],
                ],
                .....
            ]

        ```

5- Add Product Table and Model:

   - Next, we need to create migration for products table using Laravel 5.7 php artisan command,

     > php artisan make:migration create_products_table

   - After this command you will find one file in following path database/migrations 
     
     ```
      <?php

        use Illuminate\Support\Facades\Schema;
        use Illuminate\Database\Schema\Blueprint;
        use Illuminate\Database\Migrations\Migration;

        class CreateProductsTable extends Migration {
        /**
            * Run the migrations.
            *
            * @return void
            */

            public function up()
            {
                Schema::create('products', function (Blueprint $table) {

                    $table->increments('id');
                    $table->string('name');
                    $table->text('detail');
                    $table->timestamps();

                });
            }

            /**
            * Reverse the migrations.
            *
            * @return void
            */

            public function down()
            {
                Schema::dropIfExists('products');
            }

        }

     ```


