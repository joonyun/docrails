레일스 시작하기
==========================

본 가이드는 루비온레일스를 준비하여 실행하는 것에 대해 다루게 된다. 따라서 본 가이드를 읽은 후에는 아래의 내용들에 대해서 친숙하게 될 것이다.

* 레일스 인스톨하는 방법, 레일스 어플리케이션을 새로 생성하는 방법, 그리고 어플리케이션을 데이터베이스에 연결하는 방법.
* 레일스 어플리케이션의 일반적인 레일아웃 구성
* MVC(모델, 뷰, 컨트롤러)와 REST 방식 디자인의 기본 원리
* 레일스 어플리케이션의 시작 코드를 신속하게 생성하는 방법

--------------------------------------------------------------------------------

WARNING. 본 가이드는 레일스 3.2에 근거한다. 따라서 본 가이드에서 보게되는 코드 중 일부는 레일스 3.2 이전 버전에서는 작동하기 않을 것이다.

[Guide Assumptions] 가이드 전제조건 
-----------------

본 가이드는 레일스를 처음부터 시작하는 초보자를 위해 디자인되었다. 따라서 이전에 레일스에 대한 경험이 없는 것으로 가정하고 작성되었다. 그러나, 레일스가 제대로 동작하기 위해서는 아래의 조건을 만족해야 한다. 

