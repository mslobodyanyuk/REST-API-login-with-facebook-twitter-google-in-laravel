LearnByBuildApp 
===============
	How to login with facebook,twitter and google in laravel parts(1-3)
-----------------------------------------------------------------------	
		[part 1 - 8:49](https://www.youtube.com/watch?v=T49xhjxFNX4)
		[part( 2 ( 10:35 ))](https://www.youtube.com/watch?v=JUylqh07Dgc&t=4s)
		[part( 3 ( 9:57 ))](https://www.youtube.com/watch?v=iibOAZQCmwg&t=1s)
---	
	[facebook - 1 ( 5:20 - 8:49 )](https://www.youtube.com/watch?v=T49xhjxFNX4)
	[twitter  - 2 ( 0:00 - 2:20 )](https://www.youtube.com/watch?v=JUylqh07Dgc&t=4s)
	[google+  - 2 ( 2:20 - 4:24 )](https://www.youtube.com/watch?v=JUylqh07Dgc&t=4s)		


How to login with facebook,twitter and google in laravel parts(1/3)
===================================================================
	[part( 1 ( 8:49 ))](https://www.youtube.com/watch?v=T49xhjxFNX4)
---
 [use Laravel documentation -](https://laravel.com/docs/5.4/installation)
 
	`cd C:\Server\data\htdocs\`
---
 `composer create-project --prefer-dist laravel/laravel multi-providers`
---
	`cd multi-providers`
---
 Run Laravel server:
	`php artisan serve`
---	
 Trying browser, in address line:
	<http://localhost:8000>
---
 Now we need to do some database configuratuion in file settings `.env `
---
 Whenever we change the configuratuion we have to clean the config cache:
	`php artisan cache:clear`
---
 Then we need to migrate tables:
	`php artisan migrate`
---
 Now let's build our authentication:
	`php artisan make:auth`
--- 
 Let's run Laravel server to see the changes
 	`php artisan serve`
---	
 As we see we have `Login` and the `Register`.
---
 From documentation click Official Packages -> Socialite 
	[laravel/socialite](https://github.com/laravel/socialite)
---	
 scrolldown to the documentation, this is how to install: 
	`composer require laravel/socialite`
---	
After installing the Socialite library, register the Laravel\Socialite\SocialiteServiceProvider in your `config/app.php` configuration file:
	```php
	'providers' => [
		// Other service providers...
		Laravel\Socialite\SocialiteServiceProvider::class,
	],
	```
---	
Also, add the Socialite facade to the aliases array in your app configuration file:
	```php
	'Socialite' => Laravel\Socialite\Facades\Socialite::class,	
	```
>You will also need to add credentials for the OAuth services your application utilizes. 
>These credentials should be placed in your config/services.php configuration file,
>and should use the key facebook, twitter, linkedin, google, github or bitbucket, depending on the providers your application requires.
>For example:
```php
	'github' => [
		'client_id' => 'your-github-app-id',
		'client_secret' => 'your-github-app-secret',
		'redirect' => 'http://your-callback-url',
	],
```
---
To create facebook application go to this url:
	<https://developers.facebook.com/apps>
---
 Description of how to make a facebook application:	
	[part( 1 ( 5:30 - 8:10 ))](https://www.youtube.com/watch?v=T49xhjxFNX4)
--- 
 - add facebook credentials to config/services.php configuration file: 
	```php
	'facebook' => [
		'client_id' => 'app id',
		'client_secret' => 'add secret',
		'redirect' => 'http://your-callback-url',
	],
   ```
---   
How to login with facebook,twitter and google in laravel parts(2/3)
===================================================================
	[part( 2 ( 10:35 ))](https://www.youtube.com/watch?v=JUylqh07Dgc&t=4s)
---	
To create twitter application visit this url:
		<https://apps.twitter.com>
 Description of how to make a twitter application:		
	[part( 2 ( 0:00 - 1:40 ))](https://www.youtube.com/watch?v=JUylqh07Dgc&t=4s))
-- 
 - add twitter credentials to config/services.php configuration file:
 ```php
	'twitter' => [
		'client_id' => 'app id',
		'client_secret' => 'add secret',
		'redirect' => 'http://your-callback-url',
	],
```
Let's create google application:
	<https://console.developers.google.com>
---	
 Description of how to make a google application:	
	[part( 2 ( 2:20 - 3:50 ))](https://www.youtube.com/watch?v=JUylqh07Dgc&t=4s)  
---
 - add google credentials to config/services.php configuration file:
 	```php
	'google' => [
		'client_id' => 'app id',
		'client_secret' => 'add secret',
		'redirect' => 'http://your-callback-url',
	],	
	```
---	
Now we need to create model with migration:
	`php artisan make model -m SocialProvider`
---	
	Open the migration file and fill create fields table social_providers:
	```php	
		public function up()
		{
			Schema::create('social_providers', function (Blueprint $table) {
				$table->increments('id');
				$table->integer('user_id')->unsigned()->references('id')->on('users');
				$table->string('provider_id');
				$table->string('provider');

				$table->timestamps();
			});
		}
		```
 Let's migrate them:
---
	`php artisan migrate`
---	
 We need to setup the one-to-many relationship between SocialProvider and User.
	In User model in `User.php` add this function:
```php 
     public function socialProviders(){
        return $this->hasMany(SocialProvider::class);
    }
	and inside SocialProvider model in `SocialProvider.php` add this function, function user, and array $fillable:
	    protected $fillable = ['provider_id', 'provider'];
     public function user(){
        return $this->belongsTo(User::class);
    }
```	
>If we switch back to laravel socialite page, laravel/socialite https://github.com/laravel/socialite
>we see two methods here, - one to ask provider to permission, another one to retrieve what we asking for, and change provider to 'facebook':
  ```php
  public function redirectToProvider()
    {
        return Socialite::driver('facebook')->redirect();
    }
    
    public function handleProviderCallback()
    {
        $user = Socialite::driver('facebook')->user();	
        // $user->token;
    }
```		
	and paste them into `RegisterController.php`.
---	
	and let's try to print email for test in handleProviderCallback():
		`return $user->getEmail();`
---	
 We need a routes, copy them from laravel/socialite https://github.com/laravel/socialite in a `routes/web.php` and edit:
>	- just change 'github' to 'facebook' and 'Auth\' to 'RegisterController'
		```php
		Route::get('auth/facebook', 'Auth\RegisterController@redirectToProvider');
		Route::get('auth/facebook/callback', 'Auth\RegisterController@handleProviderCallback');
		```
---		
	from browser - <localhost:8000//auth/facebook>:
	- also add if there is no 'use Socialite;' in `RegisterController.php`.
---	
	refresh browser <localhost:8000//auth/facebook>.
		- if we get an exception:
>		cURL error 60: SSL...
	Go to this url: <https://curl.haxx.se/ca/cacert.pem> 
		* save the file cacert.pem.txt
		* remove the txt-extension
		* copy the file into /php/ext
		* in php.ini paste this line, you gonna change it to match your path
			
>[cURL]
>curl.cainfo = " path to...\php\ext\cacert.pem"
			
		* save the php.ini and restart the Apache.
		* and restart the Laravel server to.
	Try again, in browser:
		<localhost:8000//auth/facebook>
---		
	
	How to login with facebook,twitter and google in laravel parts(3/3)		
=======================================================================	
		[part( 3 ( 9:57 ))](https://www.youtube.com/watch?v=iibOAZQCmwg&t=1s)		
---	
	Now it's time to modify this function handleProviderCallback() in `RegisterController.php`.
	```php
	public function handleProviderCallback()
    {
        try
        {     
            $socialUser = Socialite::driver('facebook')->user();
        }
        catch(\Exception $e)
        {          
            return redirect('/');
        }
        //check if we have logged provider
        $socialProvider = SocialProvider::where('provider_id',$socialUser->getId())->first();

        if (!$socialProvider){
            // create a new user and provider
            $user = User::firstorCreate(
            ['email' => $socialUser->getEmail()],
            ['name' => $socialUser->getName()]
            );
            $user ->SocialProviders()->create(
                ['provider_id'=> $socialUser->getId(), 'provider' => 'facebook' ]
            );
        }
        else
            $user = $socialProvider->user;
            auth()->login($user);
        return redirect('/home');
    }
	```
	Try again, in browser:
		<localhost:8000//auth/facebook>
	- if we have an error:
>		QueryException in Connection.php line 761:
>			SQLSTATE[HYooo]: General error: 1364 Field 'password'...
		- open  `config/database.php`:
		in mysql section change 'strict'=> false, instead of true.
```php
		'mysql' => [
            ...
            'strict' => false,
```			
		Try to login, but don't forget to clear a config cache:
				`php artisan config:cache`
				`php artisan serve`
---				
 refresh browser <localhost:8000//auth/facebook>.				
--- 
	- add parameter {provider} in the `routes/web.php` and include it in redirectToProvider() and handleProviderCallback(); in `RegisterController.php`.
		`routes/web.php`:
		```php
			Route::get('auth/{provider}', 'Auth\RegisterController@redirectToProvider');
			Route::get('auth/{provider}/callback', 'Auth\RegisterController@handleProviderCallback');		
		```			
		`RegisterController.php`:
		```php
  public function redirectToProvider($provider) 
    {                               
        return Socialite::driver($provider)->redirect();
    }

    public function handleProviderCallback($provider)
    {
        try
        {           
            $socialUser = Socialite::driver($provider)->user();
        }
        catch(\Exception $e)
        {
          return redirect('/');
        }
        //check if we have logged provider
        $socialProvider = SocialProvider::where('provider_id',$socialUser->getId())->first();

        if (!$socialProvider){
            // create a new user and provider
            $user = User::firstorCreate(
            ['email' => $socialUser->getEmail()],
            ['name' => $socialUser->getName()]
            );

            $user ->SocialProviders()->create(
                ['provider_id'=> $socialUser->getId(), 'provider' => $provider ]
            );
        }
        else
            $user = $socialProvider->user;

            auth()->login($user);

        return redirect('/home');
    }		
	```