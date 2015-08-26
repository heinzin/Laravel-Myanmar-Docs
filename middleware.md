# HTTP Middleware  ( HTTP ကြားခံအလွှာ )

- [နိဒါန်း](#introduction)
- [ Middleware ကို သတ်မှတ်ခြင်း](#defining-middleware)
- [ Middleware နဲ. Resistering ပြုလုပ်ခြင်း](#registering-middleware)
- [Middleware ရဲ. သတ်မှတ်ချက်များ](#middleware-parameters)
- [ Middlewareကို အဆုံးသတ်ခြင်း](#terminable-middleware)
    
<a name="introduction"></a>
## Introduction
HTTP middleware က web application ထဲကို ဝင်လာတဲ. http requests တွေ အကုန်လုံးကို filtering လုပ်ပေးတဲ. အရာတစ်ခုဖြစ်ပါတယ်။
ဥပမာ ၊ Web application ကိုအသုံးပြုတဲ. User ကို အသုံးပြုခွင်.ရှိမရှိ ကို Laravel မှာ ပါဝင်တဲ. Middleware တစ်ခုက သတ်မှတ်ပေးနေတယ်
တကယ်လို. user က အသုံးပြုခွင်.မရှိလျှင် login screen ကို middleware က redirect လုပ်ပေးပါလိမ်.မယ် 
သို.ပေမဲ. ၊ user က အသုံးပြုခွင်. ရှိလျှင် application ထဲသို.request ဝင်ရန် middleware ကခွင်. ပြုပေး သည် ။
တကယ်တော.။ ထပ်ပေါင်းထည်.ထားတဲ. middleware က authentication လိုမျိုး လည်းပဲ လုပ်ဆောင်ချက်တစ်မျိုးဖြစ် လုပ်ဆောင်ပေးသည်။
CORS middleware တစ်ခုက  ထပ်ပေါင်းထည်.ထားတဲ. headers တွေအတွက် application ကနေပြီး  အကုန်လုံး responses လုပ်ပေးတဲ. တာဝန်ရှိသည်။
logging middleware တစ်ခုက application ထဲကို ဝင်လာသမျှ requests တွေအကုန်လုံးကို log မှတ်ပေးခဲ.နိုင်သည်။
Laravel Framework တွေ များစွာသော middleware တွေပါဝင်ခဲ.သည်၊
maintenance အတွက် middleware,authentication အတွက် middleware,CSRF protection အတွက် middleware နဲ. များစွာသော middlewares များ ပါဝင်နေသည်။
အဲ.ဒီ middleware တွေအကုန်လုံးကတော. `app/Http/Middleware` ဆိုတဲ. directory တွေတည်ရှိနေသည်။

<a name="defining-middleware"></a>
## Middleware ကို သတ်မှတ်ခြင်း

Middleware အသစ်တစ်ခုကို create လုပ်ရန်  အသုံးပြုရမဲ.  Artisan command က`make:middleware` 

    php artisan make:middleware OldMiddleware

အဲ.ဒီ command က `app/Http/Middleware` ဆိုတဲ. directory ထဲမှာ `OldMiddleware` ဆိုပြီးနေရာယူနေလိမ်.မည်။
 အဲ.ဒီ middleware မှာ, 200 `age` ထက် ကြီး လျှင် route က access လုပ်ဖို. ခွင်.ပြုတယ်။ အခြားဟာတွေဆိုရင် user ကိုhome URI သို. redirect လုပ်ပေးသည်။

    <?php

    namespace App\Http\Middleware;

    use Closure;

    class OldMiddleware
    {
        /**
         * Run the request filter.
         *
         * @param  \Illuminate\Http\Request  $request
         * @param  \Closure  $next
         * @return mixed
         */
        public function handle($request, Closure $next)
        {
            if ($request->input('age') <= 200) {
                return redirect('home');
            }

            return $next($request);
        }

    }

ပေးလိုက်တဲ. `age` က `200` နဲ. ညီမယ် ဒါမှမဟုတ် နည်းမယ်ဆိုရင် , middleware က  client သို. http redirect လုပ်ပေးမယ်.၊ တစ်နည်းအားဖြင်. request က  application က ဖြတ်ခွင်.ပေးလိမ်.မယ် ။ , simply call the `$next` callback with the `$request`.
request က ပိုပြီး ဝင်ရောက်သွားမယ် application ထဲကို ( middleware က ဖြတ် ခွင်.ပြုမယ် ) , ရိုးရှင်းစွာ`$next` ကို call မယ် `$request` နဲ.  ပြန်ခေါ်မည် 

middleware ကို a series of layers လို. အကောင်းဆုံးမြင်နိုင်သည် , http request တွေ သည် application ကို မထိရောက်ခင် middleware  ဖြတ်ရမယ်  ။ Layer တစ်ခုချင်းစီ  request ကို သတ်မှတ် နိုင်တယ်  နောက်ပြီး လုံး၀လည်းreject လုပ်နိုင်တယ် 


### *Before* / *After* Middleware

middleware တစ်ခုက မလုပ်ခင် မှာ runမယ် လုပ်ပြီးတော.မှ run မယ်ဆိုတာ ကတော. middleware ပေါ်မှာဘဲ မူတည်သည်။ 
ဥပမာ  ၊ application ဖြင်. request ကို handle မလုပ် မီ အချို.  task တွေကို  အောက်ပါ middleware  လုပ်ဆောင်လိမ်.မယ်  


    <?php

    namespace App\Http\Middleware;

    use Closure;

    class BeforeMiddleware
    {
        public function handle($request, Closure $next)
        {
            // Perform action

            return $next($request);
        }
    }

သို.ရာတွေ , application ဖြင်. request ကို handle လုပ်ပြီးတော.မှ  သူ.ရဲ. task ကို this  middleware က လုပ်ဆောင်လိမ်.မယ်  

    <?php

    namespace App\Http\Middleware;

    use Closure;

    class AfterMiddleware
    {
        public function handle($request, Closure $next)
        {
            $response = $next($request);

            // Perform action

            return $response;
        }
    }

<a name="registering-middleware"></a>
## Middleware နဲ. Registering  ပြုလုပ်ခြင်း 

### Global Middleware

Application ထဲကို ဝင်လာတဲ. HTTP request တိုင်း ကို ဖမ်းတဲ. middleware ကို လိုချင်လျှင် , simply  list the middleware class မှာ `app/Http/Kernel.php` class.  ၏`$middleware` property ထဲတွင် ရှိသည်။


### Assigning Middleware To Routes

 middlware နဲ.  routes တွေကို အတိအကျ သတ် မှတ် ချင်လျှင် ၊ the middleware a short-hand key ကို `app/Http/Kernel.php`  ဆိုတဲ. file ထဲ မှာ သတ် မှတ် ရမည် ။ Default အနေနဲ. အဲ.ဒီ class ၏ `$routeMiddleware` propertiy  က  
 the middleware အတွက် Laravel နဲ.အတူ ပါ၀င်ခွင်. ရှိ သည်။ `App\Http\Kernel` Class   ထဲက list ထဲကို Key တစ်ခု  ကိုယ်တိုင် ပေါင်းထည်.ပေးရမည်။


 For example:

    // Within App\Http\Kernel Class...

    protected $routeMiddleware = [
        'auth' => \App\Http\Middleware\Authenticate::class,
        'auth.basic' => \Illuminate\Auth\Middleware\AuthenticateWithBasicAuth::class,
        'guest' => \App\Http\Middleware\RedirectIfAuthenticated::class,
    ];

Once the middleware has been defined in the HTTP kernel, you may use the `middleware` key in the route options array:

    Route::get('admin/profile', ['middleware' => 'auth', function () {
        //
    }]);

<a name="middleware-parameters"></a>
##Middleware ရဲ. သတ်မှတ်ချက်များ

additional custom parameters ကို Middleware က လက်ခံနိုင်တယ် ။
For example, if your application needs to verify that the authenticated user has a given "role" before performing a given action, 
ဥပမာ၊ the authenticated user  သည်  role လို.ပေး ထားတဲ.  action ကို မလုပ် ဆောင်မှီ  application က vertify လုပ်ရန်လိုသည်။ an additional argument အဖြင်. လက်ခံရန်  role name တစ်ခုအနေနဲ. ဖြစ် သော  `RoleMiddleware` ကို create လုပ်ရမည် ။ 

The `$next` argument ပြီးတော.  Additional middleware parameters က middleware ထဲကို ဖြတ်သန်းသွားလိမ်.မည်။

    <?php

    namespace App\Http\Middleware;

    use Closure;

    class RoleMiddleware
    {
        /**
         * Run the request filter.
         *
         * @param  \Illuminate\Http\Request  $request
         * @param  \Closure  $next
         * @param  string  $role
         * @return mixed
         */
        public function handle($request, Closure $next, $role)
        {
            if (! $request->user()->hasRole($role)) {
                // Redirect...
            }

            return $next($request);
        }

    }



`route` ကို  the middleware  name နဲ. parameter တွေကို `:` နဲ. အတူ ပိုင်းခြားပြီး  သတ် မှတ်ထားသောအခါ Middleware parameters တွေကို သတ်မှတ်ခဲ.ချင်မှ သတ်မှတ်လိမ်.မည်၊။ commas ဖြင်.  Multiple parameters တွေကို ကန်.သတ်ထားရမည်။
    


    Route::put('post/{id}', ['middleware' => 'role:editor', function ($id) {
        //
    }]);

<a name="terminable-middleware"></a>
## Middlewareကို အဆုံးသတ်ခြင်း



 HTTP response က Browser ကို ပို.ပေးပြီးနောက်  တစ်ခါတစ်ရံ middleware က  အချို.အလုပ်တွေကိုလုပ် ပေးရန်လိုသည် ။ For example,  Browser ကို response လုပ် ပေးပြီးနောက် Laravel မှာပါတဲ. the "session" middleware က  the session data ကို သိမ်းထားပေးသည်၊   `terminate` method ကို the  middleware ထဲ ပေါင်းထည်.ခြင်းဖြင်. အဆုံးသတ် သည်။
  

    <?php
    
    namespace Illuminate\Session\Middleware;

    use Closure;

    class StartSession
    {
        public function handle($request, Closure $next)
        {
            return $next($request);
        }

        public function terminate($request, $response)
        {
            // Store the session data...
        }
    }

The `terminate` method သည်  the request နဲ. the response  နှစ်ခုစလုံးကို လက်ခံရမည်။တစ်ကြိမ် terminable middleware တစ်ခုကို သတ် မှတ်ပြီးတိုင်း    HTTP kernel ထဲကို global middlewares ရဲ.  list ထဲကို terminable middleware ထည်.ရမည် ။

Middleware မှာ `terminate` method ကို ခေါ် သောအခါ middleware ရဲ. ဖြစ်စဥ် အသစ်တစ်ခုကို  Laravel က  [service container](http://laravel.com/docs/5.1/container) မှ  ဆုံးဖြတ်ပေးသည်။ `handle` and `terminate` methods တွေ ကို ခေါ်ေသာအခါ တူညီတဲ. middleware ဖြစ်စဥ်  ကို အသုံးပြုချင်လျှင်  container ရဲ. `singleton` method ကိုသုံးပြီး  middleware ကို container နဲ.အတူ register လုပ်ပြီးသုံးနိုင်သည်။
