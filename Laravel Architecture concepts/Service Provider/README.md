# Service Provider

![](images/provider.png)

1. __Service Provider là gì ?__      
	Là trung tâm đăng ký của một project Laravel, bao gồm các core service của frame Laravel như Container bindings, event listener, middleware, route cho đến các service tự custom khác của project.
  Ở file config/app.php, kéo đến array 'Provider', đây là nơi đăng ký tất cả class service được load cho project.

2. __Viết một Service Provider__   
	Tất cả các Service Provider kế thừa class *Illuminate\Support\ServiceProvider*. Hầu hết các Service Provider chứa 2 phương thức *register* và *boot*.
    - Phương thức *register* dùng để đăng ký với Service Container bằng các cách thức binding mà Laravel cung cấp: Basic binding, Singleon binding, Interface binding.
    ```
    class RiakServiceProvider extends ServiceProvider
    {
       public function register()
       {
           $this->app->singleton(Connection::class, function ($app) {
               return new Connection(config('riak'));
           });
       }
       }
    ```  
    - Phương thức *boot* là phương thức được gọi sau khi tất cả các service provider đã được đăng ký xong, nghĩa là ta có thể sử dụng tất cả các service được đăng ký với framework.
    ```
    namespace App\Providers;

    use Illuminate\Support\ServiceProvider;

    class ComposerServiceProvider extends ServiceProvider
    {
        public function boot()
        {
            view()->composer('view', function () {
                //
            });
        }
    }
    ```

    Sau khi viết xong class Service Provider, ta vào *config/app.php*, ở array 'Provider' và add class vừa viết vào.  
    ```
    'providers' => [
    // Other Service Providers

    App\Providers\ComposerServiceProvider::class,
    ],
    ```
3.  __Trì hoãn đăng ký Service Provider__  
  Ta có thể trì hoãn việc đăng ký một Service Provider cho đến khi ứng dụng thực sự cần nó. Điều này sẽ giúp tăng hiệu suất ứng dụng lên đáng kể.
  Để trì hoãn việc load 1 provider, ta implement *\Illuminate\Contracts\Support\DeferrableProvider* interface và định nghĩa phương thức *provides* ( Trả về một Service) :
  ```
  <?php

  namespace App\Providers;

  use Illuminate\Contracts\Support\DeferrableProvider;
  use Illuminate\Support\ServiceProvider;
  use Riak\Connection;

   class RiakServiceProvider extends ServiceProvider implements DeferrableProvider
   {

       public function register()
       {
           $this->app->singleton(Connection::class, function ($app) {
               return new Connection($app['config']['riak']);
           });
       }

       public function provides()
       {
           return [Connection::class];
       }
   }
  ```

# References 
- https://laravel.com/docs/6.x/providers  
  