* The [Ruby](http://www.ruby-lang.org/en/downloads) language version 1.9.3 or higher
* The [RubyGems](http://rubyforge.org/frs/?group_id=126) packaging system
    * To learn more about RubyGems, please read the [RubyGems User Guide](http://docs.rubygems.org/read/book/1)
* A working installation of the [SQLite3 Database](http://www.sqlite.org)

레일스는 루비 프로그래밍 언어로 동작되는 웹어플리케이션 프레임워크이다. 이전에 루비언어에 대한 경험이 없다면 레일스를 배우기가 매우 어려울 것이다. 인터넷 상에 아래와 같이 루비를 배우기 위한 훌륭한 리소스들을 무상으로 제공되므로 참고하기 바란다.

* [Mr. Neighborly's Humble Little Ruby Book](http://www.humblelittlerubybook.com)
* [Programming Ruby](http://www.ruby-doc.org/docs/ProgrammingRuby/)
* [Why's (Poignant) Guide to Ruby](http://mislav.uniqpath.com/poignant-guide/)

[What is Rails] 레일스란 무엇인가?
--------------

레일스는 루비언어로 작성된 웹어플리케이션 개발 프레임워크이다. 
레일스는 모든 개발자가 시작할 때 필요로하는 것들을 미리 만들어 주어 웹 어플리케이션 프로그래밍을 보다 쉽게 도와주도록 디자인되었다. 따라서 레일스는 다른 많은 언어와 프레임워크 보다 더 많은 것을 수행하지만 작업해야 할 코드량은 더 적게 해 줍니다.
경험이 많은 레일스 개발자는 레일스가 웹어플리케이션 개발을 더 재밋게 해 준다고 말한다. 

레일스는 독선적인 소프트웨어이다. 즉, 작업을 하기 위한 최상의 방법이 있다고 가정하고 그러한 방식대로 할 것을 권장하도록 디자인되어 있으며 어떤 경우에는 다른 대안에 대해서 권장하지 않는다. "레일스 방식"을 알게 되면 아마도 놀라울 정도의 생성성 향상을 발견하게 될 것이다. 레일스 어플리케이션에 대해서 기존 방식의 습관을 고집할 경우나 다른 곳에서 배운 바를 사용하고자 한다면 아마도 더 즐거운 경험을 가지지 못할 수도 있다. 

레일스 철학은 두가지 주요 가이드 원칙을 포함한다.

* DRY - "Don't Repeat Yourself" - 이것은 동일한 코드를 반복해서 작성하는 것은 좋지 않다는 것을 의미한다.
* Convention Over Configuration - 이것은 레일스가 이미 개발자가 하고자 하는 것과 구현하고자 하는 방법을 미리 가정해 놓고 있다는 것을 의미한다. 즉, 끝없이 많은 구성 파일을 통해서 모든 세세한 것까지 설정하도록 요구하지 않는다는 것이다.

[What is Rails] 레일스 프로젝트 새로 생성하기
----------------------------

본 가이드를 시용하는 최선의 방법은 각 단계를 따라 가는 것이며 이 예제 어플리케이션을 작성하기 위해 어떠한 코드나 단계도 추가로 필요하지 않기 때문에 문자 그대로 단계별로 따라하기만 하면 된다. 전체 소스 코드는 [여기](https://github.com/lifo/docrails/tree/master/guides/code/getting_started)에서 얻을 수 있다. 

본 가이드를 따라하게 되면, 매우 간단한 웹블로그인 `blog`라는 레일스 프로젝트를 만들게 될 것이다. 이 `blog` 어플리케이션을 시작하기 전에 레일스가 인스톨되어 있는지 확인해야 할 필요가 있다.

TIP: 아래의 예문에서는 유닉스계열 운영시스템에서 superuser와 일반 user 터미널 프롬프트를 표시하기 위해 사용하는 # 와 $ 기호를 사용한다. 윈도우를 사용할 경우에는 c:\source_code> 와 같이 보이게 된다.

### [Installing Rails] 레일스 설치하기

커맨트라인 프롬프트를 연다. Mac에서는 ternimal이라고 하고, 윈도우에서는 command prompt라고 한다. `$` 표시가 앞에 있는 명령은 커맨트라인에서 실행해야 한다. 아래와 같은 명령으로 설치된 루비의 버전을 확인한다.

```bash
$ ruby -v
ruby 1.9.3p194
```

레일스를 인스톨하기 위해서 RubyGems에서 제공하는 `gem install` 명령을 사용할 수 있다.

```bash
$ gem install rails
```

TIP. 시스템에 루비와 루비온레일스를 인스톨할 수 있도록 해주는 툴들이 많이 있다. 윈도우 사용자는 [Rails Installer](http://railsinstaller.org)를, Mac OS X 사용자는 [Rails One Click](http://railsoneclick.com)를 사용할 수 있다.

모든 것이 제대로 인스톨되었는지 확인하기 위해 아래와 같이 레일스 버전을 확인해 본다.

```bash
$ rails --version
```

"Rails 3.2.8"와 같이 표시되면 다음으로 진행할 준비가 된 것이다.

### [Creating the Blog Application] Blog 어플리케이션 생성하기

레일스는 개발을 더 쉽게 하기 위해 많은 제너레이터를 제공해 준다. 이들 중에 하나가 어플리케이션 제너레이터인데, 레일스 어플리케이션을 시작할 수 있는 기본 코드를 생성해 주어 개발자가 직접 코드를 처음부터 작성할 필요가 없게 해준다. 

이 제너레이터를 사용하기 위해서, 우선 터미널을 열고, 파일을 생성할 수 있는 권한이 있는 디렉토리로 이동하여 다음과 같이 명령을 실행한다.

```bash
$ rails new blog
```

위의 결과로 blog라는 디렉토리에 Blog 어플리케이션이 생성되고 또한, `bundle install`을 실행하면 `Gemfile`에 이미 기술되어 있는 젬들이 설치될 것이다.

TIP: `rails new -h` 명령을 실행하면 레일스 어플리케이션 제너레이터에 추가할 수 있는 모든 커맨트라인 옵션을 볼 수 있다. 

blog 어플리케이션을 생성한 후 blog 디렉토리로 이동하면 어플리케이션에서 대해서 바로 작업을 계속할 수 있게 된다. 

```bash
$ cd blog
```

이제 `rails new blog` 명령으로 `blog` 디렉토리가 생성되었다. `blog` 디렉토리는 레일스 어플리케이션을 구성하도록 자동으로 생성된 많은 파일과 폴더를 포함하게 된다. 이 튜토리얼에서 보게 되는 대부분의 작업은 `app/` 폴더 상에 이루어지게 될 것이지만 아래에 레일스가 디폴트로 생성하는 각 파일과 폴더의 기능에 대해 간단히 설명을 한다.

| File/Folder | Purpose |
| ----------- | ------- |
|app/|Contains the controllers, models, views, helpers, mailers and assets for your application. You'll focus on this folder for the remainder of this guide.|
|config/|Configure your application's runtime rules, routes, database, and more.  This is covered in more detail in [Configuring Rails Applications](configuring.html)|
|config.ru|Rack configuration for Rack based servers used to start the application.|
|db/|Contains your current database schema, as well as the database migrations.|
|doc/|In-depth documentation for your application.|
|Gemfile<br />Gemfile.lock|These files allow you to specify what gem dependencies are needed for your Rails application. These files are used by the Bundler gem. For more information about Bundler, see [the Bundler website](http://gembundler.com) |
|lib/|Extended modules for your application.|
|log/|Application log files.|
|public/|The only folder seen to the world as-is. Contains the static files and compiled assets.|
|Rakefile|This file locates and loads tasks that can be run from the command line. The task definitions are defined throughout the components of Rails. Rather than changing Rakefile, you should add your own tasks by adding files to the lib/tasks directory of your application.|
|README.rdoc|This is a brief instruction manual for your application. You should edit this file to tell others what your application does, how to set it up, and so on.|
|script/|Contains the rails script that starts your app and can contain other scripts you use to deploy or run your application.|
|test/|Unit tests, fixtures, and other test apparatus. These are covered in [Testing Rails Applications](testing.html)|
|tmp/|Temporary files (like cache, pid and session files)|
|vendor/|A place for all third-party code. In a typical Rails application, this includes Ruby Gems and the Rails source code (if you optionally install it into your project).|

Hello, Rails!
-------------

시작을 위해 화면상에 약간의 텍스트를 바로 보이게 해 보자. 이를 위해서는, 레일스 어플리케이션 서버가 실행되어야 한다.

### [Starting up the Web Server] 웹서버 시작하기

이미 실제 기능을 하는 레일스 어플리케이션을 만든 상태이므로 어플리케이션을 실행해 보기 위해서는 개발 장비에서 웹서버를 시작해야 한다. 다음과 같이 할 수 있다.

```bash
$ rails server
```

TIP: Coffeescript를 Javascript로 컴파일하기 위해서는 Javascript 런타임 라이브러리가 필요한데 없는 경우 `execjs` 에러가 발생하게 될 것이다. 대개 Mac OS X과 윈도우는 이것이 설치되어 배포된다. 레일스는 어플리케이션이 새로 생성될 때 `therubyracer` 젬을 코멘트 처리해서 Gemfile에 추가해 주므로 필요할 때 코멘트 표시를 제거하여 인스톨할 수 있다. JRuby 사용자들에게 추천되는 런타임 라이브러리는 `therubyrhino`이며 JRuby환경에서 레일스 어플리케이션을 새로 생성할 때 Gemfile에 디폴트로 추가된다. [ExecJS](https://github.com/sstephenson/execjs#readme)를 방문하면 지원되는 모든 런타임 라이브러리를 찾아 볼 수 있다.

이제 디폴트로 루비에 내장된 웹서버인 WEBrick이 실행될 것이다. 어플리케이션이 실행되는 것을 보기 위해서 브라우저 윈도우를 열고 <http://localhost:3000>로 접속한다. 이 때 아래와 같이 레일스의 디폴트 정보 페이지를 보게 될 것이다. 

![Welcome Aboard screenshot](images/rails_welcome.png)

TIP: 웹서버를 중지하려면 서버가 실행 중인 터미널에서 Ctrl+C 를 누르면 된다. 서버가 중단되면 터미널에서 커맨드 프롬프트 커서가 다시 보이게 되는데, Mac을 포함해서 대부분의 유닉스 계열 시스템에서는 `$` 로 표시된다. 개발환경에서는 수정된 코드를 서버가 자동으로 반영하기 때문에 그 때마다 서버를 다시 시작하지 않아도 된다. 

"Welcome Aboard" 페이지는 새로 생성된 레일스 어플리케이션의 _smoke test_ 이다. 즉, 웹어플리케이션이 제대로 구성되어 웹페이지를 볼 수 있다는 것을 확인하는 페이지인 것이다. 또한 _About your application's environment_ 링크를 클릭하면 어플리케이션 환경에 대한 요약정보를 볼 수 있다. 

### Say "Hello", Rails

레일스가 웹페이지에 "Hello"라고 표시하도록 하려면, 최소한 하나의 _컨트롤러_ 와 하나의 _뷰_ 가 있어야 한다.

컨트롤러의 역할은 어플리케이션에 대한 임의의 요청를 받는 것이다. _라우팅_ 은 어떤 컨트롤러가 어떤 요청를 받게 되는지를 결정하게 된다. 종종, 각 컨트롤러에 하나 이상의 라우트가 정의되고 각기 다른 라우트는 자신의 _액션_ 을 가지게 된다. 액션의 역할은 뷰에 제공할 정보를 취합하는 것이다. 

뷰의 역할은 사람이 읽을 수 있는 형태로 이러한 정보를 보이게 하는 것이다. 중요한 차이점은 정보를 취합하는 곳이 뷰가 아니고 _컨트롤러_ 라는 것이다. 뷰는 단지 특정 정보를 보이게 한다는 것이다. 디폴트로 뷰 템플릿은 사용자로 보내지기 전에 레일스에서 요청주기에 의해 변환되는 ERB(Embeded Ruby)라는 언어로 작성된다.

컨트롤러를 새로 생성하기 위해서는 "controller" 제너레이터를 실행해서 아래와 같이 "index"라는 액션을 가지는 "welcome" 컨트롤러를 명시해 준다.

```bash
$ rails generate controller welcome index
```
이때, 레일스는 개발자를 대신해서 여러 종류의 파일과 하나의 라우트를 생성하게 될 것이다.

```bash
create  app/controllers/welcome_controller.rb
 route  get "welcome/index"
invoke  erb
create    app/views/welcome
create    app/views/welcome/index.html.erb
invoke  test_unit
create    test/controllers/welcome_controller_test.rb
invoke  helper
create    app/helpers/welcome_helper.rb
invoke    test_unit
create      test/helpers/welcome_helper_test.rb
invoke  assets
invoke    coffee
create      app/assets/javascripts/welcome.js.coffee
invoke    scss
create      app/assets/stylesheets/welcome.css.scss
```

이 중에 가장 중요한 것은 `app/controllers/welcome_controller.rb`에 위치한 컨트롤러와 `app/views/welcome/index.html.erb`에 위치한 뷰이다.

텍스트 에디터에서 `app/views/welcome/index.html.erb` 파일을 열고 아래와 같이 코드 한줄을 추가해 준다.

```html
<h1>Hello, Rails!</h1>
```

### [Setting the Application Home Page] 어플리케이션 홈 페이지 세팅하기

컨트롤러와 뷰를 만들었으므로 이제 Hello Rails!라는 문구를 보여줄 시점을 레일스에게 알려주어야 한다. 여기서는, 웹사이트의 루트 주소로, <http://localhost:3000>, 이동할 때 보이도록 하겠다. 그러나 아직 "Welcome Aboard" _smoke test_ 페이지가 해당 위치에 있다. 

따라서 "welcome" 컨트롤러의 "index" 뷰가 루트 주소상에서 제대로 보이게 하기 위해서는 어플리케이션의 `public` 디렉토리에 있는 `index.html` 파일을 삭제해 주어야 한다.

레일스는 컨트롤러에서 동적으로 생성하는 컨텐츠에 우선해서 라우트와 일치하는 `public` 디렉토리상에 있는 정적 파일을 불러오기 때문에 이러한 조치가 필요한 것이다. `index.html` 파일은 조금 특수한데, 예를 들어 <http://localhost:3000>와 같은 루트 경로를 요청할 때 이 파일을 응답으로 보내게 된다. <http://localhost:3000/welcome>와 같이 요청이 오게 되면 `public/welcome.html`이 존재할 경우 우선적으로 보내지지게 된다.

이제 실제 홈페이지의 위치를 레일스에게 알려주어야 한다. 

에디터에서 `config/routes.rb` 파일을 열고 아래와 같이 추가한다.

```ruby
Blog::Application.routes.draw do
  get "welcome/index"

  # The priority is based upon order of creation:
  # first created -> highest priority.
  # ...
  # You can have the root of your site routed with "root"
  # just remember to delete public/index.html.
  # root to: "welcome#index"
```

이것은 어플리케이션의 _라우팅 파일_ 인데, 외부로부터 들어오는 요청을 컨트롤러와 액션으로 연결하는 방법을 레일스에게 알려누는 특수한 DSL(domain-specific language)로 경로들의 목록을 기술하고 있다. 이 파일에는 여러가지 샘플 경로명이 코멘트로 표시되어 있으며 그 중에 하나에서는, 루트경로를 특정 컨트롤러와 액션에 연결하는 방법을 실제로 보여 준다. `root :to` 로 시작하는 코드라인을 찾아서 코멘트를 제거하면 아래와 같이 보이게 된다. 

```ruby
root to: "welcome#index"
```

`root to: "welcome#index"` 는 어플리케이션의 루트로 들어오는 요청을 welcome 컨트롤러의 index 액션으로 연결하도록 레일스에게 알려주게 되고, `get "welcome/index"` 는 <http://localhost:3000/welcome/index> 로 들어오는 요청을 welcome 컨트롤러의 index 액션으로 연결하도록 한다. 이것은 컨트롤러 제너레이터(`rails generate controller welcome index`)를 실행했을 때 이미 만들어진 경로이다.

브라우저 상에서 <http://localhost:3000> 주소를 연결할 때, `app/views/welcome/index.html.erb` 파일에 추가했던 `Hello, Rails!` 메시지를 볼 수 있게 되는데, 이것은 새로 추가한 경로가 `WelcomeController`의 `index` 액션으로 연결토록 지시하여 뷰를 정확하게 렌더링한다는 것을 의미합니다. 

TIP: 라우팅에 대해 더 자세한 내용을 원하면, [Rails Routing from the Outside In](routing.html) 를 참조하기 바란다.

[Getting Up and Running] 제대로 시작하기
----------------------

이제 컨트롤러, 액션 뷰를 만드는 방법을 알게되었으니 좀 더 실질적인 것을 만들어 보도록 한다. 
Blog 어플리케이션에서 _resource_ 를 하나 새로 만들게 될 것이다. 하나의 resource란 posts, people 또는 animals와 같이 비슷한 객체들의 모음을 말하는 용어라 할 수 있다. 특정 resource에 대한 항목들을 생성, 읽기, 업데이트, 삭제할 수 있는데, 이러한 동작을 _CRUD_ 작업이라고 한다.

다음 섹션에서, post를 새로 추가해서 내용을 볼 수 있도록 할 것이다. 이것은 CRUD의 "C"와 "R"에 해당하며, 각각 creating과 reading을 의미한다. 이를 위한 form은 다음과 같다. 

![The new post form](images/getting_started/new_post.png)

현재로서는 매우 간단하게 보일지라도 괜찮다. 나중에 좀더 모양을 좋게 할 것이다. 

### [Laying down the ground work] 초석 다지기

어플리케이션에서 post를 하나 새로 만들고자 할 때 가장 먼저 해야할 것을 작업을 할 위치를 확보하는 것이다. 가장 좋은 장소로 `/posts/new` 를 들 수 있다. 지금 <http://localhost:3000/posts/new> 위치로 이동하게 되면 라우팅 에러가 발생할 것이다.

![A routing error, no route matches /posts/new](images/getting_started/routing_error_no_route_matches.png)

왜냐하면 `config/routes.rb` 내에 이 경로에 대한 정의가 없기 때문이다. 디폴트로 레일스는 먼저 정의했던 루트경로외에는 추가로 정의한 경로가 없기 때문에 해당 경로에 대한 라우트를 정의해 주어야 한다.

이를 위해서 `config/routes.rb` 파일내 `draw` 메소드의 `do` 와 `end` 사이에 하나의 경로를 생성해 주어야 한다. 

```ruby
get "posts/new"
```

이 라우트의 정의는 매우 간단하다. 하나의 새로운 경로를 정의하는데 `GET` 요청에 대해서만 응답하게되며, 실제 경로는 `posts/new` 가 된다. 그러나, `:to` 옵션을 사용하지 않았는데 매칭 되는 경로를 어떻게 알 수 있을지 궁금할 것이다. 이 상태에서는 레일스가 인지할 수 있는 디폴트 매핑법을 사용하게 되는데, 레일스는 posts 컨트롤러내의 new 액션으로 라우팅이 되도록 할 것이다. 

경로가 정의되어 있을 경우, 어플리케이션상의 `posts/new` 로 요청이 들어올 수 있다. <http://localhost:3000/posts/new>으로 이동하게 되면 또 다른 라우팅 에러를 접하게 될 것이다.

![Another routing error, uninitialized constant PostsController](images/getting_started/routing_error_no_controller.png)

이번 에러가 발생하는 이유는 방근 전에 정의된 라우트에서 매핑될 컨트롤러가 정의되지 않았기 때문이다. 이 라우트는 요청을 처리하기 위해 매핑된 컨트롤러를 찾으려고 할 것이지만 해당 컨트롤러가 아직 정의되지 않아서 요청을 수행할 수 없게 되는 것입니다. 이 문제를 해결하기 위한 방법은 간단하다. 즉, `PostsController`라는 컨트롤러를 만들면 되는 것이다. 이를 위해서 아래와 같이 작업을 해 주면 된다. 

```bash
$ rails g controller posts
```

방금 전에 새로 만든 `app/controllers/posts_controller.rb` 파일을 에디터로 열어 보면 비어 있는 컨트롤러를 보게 될 것이다.

```ruby
class PostsController < ApplicationController
end
```

컨트롤러는 하나의 클래스이며 `ApplicationController`로부터 상속을 받도록 정의된다. 이 클래스내에 해당 컨트롤러의 액션메소드를 정의하게 되는 것입니다. 이 액션들은 어플리케이션내에서 posts에 대한 CRUD 작업을 수행하게 될 것이다. 

이제 <http://localhost:3000/posts/new>를 다시로딩하게 되면 아래와 같이 또 다시 에러가 발생할 것입니다.

![Unknown action new for PostsController!](images/getting_started/unknown_action_new_for_posts.png)

이번 에러는 방금 전에 생성했던 `PostsController` 내에서 `new` 액션을 찾지 못했다는 것을 알려준다. 이것은 컨트롤러가 디폴트 상태로 생성될 때 필요로하는 액션들을 명시되지 않음으로 인해 실제로 컨트롤러 내부에 아무런 액션이 정의되어 있지 않기 때문에 발생한 것이다.

컨트롤러 내에 액션을 직접 정의하고자 한다면, 컨트롤러 내에 new 메소드를 정의하면 된다. `app/controllers/posts_controller.rb` 파일을 열고 아래와 같이 `PostsController` 내에 `new` 액션을 정의해 보자.

```ruby
def new
end
```

`PostsController` 내에 `new` 액션 메소드가 정의된 상태에서, <http://localhost:3000/posts/new> 경로를 다시 로딩하면 또 다른 에러를 보게 된다. 

![Template is missing for posts/new](images/getting_started/template_is_missing_posts_new.png)

레일스는 이와 같이 일반적인 액션이 자신의 정보를 보여주게 된 뷰를 가지고 있을 것으로 기대하기 때문이 이런 에러가 발생하는 것이다. 해당 뷰가 없다면 레일스는 에러를 발생하게 된다. 

위의 이미지에서 일부 메시지가 보이지 않는데 전체 내용은 아래와 같다. 

<blockquote>
Missing template posts/new, application/new with {locale:[:en], formats:[:html], handlers:[:erb, :builder, :coffee]}. Searched in: * "/path/to/blog/app/views"
</blockquote>

에러 메시지가 텍스트로 되어 있다. 각 부분의 의미를 이해해 보도록 하자.

첫번째 부분은 누락된 템플릿이 무엇인지 알려 준다. 이 경우에는, `posts/new` 템플릿이 해당된다. 레일스는 맨 먼저 이 템플릿을 찾아보게 될 것이다. 없다면 `application/new` 템플릿을 로드하려고 시도할 것이다. 왜냐하면 `PostsController` 가 `ApplicationController` 로부터 상속을 받기 때문이다. 


The next part of the message contains a hash. The `:locale` key in this hash simply indicates what spoken language template should be retrieved. By default, this is the English — or "en" — template. The next key, `:formats` specifies the format of template to be served in response. The default format is `:html`, and so Rails is looking for an HTML template. The final key, `:handlers`, is telling us what _template handlers_ could be used to render our template. `:erb` is most commonly used for HTML templates, `:builder` is used for XML templates, and `:coffee` uses CoffeeScript to build JavaScript templates.

The final part of this message tells us where Rails has looked for the templates. Templates within a basic Rails application like this are kept in a single location, but in more complex applications it could be many different paths.

The simplest template that would work in this case would be one located at `app/views/posts/new.html.erb`. The extension of this file name is key: the first extension is the _format_ of the template, and the second extension is the _handler_ that will be used. Rails is attempting to find a template called `posts/new` within `app/views` for the application. The format for this template can only be `html` and the handler must be one of `erb`, `builder` or `coffee`. Because you want to create a new HTML form, you will be using the `ERB` language. Therefore the file should be called `posts/new.html.erb` and needs to be located inside the `app/views` directory of the application.

Go ahead now and create a new file at `app/views/posts/new.html.erb` and write this content in it:

```html
<h1>New Post</h1>
```

When you refresh <http://localhost:3000/posts/new> you'll now see that the page has a title. The route, controller, action and view are now working harmoniously! It's time to create the form for a new post.

### The first form

To create a form within this template, you will use a <em>form
builder</em>. The primary form builder for Rails is provided by a helper
method called `form_for`. To use this method, add this code into `app/views/posts/new.html.erb`:

```html+erb
<%= form_for :post do |f| %>
  <p>
    <%= f.label :title %><br>
    <%= f.text_field :title %>
  </p>

  <p>
    <%= f.label :text %><br>
    <%= f.text_area :text %>
  </p>

  <p>
    <%= f.submit %>
  </p>
<% end %>
```

If you refresh the page now, you'll see the exact same form as in the example. Building forms in Rails is really just that easy!

When you call `form_for`, you pass it an identifying object for this
form. In this case, it's the symbol `:post`. This tells the `form_for`
helper what this form is for. Inside the block for this method, the
`FormBuilder` object — represented by `f` — is used to build two labels and two text fields, one each for the title and text of a post. Finally, a call to `submit` on the `f` object will create a submit button for the form.

There's one problem with this form though. If you inspect the HTML that is generated, by viewing the source of the page, you will see that the `action` attribute for the form is pointing at `/posts/new`. This is a problem because this route goes to the very page that you're on right at the moment, and that route should only be used to display the form for a new post.

The form needs to use a different URL in order to go somewhere else.
This can be done quite simply with the `:url` option of `form_for`.
Typically in Rails, the action that is used for new form submissions
like this is called "create", and so the form should be pointed to that action.

Edit the `form_for` line inside `app/views/posts/new.html.erb` to look like this:

```html+erb
<%= form_for :post, url: { action: :create } do |f| %>
```

In this example, a `Hash` object is passed to the `:url` option. What Rails will do with this is that it will point the form to the `create` action of the current controller, the `PostsController`, and will send a `POST` request to that route. For this to work, you will need to add a route to `config/routes.rb`, right underneath the one for "posts/new":

```ruby
post "posts" => "posts#create"
```

By using the `post` method rather than the `get` method, Rails will define a route that will only respond to POST methods. The POST method is the typical method used by forms all over the web.

With the form and its associated route defined, you will be able to fill in the form and then click the submit button to begin the process of creating a new post, so go ahead and do that. When you submit the form, you should see a familiar error:

![Unknown action create for PostsController](images/getting_started/unknown_action_create_for_posts.png)

You now need to create the `create` action within the `PostsController` for this to work.

### Creating posts

To make the "Unknown action" go away, you can define a `create` action within the `PostsController` class in `app/controllers/posts_controller.rb`, underneath the `new` action:

```ruby
class PostsController < ApplicationController
  def new
  end

  def create
  end
end
```

If you re-submit the form now, you'll see another familiar error: a template is missing. That's ok, we can ignore that for now. What the `create` action should be doing is saving our new post to a database.

When a form is submitted, the fields of the form are sent to Rails as _parameters_. These parameters can then be referenced inside the controller actions, typically to perform a particular task. To see what these parameters look like, change the `create` action to this:

```ruby
def create
  render text: params[:post].inspect
end
```

The `render` method here is taking a very simple hash with a key of `text` and value of `params[:post].inspect`. The `params` method is the object which represents the parameters (or fields) coming in from the form. The `params` method returns a `HashWithIndifferentAccess` object, which allows you to access the keys of the hash using either strings or symbols. In this situation, the only parameters that matter are the ones from the form.

If you re-submit the form one more time you'll now no longer get the missing template error. Instead, you'll see something that looks like the following:

```ruby
{"title"=>"First post!", "text"=>"This is my first post."}
```

This action is now displaying the parameters for the post that are coming in from the form. However, this isn't really all that helpful. Yes, you can see the parameters but nothing in particular is being done with them.

### Creating the Post model

Models in Rails use a singular name, and their corresponding database tables use
a plural name. Rails provides a generator for creating models, which
most Rails developers tend to use when creating new models.
To create the new model, run this command in your terminal:

```bash
$ rails generate model Post title:string text:text
```

With that command we told Rails that we want a `Post` model, together
with a _title_ attribute of type string, and a _text_ attribute
of type text. Those attributes are automatically added to the `posts`
table in the database and mapped to the `Post` model.

Rails responded by creating a bunch of files. For
now, we're only interested in `app/models/post.rb` and
`db/migrate/20120419084633_create_posts.rb` (your name could be a bit
different). The latter is responsible
for creating the database structure, which is what we'll look at next.

TIP: Active Record is smart enough to automatically map column names to
model attributes, which means you don't have to declare attributes
inside Rails models, as that will be done automatically by Active
Record.

### Running a Migration

As we've just seen, `rails generate model` created a _database
migration_ file inside the `db/migrate` directory.
Migrations are Ruby classes that are designed to make it simple to
create and modify database tables. Rails uses rake commands to run migrations,
and it's possible to undo a migration after it's been applied to your database.
Migration filenames include a timestamp to ensure that they're processed in the
order that they were created.

If you look in the `db/migrate/20120419084633_create_posts.rb` file (remember,
yours will have a slightly different name), here's what you'll find:

```ruby
class CreatePosts < ActiveRecord::Migration
  def change
    create_table :posts do |t|
      t.string :title
      t.text :text

      t.timestamps
    end
  end
end
```

The above migration creates a method named `change` which will be called when you
run this migration. The action defined in this method is also reversible, which
means Rails knows how to reverse the change made by this migration, in case you
want to reverse it later. When you run this migration it will create a
`posts` table with one string column and a text column. It also creates two
timestamp fields to allow Rails to track post creation and update times.

TIP: For more information about migrations, refer to [Rails Database
Migrations](migrations.html).

At this point, you can use a rake command to run the migration:

```bash
$ rake db:migrate
```

Rails will execute this migration command and tell you it created the Posts
table.

```bash
==  CreatePosts: migrating ====================================================
-- create_table(:posts)
   -> 0.0019s
==  CreatePosts: migrated (0.0020s) ===========================================
```

NOTE. Because you're working in the development environment by default, this
command will apply to the database defined in the `development` section of your
`config/database.yml` file. If you would like to execute migrations in another
environment, for instance in production, you must explicitly pass it when
invoking the command: `rake db:migrate RAILS_ENV=production`.

### Saving data in the controller

Back in `posts_controller`, we need to change the `create` action
to use the new `Post` model to save the data in the database. Open that file
and change the `create` action to look like this:

```ruby
def create
  @post = Post.new(params[:post])

  @post.save
  redirect_to action: :show, id: @post.id
end
```

Here's what's going on: every Rails model can be initialized with its
respective attributes, which are automatically mapped to the respective
database columns. In the first line we do just that (remember that
`params[:post]` contains the attributes we're interested in). Then,
`@post.save` is responsible for saving the model in the database.
Finally, we redirect the user to the `show` action,
which we'll define later.

TIP: As we'll see later, `@post.save` returns a boolean indicating
whether the model was saved or not.

### Showing Posts

If you submit the form again now, Rails will complain about not finding
the `show` action. That's not very useful though, so let's add the
`show` action before proceeding. Open `config/routes.rb` and add the following route:

```ruby
get "posts/:id" => "posts#show"
```

The special syntax `:id` tells rails that this route expects an `:id`
parameter, which in our case will be the id of the post. Note that this
time we had to specify the actual mapping, `posts#show` because
otherwise Rails would not know which action to render.

As we did before, we need to add the `show` action in the
`posts_controller` and its respective view.

```ruby
def show
  @post = Post.find(params[:id])
end
```

A couple of things to note. We use `Post.find` to find the post we're
interested in. We also use an instance variable (prefixed by `@`) to
hold a reference to the post object. We do this because Rails will pass all instance
variables to the view.

Now, create a new file `app/view/posts/show.html.erb` with the following
content:

```html+erb
<p>
  <strong>Title:</strong>
  <%= @post.title %>
</p>

<p>
  <strong>Text:</strong>
  <%= @post.text %>
</p>
```

Finally, if you now go to
<http://localhost:3000/posts/new> you'll
be able to create a post. Try it!

![Show action for posts](images/getting_started/show_action_for_posts.png)

### Listing all posts

We still need a way to list all our posts, so let's do that. As usual,
we'll need a route placed into `config/routes.rb`:

```ruby
get "posts" => "posts#index"
```

And an action for that route inside the `PostsController` in the `app/controllers/posts_controller.rb` file:

```ruby
def index
  @posts = Post.all
end
```

And then finally a view for this action, located at `app/views/posts/index.html.erb`:

```html+erb
<h1>Listing posts</h1>

<table>
  <tr>
    <th>Title</th>
    <th>Text</th>
  </tr>

  <% @posts.each do |post| %>
    <tr>
      <td><%= post.title %></td>
      <td><%= post.text %></td>
    </tr>
  <% end %>
</table>
```

Now if you go to `http://localhost:3000/posts` you will see a list of all the posts that you have created.

### Adding links

You can now create, show, and list posts. Now let's add some links to
navigate through pages.

Open `app/views/welcome/index.html.erb` and modify it as follows:

```html+erb
<h1>Hello, Rails!</h1>
<%= link_to "My Blog", controller: "posts" %>
```

The `link_to` method is one of Rails' built-in view helpers. It creates a
hyperlink based on text to display and where to go - in this case, to the path
for posts.

Let's add links to the other views as well, starting with adding this "New Post" link to `app/views/posts/index.html.erb`, placing it above the `<table>` tag:

```erb
<%= link_to 'New post', action: :new %>
```

This link will allow you to bring up the form that lets you create a new post. You should also add a link to this template — `app/views/posts/new.html.erb` — to go back to the `index` action. Do this by adding this underneath the form in this template:

```erb
<%= form_for :post do |f| %>
  ...
<% end %>

<%= link_to 'Back', action: :index %>
```

Finally, add another link to the `app/views/posts/show.html.erb` template to go back to the `index` action as well, so that people who are viewing a single post can go back and view the whole list again:

```html+erb
<p>
  <strong>Title:</strong>
  <%= @post.title %>
</p>

<p>
  <strong>Text:</strong>
  <%= @post.text %>
</p>

<%= link_to 'Back', action: :index %>
```

TIP: If you want to link to an action in the same controller, you don't
need to specify the `:controller` option, as Rails will use the current
controller by default.

TIP: In development mode (which is what you're working in by default), Rails
reloads your application with every browser request, so there's no need to stop
and restart the web server when a change is made.

### Allowing the update of fields

The model file, `app/models/post.rb` is about as simple as it can get:

```ruby
class Post < ActiveRecord::Base
end
```

There isn't much to this file - but note that the `Post` class inherits from
`ActiveRecord::Base`. Active Record supplies a great deal of functionality to
your Rails models for free, including basic database CRUD (Create, Read, Update,
Destroy) operations, data validation, as well as sophisticated search support
and the ability to relate multiple models to one another.

Rails includes methods to help you secure some of your model fields.
Open the `app/models/post.rb` file and edit it:

```ruby
class Post < ActiveRecord::Base
  attr_accessible :text, :title
end
```

This change will ensure that all changes made through HTML forms can edit the content of the text and title fields.
It will not be possible to define any other field value through forms. You can still define them by calling the `field=` method of course.
Accessible attributes and the mass assignment problem is covered in details in the [Security guide](security.html#mass-assignment)

### Adding Some Validation

Rails includes methods to help you validate the data that you send to models.
Open the `app/models/post.rb` file and edit it:

```ruby
class Post < ActiveRecord::Base
  attr_accessible :text, :title

  validates :title, presence: true,
                    length: { minimum: 5 }
end
```

These changes will ensure that all posts have a title that is at least five characters long.
Rails can validate a variety of conditions in a model, including the presence or uniqueness of columns, their
format, and the existence of associated objects. Validations are covered in detail
in [Active Record Validations and Callbacks](active_record_validations_callbacks.html#validations-overview)

With the validation now in place, when you call `@post.save` on an invalid
post, it will return `false`. If you open `app/controllers/posts_controller.rb`
again, you'll notice that we don't check the result of calling `@post.save`
inside the `create` action. If `@post.save` fails in this situation, we need to
show the form back to the user. To do this, change the `new` and `create`
actions inside `app/controllers/posts_controller.rb` to these:

```ruby
def new
  @post = Post.new
end

def create
  @post = Post.new(params[:post])

  if @post.save
    redirect_to action: :show, id: @post.id
  else
    render 'new'
  end
end
```

The `new` action is now creating a new instance variable called `@post`, and
you'll see why that is in just a few moments.

Notice that inside the `create` action we use `render` instead of `redirect_to` when `save`
returns `false`. The `render` method is used so that the `@post` object is passed back to the `new` template when it is rendered. This rendering is done within the same request as the form submission, whereas the `redirect_to` will tell the browser to issue another request.

If you reload
<http://localhost:3000/posts/new> and
try to save a post without a title, Rails will send you back to the
form, but that's not very useful. You need to tell the user that
something went wrong. To do that, you'll modify
`app/views/posts/new.html.erb` to check for error messages:

```html+erb
<%= form_for :post, url: { action: :create } do |f| %>
  <% if @post.errors.any? %>
  <div id="errorExplanation">
    <h2><%= pluralize(@post.errors.count, "error") %> prohibited
      this post from being saved:</h2>
    <ul>
    <% @post.errors.full_messages.each do |msg| %>
      <li><%= msg %></li>
    <% end %>
    </ul>
  </div>
  <% end %>
  <p>
    <%= f.label :title %><br>
    <%= f.text_field :title %>
  </p>

  <p>
    <%= f.label :text %><br>
    <%= f.text_area :text %>
  </p>

  <p>
    <%= f.submit %>
  </p>
<% end %>

<%= link_to 'Back', action: :index %>
```

A few things are going on. We check if there are any errors with
`@post.errors.any?`, and in that case we show a list of all
errors with `@post.errors.full_messages`.

`pluralize` is a rails helper that takes a number and a string as its
arguments. If the number is greater than one, the string will be automatically pluralized.

The reason why we added `@post = Post.new` in `posts_controller` is that
otherwise `@post` would be `nil` in our view, and calling
`@post.errors.any?` would throw an error.

TIP: Rails automatically wraps fields that contain an error with a div
with class `field_with_errors`. You can define a css rule to make them
standout.

Now you'll get a nice error message when saving a post without title when you
attempt to do just that on the new post form [(http://localhost:3000/posts/new)](http://localhost:3000/posts/new).

![Form With Errors](images/getting_started/form_with_errors.png)

### Updating Posts

We've covered the "CR" part of CRUD. Now let's focus on the "U" part, updating posts.

The first step we'll take is adding an `edit` action to `posts_controller`.

Start by adding a route to `config/routes.rb`:

```ruby
get "posts/:id/edit" => "posts#edit"
```

And then add the controller action:

```ruby
def edit
  @post = Post.find(params[:id])
end
```

The view will contain a form similar to the one we used when creating
new posts. Create a file called `app/views/posts/edit.html.erb` and make
it look as follows:

```html+erb
<h1>Editing post</h1>

<%= form_for :post, url: { action: :update, id: @post.id },
method: :put do |f| %>
  <% if @post.errors.any? %>
  <div id="errorExplanation">
    <h2><%= pluralize(@post.errors.count, "error") %> prohibited
      this post from being saved:</h2>
    <ul>
    <% @post.errors.full_messages.each do |msg| %>
      <li><%= msg %></li>
    <% end %>
    </ul>
  </div>
  <% end %>
  <p>
    <%= f.label :title %><br>
    <%= f.text_field :title %>
  </p>

  <p>
    <%= f.label :text %><br>
    <%= f.text_area :text %>
  </p>

  <p>
    <%= f.submit %>
  </p>
<% end %>

<%= link_to 'Back', action: :index %>
```

This time we point the form to the `update` action, which is not defined yet
but will be very soon.

The `method: :put` option tells Rails that we want this form to be
submitted via the `PUT` HTTP method which is the HTTP method you're expected to use to
**update** resources according to the REST protocol.

TIP: By default forms built with the _form_for_ helper are sent via `POST`.

Next, we need to add the `update` action. The file
`config/routes.rb` will need just one more line:

```ruby
put "posts/:id" => "posts#update"
```

And then create the `update` action in `app/controllers/posts_controller.rb`:

```ruby
def update
  @post = Post.find(params[:id])

  if @post.update_attributes(params[:post])
    redirect_to action: :show, id: @post.id
  else
    render 'edit'
  end
end
```

The new method, `update_attributes`, is used when you want to update a record
that already exists, and it accepts a hash containing the attributes
that you want to update. As before, if there was an error updating the
post we want to show the form back to the user.

TIP: You don't need to pass all attributes to `update_attributes`. For
example, if you'd call `@post.update_attributes(title: 'A new title')`
Rails would only update the `title` attribute, leaving all other
attributes untouched.

Finally, we want to show a link to the `edit` action in the list of all the
posts, so let's add that now to `app/views/posts/index.html.erb` to make it
appear next to the "Show" link:

```html+erb
<table>
  <tr>
    <th>Title</th>
    <th>Text</th>
    <th></th>
    <th></th>
  </tr>

<% @posts.each do |post| %>
  <tr>
    <td><%= post.title %></td>
    <td><%= post.text %></td>
    <td><%= link_to 'Show', action: :show, id: post.id %></td>
    <td><%= link_to 'Edit', action: :edit, id: post.id %></td>
  </tr>
<% end %>
</table>
```

And we'll also add one to the `app/views/posts/show.html.erb` template as well,
so that there's also an "Edit" link on a post's page. Add this at the bottom of
the template:

```html+erb
...

<%= link_to 'Back', action: :index %>
| <%= link_to 'Edit', action: :edit, id: @post.id %>
```

And here's how our app looks so far:

![Index action with edit link](images/getting_started/index_action_with_edit_link.png)

### Using partials to clean up duplication in views

`partials` are what Rails uses to remove duplication in views. Here's a
simple example:

```html+erb
# app/views/user/show.html.erb

<h1><%= @user.name %></h1>

<%= render 'user_details' %>

# app/views/user/_user_details.html.erb

<%= @user.location %>

<%= @user.about_me %>
```

The `users/show` template will automatically include the content of the
`users/_user_details` template. Note that partials are prefixed by an underscore,
as to not be confused with regular views. However, you don't include the
underscore when including them with the `helper` method.

TIP: You can read more about partials in the
[Layouts and Rendering in Rails](layouts_and_rendering.html) guide.

Our `edit` action looks very similar to the `new` action, in fact they
both share the same code for displaying the form. Let's clean them up by
using a partial.

Create a new file `app/views/posts/_form.html.erb` with the following
content:

```html+erb
<%= form_for @post do |f| %>
  <% if @post.errors.any? %>
  <div id="errorExplanation">
    <h2><%= pluralize(@post.errors.count, "error") %> prohibited
      this post from being saved:</h2>
    <ul>
    <% @post.errors.full_messages.each do |msg| %>
      <li><%= msg %></li>
    <% end %>
    </ul>
  </div>
  <% end %>
  <p>
    <%= f.label :title %><br>
    <%= f.text_field :title %>
  </p>

  <p>
    <%= f.label :text %><br>
    <%= f.text_area :text %>
  </p>

  <p>
    <%= f.submit %>
  </p>
<% end %>
```

Everything except for the `form_for` declaration remained the same.
How `form_for` can figure out the right `action` and `method` attributes
when building the form will be explained in just a moment. For now, let's update the
`app/views/posts/new.html.erb` view to use this new partial, rewriting it
completely:

```html+erb
<h1>New post</h1>

<%= render 'form' %>

<%= link_to 'Back', action: :index %>
```

Then do the same for the `app/views/posts/edit.html.erb` view:

```html+erb
<h1>Edit post</h1>

<%= render 'form' %>

<%= link_to 'Back', action: :index %>
```

Point your browser to <http://localhost:3000/posts/new> and
try creating a new post. Everything still works. Now try editing the
post and you'll receive the following error:

![Undefined method post_path](images/getting_started/undefined_method_post_path.png)

To understand this error, you need to understand how `form_for` works.
When you pass an object to `form_for` and you don't specify a `:url`
option, Rails will try to guess the `action` and `method` options by
checking if the passed object is a new record or not. Rails follows the
REST convention, so to create a new `Post` object it will look for a
route named `posts_path`, and to update a `Post` object it will look for
a route named `post_path` and pass the current object. Similarly, rails
knows that it should create new objects via POST and update them via
PUT.

If you run `rake routes` from the console you'll see that we already
have a `posts_path` route, which was created automatically by Rails when we
defined the route for the index action.
However, we don't have a `post_path` yet, which is the reason why we
received an error before. With your server running you can view your routes by visiting [localhost:3000/rails/info/routes](http://localhost:3000/rails/info/routes), or you can generate them from the command line by running `rake routes`:

```bash
$ rake routes

    posts GET  /posts(.:format)            posts#index
posts_new GET  /posts/new(.:format)        posts#new
          POST /posts(.:format)            posts#create
          GET  /posts/:id(.:format)        posts#show
          GET  /posts/:id/edit(.:format)   posts#edit
          PUT  /posts/:id(.:format)        posts#update
     root      /                           welcome#index
```

To fix this, open `config/routes.rb` and modify the `get "posts/:id"`
line like this:

```ruby
get "posts/:id" => "posts#show", as: :post
```

The `:as` option tells the `get` method that we want to make routing helpers
called `post_url` and `post_path` available to our application. These are
precisely the methods that the `form_for` needs when editing a post, and so now
you'll be able to update posts again.

NOTE: The `:as` option is available on the `post`, `put`, `delete` and `match`
routing methods also.

### Deleting Posts

We're now ready to cover the "D" part of CRUD, deleting posts from the
database. Following the REST convention, we're going to add a route for
deleting posts to `config/routes.rb`:

```ruby
delete "posts/:id" => "posts#destroy"
```

The `delete` routing method should be used for routes that destroy
resources. If this was left as a typical `get` route, it could be possible for
people to craft malicious URLs like this:

```html
<a href='http://example.com/posts/1/destroy'>look at this cat!</a>
```

We use the `delete` method for destroying resources, and this route is mapped to
the `destroy` action inside `app/controllers/posts_controller.rb`, which doesn't exist yet, but is
provided below:

```ruby
def destroy
  @post = Post.find(params[:id])
  @post.destroy

  redirect_to action: :index
end
```

You can call `destroy` on Active Record objects when you want to delete
them from the database. Note that we don't need to add a view for this
action since we're redirecting to the `index` action.

Finally, add a 'destroy' link to your `index` action template
(`app/views/posts/index.html.erb`) to wrap everything
together.

```html+erb
<h1>Listing Posts</h1>
<table>
  <tr>
    <th>Title</th>
    <th>Text</th>
    <th></th>
    <th></th>
    <th></th>
  </tr>

<% @posts.each do |post| %>
  <tr>
    <td><%= post.title %></td>
    <td><%= post.text %></td>
    <td><%= link_to 'Show', action: :show, id: post.id %></td>
    <td><%= link_to 'Edit', action: :edit, id: post.id %></td>
    <td><%= link_to 'Destroy', { action: :destroy, id: post.id }, method: :delete, data: { confirm: 'Are you sure?' } %></td>
  </tr>
<% end %>
</table>
```

Here we're using `link_to` in a different way. We wrap the
`:action` and `:id` attributes in a hash so that we can pass those two keys in
first as one argument, and then the final two keys as another argument. The `:method` and `:'data-confirm'`
options are used as HTML5 attributes so that when the link is clicked,
Rails will first show a confirm dialog to the user, and then submit the link with method `delete`.
This is done via the JavaScript file `jquery_ujs` which is automatically included
into your application's layout (`app/views/layouts/application.html.erb`) when you
generated the application. Without this file, the confirmation dialog box wouldn't appear.

![Confirm Dialog](images/getting_started/confirm_dialog.png)

Congratulations, you can now create, show, list, update and destroy
posts. In the next section will see how Rails can aid us when creating
REST applications, and how we can refactor our Blog app to take
advantage of it.

### Going Deeper into REST

We've now covered all the CRUD actions of a REST app. We did so by
declaring separate routes with the appropriate verbs into
`config/routes.rb`. Here's how that file looks so far:

```ruby
get "posts" => "posts#index"
get "posts/new"
post "posts" => "posts#create"
get "posts/:id" => "posts#show", as: :post
get "posts/:id/edit" => "posts#edit"
put "posts/:id" => "posts#update"
delete "posts/:id" => "posts#destroy"
```

That's a lot to type for covering a single **resource**. Fortunately,
Rails provides a `resources` method which can be used to declare a
standard REST resource. Here's how `config/routes.rb` looks after the
cleanup:

```ruby
Blog::Application.routes.draw do

  resources :posts

  root to: "welcome#index"
end
```

If you run `rake routes`, you'll see that all the routes that we
declared before are still available:

```bash
$ rake routes
    posts GET    /posts(.:format)          posts#index
          POST   /posts(.:format)          posts#create
 new_post GET    /posts/new(.:format)      posts#new
edit_post GET    /posts/:id/edit(.:format) posts#edit
     post GET    /posts/:id(.:format)      posts#show
          PUT    /posts/:id(.:format)      posts#update
          DELETE /posts/:id(.:format)      posts#destroy
     root        /                         welcome#index
```

Also, if you go through the motions of creating, updating and deleting
posts the app still works as before.

TIP: In general, Rails encourages the use of resources objects in place
of declaring routes manually. It was only done in this guide as a learning
exercise. For more information about routing, see
[Rails Routing from the Outside In](routing.html).

Adding a Second Model
---------------------

It's time to add a second model to the application. The second model will handle comments on
posts.

### Generating a Model

We're going to see the same generator that we used before when creating
the `Post` model. This time we'll create a `Comment` model to hold
reference of post comments. Run this command in your terminal:

```bash
$ rails generate model Comment commenter:string body:text post:references
```

This command will generate four files:

| File                                         | Purpose                                                                                                |
| -------------------------------------------- | ------------------------------------------------------------------------------------------------------ |
| db/migrate/20100207235629_create_comments.rb | Migration to create the comments table in your database (your name will include a different timestamp) |
| app/models/comment.rb                        | The Comment model                                                                                      |
| test/models/comment_test.rb                  | Testing harness for the comments model                                                                 |
| test/fixtures/comments.yml                   | Sample comments for use in testing                                                                     |

First, take a look at `comment.rb`:

```ruby
class Comment < ActiveRecord::Base
  belongs_to :post
  attr_accessible :body, :commenter
end
```

This is very similar to the `post.rb` model that you saw earlier. The difference
is the line `belongs_to :post`, which sets up an Active Record _association_.
You'll learn a little about associations in the next section of this guide.

In addition to the model, Rails has also made a migration to create the
corresponding database table:

```ruby
class CreateComments < ActiveRecord::Migration
  def change
    create_table :comments do |t|
      t.string :commenter
      t.text :body
      t.references :post

      t.timestamps
    end

    add_index :comments, :post_id
  end
end
```

The `t.references` line sets up a foreign key column for the association between
the two models. And the `add_index` line sets up an index for this association
column. Go ahead and run the migration:

```bash
$ rake db:migrate
```

Rails is smart enough to only execute the migrations that have not already been
run against the current database, so in this case you will just see:

```bash
==  CreateComments: migrating =================================================
-- create_table(:comments)
   -> 0.0008s
-- add_index(:comments, :post_id)
   -> 0.0003s
==  CreateComments: migrated (0.0012s) ========================================
```

### Associating Models

Active Record associations let you easily declare the relationship between two
models. In the case of comments and posts, you could write out the relationships
this way:

* Each comment belongs to one post.
* One post can have many comments.

In fact, this is very close to the syntax that Rails uses to declare this
association. You've already seen the line of code inside the Comment model that
makes each comment belong to a Post:

```ruby
class Comment < ActiveRecord::Base
  belongs_to :post
end
```

You'll need to edit the `post.rb` file to add the other side of the association:

```ruby
class Post < ActiveRecord::Base
  validates :title, presence: true,
                    length: { minimum: 5 }

  has_many :comments
end
```

These two declarations enable a good bit of automatic behavior. For example, if
you have an instance variable `@post` containing a post, you can retrieve all
the comments belonging to that post as an array using `@post.comments`.

TIP: For more information on Active Record associations, see the [Active Record
Associations](association_basics.html) guide.

### Adding a Route for Comments

As with the `welcome` controller, we will need to add a route so that Rails knows
where we would like to navigate to see `comments`. Open up the
`config/routes.rb` file again, and edit it as follows:

```ruby
resources :posts do
  resources :comments
end
```

This creates `comments` as a _nested resource_ within `posts`. This is another
part of capturing the hierarchical relationship that exists between posts and
comments.

TIP: For more information on routing, see the [Rails Routing](routing.html) guide.

### Generating a Controller

With the model in hand, you can turn your attention to creating a matching
controller. Again, we'll use the same generator we used before:

```bash
$ rails generate controller Comments
```

This creates six files and one empty directory:

| File/Directory                               | Purpose                                  |
| -------------------------------------------- | ---------------------------------------- |
| app/controllers/comments_controller.rb       | The Comments controller                  |
| app/views/comments/                          | Views of the controller are stored here  |
| test/controllers/comments_controller_test.rb | The test for the controller              |
| app/helpers/comments_helper.rb               | A view helper file                       |
| test/helpers/comments_helper_test.rb         | The test for the helper                  |
| app/assets/javascripts/comment.js.coffee     | CoffeeScript for the controller          |
| app/assets/stylesheets/comment.css.scss      | Cascading style sheet for the controller |

Like with any blog, our readers will create their comments directly after
reading the post, and once they have added their comment, will be sent back to
the post show page to see their comment now listed. Due to this, our
`CommentsController` is there to provide a method to create comments and delete
spam comments when they arrive.

So first, we'll wire up the Post show template
(`/app/views/posts/show.html.erb`) to let us make a new comment:

```html+erb
<p>
  <strong>Title:</strong>
  <%= @post.title %>
</p>

<p>
  <strong>Text:</strong>
  <%= @post.text %>
</p>

<h2>Add a comment:</h2>
<%= form_for([@post, @post.comments.build]) do |f| %>
  <p>
    <%= f.label :commenter %><br />
    <%= f.text_field :commenter %>
  </p>
  <p>
    <%= f.label :body %><br />
    <%= f.text_area :body %>
  </p>
  <p>
    <%= f.submit %>
  </p>
<% end %>

<%= link_to 'Edit Post', edit_post_path(@post) %> |
<%= link_to 'Back to Posts', posts_path %>
```

This adds a form on the `Post` show page that creates a new comment by
calling the `CommentsController` `create` action. The `form_for` call here uses
an array, which will build a nested route, such as `/posts/1/comments`.

Let's wire up the `create` in `app/controllers/comments_controller.rb`:

```ruby
class CommentsController < ApplicationController
  def create
    @post = Post.find(params[:post_id])
    @comment = @post.comments.create(params[:comment])
    redirect_to post_url(@post)
  end
end
```

You'll see a bit more complexity here than you did in the controller for posts.
That's a side-effect of the nesting that you've set up. Each request for a
comment has to keep track of the post to which the comment is attached, thus the
initial call to the `find` method of the `Post` model to get the post in question.

In addition, the code takes advantage of some of the methods available for an
association. We use the `create` method on `@post.comments` to create and save
the comment. This will automatically link the comment so that it belongs to that
particular post.

Once we have made the new comment, we send the user back to the original post
using the `post_path(@post)` helper. As we have already seen, this calls the
`show` action of the `PostsController` which in turn renders the `show.html.erb`
template. This is where we want the comment to show, so let's add that to the
`app/views/posts/show.html.erb`.

```html+erb
<p>
  <strong>Title:</strong>
  <%= @post.title %>
</p>

<p>
  <strong>Text:</strong>
  <%= @post.text %>
</p>

<h2>Comments</h2>
<% @post.comments.each do |comment| %>
  <p>
    <strong>Commenter:</strong>
    <%= comment.commenter %>
  </p>

  <p>
    <strong>Comment:</strong>
    <%= comment.body %>
  </p>
<% end %>

<h2>Add a comment:</h2>
<%= form_for([@post, @post.comments.build]) do |f| %>
  <p>
    <%= f.label :commenter %><br />
    <%= f.text_field :commenter %>
  </p>
  <p>
    <%= f.label :body %><br />
    <%= f.text_area :body %>
  </p>
  <p>
    <%= f.submit %>
  </p>
<% end %>

<%= link_to 'Edit Post', edit_post_path(@post) %> |
<%= link_to 'Back to Posts', posts_path %>
```

Now you can add posts and comments to your blog and have them show up in the
right places.

![Post with Comments](images/getting_started/post_with_comments.png)

Refactoring
-----------

Now that we have posts and comments working, take a look at the
`app/views/posts/show.html.erb` template. It is getting long and awkward. We can
use partials to clean it up.

### Rendering Partial Collections

First, we will make a comment partial to extract showing all the comments for the
post. Create the file `app/views/comments/_comment.html.erb` and put the
following into it:

```html+erb
<p>
  <strong>Commenter:</strong>
  <%= comment.commenter %>
</p>

<p>
  <strong>Comment:</strong>
  <%= comment.body %>
</p>
```

Then you can change `app/views/posts/show.html.erb` to look like the
following:

```html+erb
<p>
  <strong>Title:</strong>
  <%= @post.title %>
</p>

<p>
  <strong>Text:</strong>
  <%= @post.text %>
</p>

<h2>Comments</h2>
<%= render @post.comments %>

<h2>Add a comment:</h2>
<%= form_for([@post, @post.comments.build]) do |f| %>
  <p>
    <%= f.label :commenter %><br />
    <%= f.text_field :commenter %>
  </p>
  <p>
    <%= f.label :body %><br />
    <%= f.text_area :body %>
  </p>
  <p>
    <%= f.submit %>
  </p>
<% end %>

<%= link_to 'Edit Post', edit_post_path(@post) %> |
<%= link_to 'Back to Posts', posts_path %>
```

This will now render the partial in `app/views/comments/_comment.html.erb` once
for each comment that is in the `@post.comments` collection. As the `render`
method iterates over the `@post.comments` collection, it assigns each
comment to a local variable named the same as the partial, in this case
`comment` which is then available in the partial for us to show.

### Rendering a Partial Form

Let us also move that new comment section out to its own partial. Again, you
create a file `app/views/comments/_form.html.erb` containing:

```html+erb
<%= form_for([@post, @post.comments.build]) do |f| %>
  <p>
    <%= f.label :commenter %><br />
    <%= f.text_field :commenter %>
  </p>
  <p>
    <%= f.label :body %><br />
    <%= f.text_area :body %>
  </p>
  <p>
    <%= f.submit %>
  </p>
<% end %>
```

Then you make the `app/views/posts/show.html.erb` look like the following:

```html+erb
<p>
  <strong>Title:</strong>
  <%= @post.title %>
</p>

<p>
  <strong>Text:</strong>
  <%= @post.text %>
</p>

<h2>Add a comment:</h2>
<%= render "comments/form" %>

<%= link_to 'Edit Post', edit_post_path(@post) %> |
<%= link_to 'Back to Posts', posts_path %>
```

The second render just defines the partial template we want to render,
`comments/form`. Rails is smart enough to spot the forward slash in that
string and realize that you want to render the `_form.html.erb` file in
the `app/views/comments` directory.

The `@post` object is available to any partials rendered in the view because we
defined it as an instance variable.

Deleting Comments
-----------------

Another important feature of a blog is being able to delete spam comments. To do
this, we need to implement a link of some sort in the view and a `DELETE` action
in the `CommentsController`.

So first, let's add the delete link in the
`app/views/comments/_comment.html.erb` partial:

```html+erb
<p>
  <strong>Commenter:</strong>
  <%= comment.commenter %>
</p>

<p>
  <strong>Comment:</strong>
  <%= comment.body %>
</p>

<p>
  <%= link_to 'Destroy Comment', [comment.post, comment],
               method: :delete,
               data: { confirm: 'Are you sure?' } %>
</p>
```

Clicking this new "Destroy Comment" link will fire off a `DELETE
/posts/:post_id/comments/:id` to our `CommentsController`, which can then use
this to find the comment we want to delete, so let's add a destroy action to our
controller:

```ruby
class CommentsController < ApplicationController

  def create
    @post = Post.find(params[:post_id])
    @comment = @post.comments.create(params[:comment])
    redirect_to post_path(@post)
  end

  def destroy
    @post = Post.find(params[:post_id])
    @comment = @post.comments.find(params[:id])
    @comment.destroy
    redirect_to post_path(@post)
  end

end
```

The `destroy` action will find the post we are looking at, locate the comment
within the `@post.comments` collection, and then remove it from the
database and send us back to the show action for the post.


### Deleting Associated Objects

If you delete a post then its associated comments will also need to be deleted.
Otherwise they would simply occupy space in the database. Rails allows you to
use the `dependent` option of an association to achieve this. Modify the Post
model, `app/models/post.rb`, as follows:

```ruby
class Post < ActiveRecord::Base
  validates :title, presence: true,
                    length: { minimum: 5 }
  has_many :comments, dependent: :destroy
end
```

Security
--------

If you were to publish your blog online, anybody would be able to add, edit and
delete posts or delete comments.

Rails provides a very simple HTTP authentication system that will work nicely in
this situation.

In the `PostsController` we need to have a way to block access to the various
actions if the person is not authenticated, here we can use the Rails
`http_basic_authenticate_with` method, allowing access to the requested
action if that method allows it.

To use the authentication system, we specify it at the top of our
`PostsController`, in this case, we want the user to be authenticated on every
action, except for `index` and `show`, so we write that:

```ruby
class PostsController < ApplicationController

  http_basic_authenticate_with name: "dhh", password: "secret", except: [:index, :show]

  def index
    @posts = Post.all
  end

  # snipped for brevity
```

We also only want to allow authenticated users to delete comments, so in the
`CommentsController` we write:

```ruby
class CommentsController < ApplicationController

  http_basic_authenticate_with name: "dhh", password: "secret", only: :destroy

  def create
    @post = Post.find(params[:post_id])
    ...
  end
  # snipped for brevity
```

Now if you try to create a new post, you will be greeted with a basic HTTP
Authentication challenge

![Basic HTTP Authentication Challenge](images/challenge.png)

What's Next?
------------

Now that you've seen your first Rails application, you should feel free to
update it and experiment on your own. But you don't have to do everything
without help. As you need assistance getting up and running with Rails, feel
free to consult these support resources:

* The [Ruby on Rails guides](index.html)
* The [Ruby on Rails Tutorial](http://railstutorial.org/book)
* The [Ruby on Rails mailing list](http://groups.google.com/group/rubyonrails-talk)
* The [#rubyonrails](irc://irc.freenode.net/#rubyonrails) channel on irc.freenode.net

Rails also comes with built-in help that you can generate using the rake command-line utility:

* Running `rake doc:guides` will put a full copy of the Rails Guides in the `doc/guides` folder of your application. Open `doc/guides/index.html` in your web browser to explore the Guides.
* Running `rake doc:rails` will put a full copy of the API documentation for Rails in the `doc/api` folder of your application. Open `doc/api/index.html` in your web browser to explore the API documentation.

TIP: To be able to generate the Rails Guides locally with the `doc:guides` rake task you need to install the RedCloth gem. Add it to your `Gemfile` and run `bundle install` and you're ready to go.

Configuration Gotchas
---------------------

The easiest way to work with Rails is to store all external data as UTF-8. If
you don't, Ruby libraries and Rails will often be able to convert your native
data into UTF-8, but this doesn't always work reliably, so you're better off
ensuring that all external data is UTF-8.

If you have made a mistake in this area, the most common symptom is a black
diamond with a question mark inside appearing in the browser. Another common
symptom is characters like "Ã¼" appearing instead of "ü". Rails takes a number
of internal steps to mitigate common causes of these problems that can be
automatically detected and corrected. However, if you have external data that is
not stored as UTF-8, it can occasionally result in these kinds of issues that
cannot be automatically detected by Rails and corrected.

Two very common sources of data that are not UTF-8:

* Your text editor: Most text editors (such as Textmate), default to saving files as
  UTF-8. If your text editor does not, this can result in special characters that you
  enter in your templates (such as é) to appear as a diamond with a question mark inside
  in the browser. This also applies to your i18n translation files.
  Most editors that do not already default to UTF-8 (such as some versions of
  Dreamweaver) offer a way to change the default to UTF-8. Do so.
* Your database. Rails defaults to converting data from your database into UTF-8 at
  the boundary. However, if your database is not using UTF-8 internally, it may not
  be able to store all characters that your users enter. For instance, if your database
  is using Latin-1 internally, and your user enters a Russian, Hebrew, or Japanese
  character, the data will be lost forever once it enters the database. If possible,
  use UTF-8 as the internal storage of your database.
