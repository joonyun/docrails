액션 컨트롤러 개요
==========================

본 가이드에서는 컨트롤러의 동작방법과 어플레케이션에서 돌아가는 요청 주기 상에서 컨트롤러가 수행하는 역할에 대해서 알게 될 것입니다. 본 가이드를 읽은 후에는 아래와 같은 내용을 할 수 있을 것입니다. 

* 컨트롤러가 요청을 처리하는 과정을 추적할 수 있습니다.
* 데이터를 세션이나 쿠키로 저장하는 이유와 방법에 대해서 알게 됩니다.
* 요청을 처리하는 과정에서 필터를 이용한 코드 실행을 할 수 있게 됩니다.
* 액션 컨트롤러의 내장 HTTP 인증을 사용할 수 있게 됩니다.
* 유저의 브라우저로 데이터를 직접 스트리밍할 수 있게 됩니다.
* 민감한 파라메터들을 여과과정을 통해서 어플리케이션의 로그에 보이지 않게 할 수 있습니다.
* 요청 처리 중에 발생할 수 있는 예외를 처리할 수 있게 됩니다. 

--------------------------------------------------------------------------------

[What Does a Controller Do?] 컨트롤러는 어떤 일을 하는가?
--------------------------

액션 컨트롤러는 MVC 중에서 C에 해당합니다. 라우팅 작업을 통해 어떤 컨트롤러를 사용하여 요청을 처리할 것인가가 결정된 후, 해당 컨트롤러는 요청을 처리하여 적절한 결과를 만들게 됩니다. 다행스럽게도, 액션 컨트롤러가 기본적인 작업을 대신해 주며, 레일스의 규칙을 이용하여 이러한 작업을 가능한한 쉽게해 줍니다.  

대부분의 [RESTful](http://en.wikipedia.org/wiki/Representational_state_transfer) 방식의 어플리케이션에서는, 개발자 입장에서는 육안적으로 확인할 수는 없지만, 컨트롤러가 요청을 받게 되면, 모델로부터 데이터를 가져와 저장하고, 뷰를 이용해서 HTML 결과를 렌더링하게 됩니다. 컨트롤러의 작업내용을 변경할 때도 문제가 되지 않는데, 이러한 처리과정이 컨트롤러가 작동하는 가장 일반적인 방법이기 때문입니다.

컨트롤러는 모델과 뷰 사이에 있는 있는 중간자로서 생각할 수 있습니다. 모델 데이터를 뷰에서 사용할 수 있도록 하여, 유저들에게 해당 데이터를 보여줄 수 있고, 반대로 유저로부터 모델로 데이터를 저장하거나 갱신할 수도 있게 합니다.

NOTE: 라우팅 과정에 대한 상세한 내용은, [Rails Routing from the Outside In](routing.html)을 참조하기 바랍니다.

[Methods and Actions] 메소드와 액션
-------------------

컨트롤러는 `ApplicationController`로부터 상속받는 하나의 루비 클래스이며, 여느 다른 클래스처럼 메소드를 가집니다. 어플리케이션이 요청을 받게 되면, 라우팅으로부터 어느 컨트롤러와 액션이 실행될지가 결정되며, 이 때 레일스는 해당 컨트롤러의 인스턴스를 만들어 액션과 동일한 이름의 메소드를 실행하게 됩니다.

```ruby
class ClientsController < ApplicationController
  def new
  end
end
```

예를 들어, 사용자가 새로운 클라이언트를 추가하기 위해 어플리케이션에서 `/clients/new` 로 가고자 한다면, 레일스는 `ClientsController` 인스턴스를 만들어 `new` 메소드를 실행하게 될 것입니다. 위의 예에서 특별한 작업내용이 없는 메소드도, 액션이 달리 언급하는 내용이 없는한 레일스가 기본적으로 `new.html.erb` 뷰를 렌더링할 것이기 때문에, 훌륭하게 작동할 수 있는 것입니다. `new` 메소드는 새 `Client` 를 만들어서 `@client` 인스턴스 변수가 뷰에서 사용가능하도록 할 수 있습니다

```ruby
def new
  @client = Client.new
end
```

[Layouts & Rendering Guide](layouts_and_rendering.html)는 이러한 부분을 좀 더 자세하게 설명을 해 줍니다.

`ApplicationController`는, 도움이 될만한 많은 메소드를 가지고 있는 `ActionController::Base`로부터 상속을 받습니다. 본 가이드는 이들 중 일부를 다루게 될 것이지만, 그 내용을 알고 싶으면 API 문서나 소스를 보시면 되겠습니다.

단지 public 메소드만 액션으로 호출할 수 있습니다. 액션으로 사용할 것이 아니라면, 보조 메소드나 필터와 같이 메소드의 가시성을 낮추는 것이 최선의 작업방법인 것입니다.

[Parameters]매개변수
----------

아마도 유저가 보낸 데이터에 접근하거나, 컨트롤러의 액션에서 다른 매개변수에 접근하고자 할 경우가 있습니다. 웹어플리케이션에는 두가지 종류의 매개변수를 사용할 수 있습니다. 첫번째는 URL의 일부분으로 소위 쿼리문자열 매개변수입니다. 쿼리문자열은 URL에서 "?" 문자 다음에 오는 모든 것을 말합니다. 두번째는 대개 POST 데이터라고 하는 것입니다. 대개 이것은 사용자들이 데이터를 입력하는 HTML 폼으로부터 오게 됩니다. 이것은 단지 HTTP POST 요청의 일부분으로만 보내지기 때문에 POST 데이터라고 합니다. 레일스는 이 두가지 매개변수를 구분하지 않으며, 모두 컨트롤러 상에서 `params` 해시로 접근할 수 있습니다. 

```ruby
class ClientsController < ActionController::Base
  # 이 액션은, HTTP GET 요청으로 실행되기 때문에, 쿼리문자열 매개변수를
  # 이용합니다만, 매개변수를 접근하는 방식에는 별 차이가 없습니다. 
  # activated 상태의 clients 목록을 보여주는 이 액션에 대한 URL은
  # /clients?status=activated 와 같습니다. 
  def index
    if params[:status] == "activated"
      @clients = Client.activated
    else
      @clients = Client.inactivated
    end
  end

  # 이 액션은 POST 데이터 파라메터를 이용합니다. 이 파라메터는 거의 대부분 유저가
  # 작성한 HTML 폼으로부터 오게 됩니다. REST방식의 요청에 대한 URL은 "/clients"가
  # 될 것이고, 데이터는 요청내용의 일부분으로 보내지게 될 것입니다.
  def create
    @client = Client.new(params[:client])
    if @client.save
      redirect_to @client
    else
      # 이 코드라인은 "create" 뷰를 렌더링했어야 할 디폴트 작업을 
      # "new" 액션 뷰템플릿으로 렌더링되도록 변경합니다.
      render "new"
    end
  end
end
```

### [Hash and Array Parameters]해시와 배열 매개변수

`params` 해쉬는 일차원 키-값 구조에 국한되지 않습니다. 배열과 중첩 해쉬를 포함할 수 있습니다. 값을 배열형태로 보내기 위해서는 키 이름에 "[]"를 붙이면 됩니다

```
GET /clients?ids[]=1&ids[]=2&ids[]=3
```

NOTE: URL에서는 "[" 와 "]" 문자를 사용할 수 없기 때문에, 이 예에서 실제 URL은 "/clients?ids%5b%5d=1&ids%5b%5d=2&ids%5b%5d=3"와 같이 인코딩될 것입니다. 대개의 경우 브라우저가 이러한 문제를 해결해 주기 때문에 걱정할 필요가 없고 레일스는 이렇게 인코딩된 URL로 요청을 받게 될 때 알아서 디코딩하게 될 것입니다. 그러나 직접 서버로 인코딩된 URL을 보내야할 경우에는 이러한 점을 염두어 두어야 합니다.

따라서 결과적으로 `params[:ids]` 값은 `["1", "2", "3"]`이 될 것입니다. 주의할 것은 매개변수 값은 항상 문자열이라는 것이며, 레일스는 이 값의 데이터형을 추측하거나 형변환을 하지 않는다는 것입니다.

해시 형태로 보내고자 한다면 브래킷([]) 안에 키 이름을 포함해 주어야 합니다.

```html
<form accept-charset="UTF-8" action="/clients" method="post">
  <input type="text" name="client[name]" value="Acme" />
  <input type="text" name="client[phone]" value="12345" />
  <input type="text" name="client[address][postcode]" value="12345" />
  <input type="text" name="client[address][city]" value="Carrot City" />
</form>
```

이 폼을 제출(submit)하면, `params[:client]` 값은 `{"name" => "Acme", "phone" => "12345", "address" => {"postcode" => "12345", "city" => "Carrot City"}}`이 될 것입니다. 여기서 `params[:client][:address]`와 같은 중첩 해시가 사용된 것을 유의하기 바랍니다.

주목할 것은 `params` 해시는 실제로 Active Support 모듈의 `HashWithIndifferentAccess` 클래스의 인스턴스이며, 키를 심볼이나 문자열 형태로 사용할 수 있게해 주는 해시와 같이 동작한다는 것입니다.

### [JSON/XML parameters]JSON/XML 매개변수

웹(서비스) 어플리케이션을 작성할 때 파라메터를 JSON이나 XML형태로 받는 것이 더 편리할 수도 있습니다. 레일스는 파라메터를 `params` 해쉬로 자동 변환해 주게 되며, 이것은 통상적으로 폼 데이터로 작업하는 것같이 접근하면 될 것입니다.

그래서 예를 들면, 아래의 JSON 매개변수를 보내게 되면:

```json
{ "company": { "name": "acme", "address": "123 Carrot Street" } }
```

`params[:company]` 값은 `{ :name => "acme", "address" => "123 Carrot Street" }`와 같이 될 것입니다.

또한, 레일스의 initializer 에 `config.wrap_parameters` 를 "on" 상태로 설정하거나 컨트롤러 내에서 `wrap_parameters` 메소스를 호출하게 되면, JSON/XML 파라메터에서 root 엘리먼트를 생략해도 문제가 없을 것입니다. 이렇게 되면 디폴트로 컨트롤러의 이름에 해당하는 키 이름으로 파라메터들이 복제되어 할당될 것입니다. 따라서 위의 파라메터는 다음과 같이 작성될 수 있습니다.

```json
{ "name": "acme", "address": "123 Carrot Street" }
```

그리고 `CompaniesController` 컨트롤러로 이 데이터를 보낸다고 가정한다면, 다음과 같이 `:company` 키로 보내지게 될 것입니다.

```ruby
{ :name => "acme", :address => "123 Carrot Street", :company => { :name => "acme", :address => "123 Carrot Street" }}
```

[API documentation](http://api.rubyonrails.org/classes/ActionController/ParamsWrapper.html)
를 참조하게 되면 파라메터로 할당받아 오게 될 키 이름이나 특수한 형태의 매개변수로 변경할 수 있습니다.

### [Routing Parameters]라우팅 파라메터

`params` 해쉬에는 반드시 `:controller` 와 `:action` 키가 포함되어 있지만, 이 값들을 직접 접근하는 대신에, `controller_name` 과 `action_name` 메소드를 사용하여야 합니다. 라우팅이 정의하는 매개변수 중에는 `:id` 키가 이용가능할 것입니다. 예를 들어, 클라이언트의 활성화 상태를 active / inactive 로 보여주는 클라이언트 목록을 가정해 보겠습니다. `config/routes.rb` 파일에 라우트 하나를 추가해서 URL주소에서 `:status` 매개변수를 가져올 수 있게 해 줍니다.

```ruby
match '/clients/:status' => 'clients#index', foo: "bar"
```

이 예에서, 사용자가 `/clients/active` 주소를 열게 되면, `params[:status]` 는 active 값으로 설정될 것입니다. 위의 라우트를 사용하면, `params[:foo]` 도 마치 쿼리문자열로 넘겨진 것 같이 bar값으로 할당될 것입니다. 같은 식으로 params[:action]` 은 index값을 포함하게 될 것입니다.

### [default_url_options] `default_url_options`

컨트롤내에 `default_url_options`라는 이름의 메소드를 정의하면 URL을 생성할 때 사용할 수 있는 전역 디폴트 파라메터를 지정할 수 있습니다. 이 메소드는 원하는 디폴트들이 들어 있는 해시를 반환해야 하며, 이 때 키는 반드시 심볼형으로 사용해야 합니다.

```ruby
class ApplicationController < ActionController::Base
  def default_url_options
    { locale: I18n.locale }
  end
end
```
이 옵션은 URL을 생성할 때 시작점으로 사용되어 `url_for` 호출시에 넘겨주는 옵션들에 의해 변경될 수 있습니다. 

위의 예에서와 같이, `ApplicationController` 내에 `default_url_options`을 정의해 두면, 어플리케이션내 모든 URL 생성시에 적용될 것입니다. 또한 이 메소드를 특정 컨트롤러내에 정의해 둔다면, 해당 컨트롤러내에서 생성되는 URL에 대해서만 적용됩니다.


[Session]세션
-------

어플리케이션에서는 사용자당 하나의 세션을 가질 수 있으며, 여기에는 요청시 마다 유지될 수 있는 소량의 데이터를 저장할 수 있습니다. 이러한 세션은 컨트롤러에서만 사용할 수 있고 여러가지 저장 메카니즘 중에 하나를 이용할 수 있습니다.

* ActionDispatch::Session::CookieStore - 클라이언트에 모든 것을 저장합니다.
* ActionDispatch::Session::CacheStore - 레일스 캐쉬에 데이터를 저장합니다.
* ActionDispatch::Session::ActiveRecordStore - 액티브레코드를 이용하여 데이터베이스에 데이터를 저장합니다. (이 때는 `activerecord-session_store` 젬이 필요함).
* ActionDispatch::Session::MemCacheStore - memcached 클러스터에 데이터를 저장합니다. (이것은 오래된 구현방법이라서 대신에 CacheStore를 사용하기 바랍니다.)

세션 저장소로 항상 쿠키를 사용하며 여기에 각 세션에 해당하는 고유 ID값을 저장하게 됩니다(쿠키를 사용해야 합니다, 레일스에서는, 보안이 더 약하기 때문에, URL에 세션 ID값을 넘기지 못하게 되어 있습니다).

대부분의 저장소에 대해서 이 세션 ID값을 이용해서 서버(예를 들면, 데이터베이스 테이블)에 있는 세션 데이터를 조회하기 위해 사용하게 됩니다. 한가지 예외 상황이 있는데, 그것은 기본적으로 추천되는 세션 저장소인 CookieStore로서, 모든 세션 데이터를 쿠키 자체에 저장하게 됩니다(세션 ID값은 필요시 언제든지 사용가능합니다). 이것은 용량이 매우 작다는 장점이 있고 세션을 사용하기 위해 새로운 어플리케이션에서 별도의 설치과정이 필요없습니다. 쿠키 데이터는 암호화되어 있어 변조를 방지할 수 있지만, 코드화가 되지 않아서 아무나 접근해서 내용을 읽을 수 있지만 수정할 수는 없습니다(만약 수정이 될 경우 레일스는 해당 쿠키를 승인하지 않게 될 것입니다).

CookieStore는 다른 것에 비해 — 훨씬 용량이 적어 — 대략 4kB 정도의 데이터를 저장할 수 있지만, 이 정도면 대부분의 경우에 충분한 용량입니다. 어떠한 종류의 세션 저장소를 사용하더라도 세션에 대용량의 데이터를 저장하는 것은 권장하고 있지 않습니다. 특히 세션에 복잡한 객체(기본 루비 객체 이외의 다른 것, 가장 흔한 예로, 모델 인스턴스)를 저장하는 것을 피해야 하는데, 서버가 요청 사이에 세션값을 재조합할 수 없어 에러를 발생할 것이기 때문입니다.

사용자 세션이 중요한 데이터를 저장하지 않거나 오랜 기간동안(예를 들어, 메시지를 보내기 위해 flash를 사용할 경우와 같이) 유지되어야 할 필요가 없는 경우 ActionDispatch::Session::CacheStore를 사용할 것을 고려할 수 있습니다. 이것은 어플리케이션에 대해서 설정해 놓은 캐시를 이용해서 세션정보를 저장하게 될 것입니다. 이것의 장점은 별도의 설치과정이나 관리가 필요없이 세션을 저장하기 위해 기존의 캐시 구조를 바로 사용할 수 있다는 것입니다. 물론, 단점은 세션값들의 수명이 짧아서 언제라도 사라질 수 있다는 것입니다.

[Security Guide](security.html) 에서 세션 저장에 대한 더 많은 내용을 읽어 보기 바랍니다.

다른 종류의 세션 저장 메카니즘을 사용하고자 한다면 `config/initializers/session_store.rb` 파일에서 세션 저장소를 변경할 수 있습니다.

```ruby
# 기본 세션 저장소인 쿠키 대신에 데이터베이스를 사용할 경우, 
# 매우 중요한 정보를 저장하지 않도록 합니다.
# ("script/rails g session_migration"을 실행해서 세션 테이블을 생성해야 합니다)
# YourApp::Application.config.session_store :active_record_store
```

세션 데이터를 보낼때 레일스는 세션 키(쿠키 이름)를 정하게 됩니다. 이것은 `config/initializers/session_store.rb` 파일에서 변경할 수 있습니다.

```ruby
# 이 파일의 내용을 변경하게 될 때 반드시 서버를 재시동해야 합니다.
YourApp::Application.config.session_store :cookie_store, key: '_your_app_session'
```

또한 `:domain` 키를 넘겨 해당 쿠키에 대해서 도메일 이름을 명시할 수도 있습니다

```ruby
# 이 파일의 내용을 변경하게 될 때 반드시 서버를 재시동해야 합니다.
YourApp::Application.config.session_store :cookie_store, key: '_your_app_session', domain: ".example.com"
```
레일스는 (CookieStore에 대해서) 세션 데이터에 표식하기 위해 사용할 보안키를 정하게 됩니다. 이것은 `config/initializers/secret_token.rb` 파일에서 변경할 수 있습니다.

```ruby
# 이 파일의 내용을 변경하게 될 때 반드시 서버를 재시동해야 합니다.

# 보내진 쿠키의 유효성 검증을 위한 보안키.
# 이 보안키를 변경하게 되면, 모든 기존의 쿠키들은 무효화 될 것입니다!
# 최소한 30개의 무작위 문자값이어야 하고 일반적인 단어를 포함해서는 안됩니다.
# 그렇지 못할 경우에는 dictionary attack(패스워드의 추출이나 암호의 해독에 쓰여지는 공격수법의 하나)을 받게 될 것입니다.
YourApp::Application.config.secret_key_base = '49d3f3de9ed86c74b94ad6bd0...'
```

NOTE: `CookieStore`를 사용할 때 보안키를 변경하게 되면, 기존의 모든 세션들이 무효화될 것입니다.

### [Accessing the Session]세션 접근하기

컨트롤러에서 `session` 인스턴스 메소드를 이용하면 세션에 접근할 수 있습니다.

NOTE: 세션은 필요시에 로드됩니다. 즉, 액션 코드에서 세션값을 호출하지 않으면 세션이 로드되지 안는다는 의미입니다. 따라서 세션을 사용하지 못하게 하는 작업이 필요없고 그저 세션에 접근하지 않으면 된다는 것입니다.

세션 값은 해쉬와 같이 키/값 쌍의 형태로 저장됩니다.

```ruby
class ApplicationController < ActionController::Base

  private

  # :current_user_id 키로 세션에 저장된 ID값으로 사용자를 검색합니다.
  # 레일스 어플리케이션에서 사용자 로그인을 흔히 이런식으로 처리합니다;
  # 로그인하면 세션 값을 설정하고 로그아웃하면 해당 세션 값을 삭제합니다.
  def current_user
    @_current_user ||= session[:current_user_id] &&
      User.find_by_id(session[:current_user_id])
  end
end
```

세션에 어떤 값을 저장하기 위해서는, 해쉬와 같이 특정 키에 할당하면 됩니다.

```ruby
class LoginsController < ApplicationController
  # 사용자에 대한 로그인을 "생성"합니다. "로그인"이라고도 합니다.
  def create
    if user = User.authenticate(params[:username], params[:password])
      # Save the user ID in the session so it can be used in
      # subsequent requests
      session[:current_user_id] = user.id
      redirect_to root_url
    end
  end
end
```

세션에서 어떤 값을 제거하기 위해서는 해당 키에 `nil` 값을 할당하면 됩니다.

```ruby
class LoginsController < ApplicationController
  # "Delete" a login, aka "log the user out"
  def destroy
    # Remove the user id from the session
    @_current_user = session[:current_user_id] = nil
    redirect_to root_url
  end
end
```

세션 전체를 재설정하기 위해서는, `reset_session` 메소드를 사용하면 됩니다.

### [The Flash]플래시(flash) 메시지

플래시는 요청시마다 사라지는 세션의 특수한 형태입니다. 플래시에 저장된 값은 다음번 요청시에만 사용할 수 있다는 의미이며, 에러 메시지 등을 표시할 때 유용하게 사용할 수 있습니다.

해쉬형태([FlashHash](http://api.rubyonrails.org/classes/ActionDispatch/Flash/FlashHash.html)의 인스턴스)로, 세션과 똑같은 방식으로 접근할 수 있습니다. 

예로서 로그아웃 동작시에, 컨트롤러는 다음번 요청 때 사용자에게 보여줄 메시지를 보낼 수 있습니다.

```ruby
class LoginsController < ApplicationController
  def destroy
    session[:current_user_id] = nil
    flash[:notice] = "You have successfully logged out."
    redirect_to root_url
  end
end
```

주목한 것은, 요청에 대한 리디렉션시에 플래시 메시지를 설정할 수도 있다는 것입니다. `:notice`, `:alert` 또는 일반적인 목적의 `:flash`에 할당할 수 있습니다.

```ruby
redirect_to root_url, notice: "You have successfully logged out."
redirect_to root_url, alert: "You're stuck here!"
redirect_to root_url, flash: { referral_code: 1234 }
```

`destroy` 액션은 어플리케이션의 `root_url` 로 리디렉트하며, 플래시 메시지는 리디렉트한 루트 주소에 나타나게 될 것입니다. 이전 액션에서 플래시 메시지로 작성한 것에 대한 처리는 전적으로 다음 액션에 달려있다는 것을 주의해야 합니다. 통상적으로 플래시로 저장된 이벤트성 에러나 알림은 어플리케이션의 레이아웃에 표시하게 될 것입니다.

```erb
<html>
  <!-- <head/> -->
  <body>
    <% flash.each do |name, msg| -%>
      <%= content_tag :div, msg, class: name %>
    <% end -%>

    <!-- more content -->
  </body>
</html>
```

이런식으로 특정 액션에서 에러나 알림 메시지가 작성되면 자동으로 레이아웃에 보이게 될 것입니다.

You can pass anything that the session can store; you're not limited to notices and alerts:
세션이 저장할 수 있는 어떤 것도 플래시 해시로 넘겨 줄 수 있어서, notice와 alert에만 국한할 필요는 없습니다.

```erb
<% if flash[:just_signed_up] %>
  <p class="welcome">Welcome to our site!</p>
<% end %>
```

플래시 메시지를 다른 요청으로 넘기고 싶을 때는 `keep` 메소드를 사용하면 됩니다.

```ruby
class MainController < ApplicationController
  # 이 액션은 root_url에 해당하지만, 이 액션으로 들어오는 모든 요청을 
  # UsersController#index 액션으로 리디렉트하고자 한다고 가정해 보겠습니다.
  # 임의의 액션이 플래시 메시지를 지정하고 여기 root_url로 리디렉트한 후, 그 플래시
  # 메시지를 또 다시 다른 url로 리디렉트한다면 일반적으로는 해당 플래시 메시지가
  # 사라져 버릴 것입니다. 그러나, 이 때, `keep` 메소드를 사용한다면 또 다른 요청시에 
  # 사용할 수 있도록 플래시 메시지를 유지할 수 있게 해 줄 것입니다.  
  def index
    # 모든 플래시 메시지들을 다음번 요청시 사용할 수 있도록 유지해 줄 것입니다.
    flash.keep

    # 또한 특정 키에 대해서만 유질할 수도 있습니다.
    # flash.keep(:notice)
    redirect_to users_url
  end
end
```

#### `flash.now`

기본적으로, 플래시에 추가한 메시지는 다음 번 요청시에 사용할 수 있지만, 때때로 현재의 요청에서 이 메시지를 사용하기를 원할 수도 있습니다. 예를 들면, `create` 액션이 특정 리소스를 저장하는데 실패할 경우 `new` 템플릿을 바로 렌더링하고자 할 것입니다. 이때, 새로운 요청을 하지 않고도 플래시 메시지를 보이게 할 수 있습니다. 이렇게 하기 위해서는, 일반적인 `flash` 를 사용하듯이 `flash.now` 를 사용하면 됩니다.

```ruby
class ClientsController < ApplicationController
  def create
    @client = Client.new(params[:client])
    if @client.save
      # ...
    else
      flash.now[:error] = "Could not save client"
      render action: "new"
    end
  end
end
```

[Cookies]쿠키
-------

어플리케이션은 - 쿠키라고 불리는 - 소량의 데이터를 클라이언트단에 저장할 수 있으며, 요청시마다 심지어 세션간에도 이 값은 사라지지 않고 유지될 것입니다. 레일스는 `cookies` 메소드를 이용해서 쿠키에 쉽게 접근할 수 있게 해 주며, `sessions` 과 같이 하나의 해시처럼 작동하게 됩니다.

```ruby
class CommentsController < ApplicationController
  def new
    # 쿠키에 저장되어 있다면, commenter의 이름을 자동으로 할당해 줍니다.
    @comment = Comment.new(author: cookies[:commenter_name])
  end

  def create
    @comment = Comment.new(params[:comment])
    if @comment.save
      flash[:notice] = "Thanks for your comment!"
      if params[:remember_name]
        # commenter의 이름을 기억해 둡니다.
        cookies[:commenter_name] = @comment.author
      else
        # commenter 이름에 해당하는 쿠키가 존재할 경우 삭제해 줍니다.
        cookies.delete(:commenter_name)
      end
      redirect_to @comment.article
    else
      render action: "new"
    end
  end
end
```

세션 값에 대해서는 키 값을 `nil` 로 할당하는 반면, 쿠키 값을 삭제하기 위해서는 `cookies.delete(:key)` 를 사용해야 함을 주목하기 바랍니다.


[Rendering xml and json data]xml과 json 데이터 렌더링하기
---------------------------

액션 컨트롤러는 `xml` 과 `json` 데이터를 매우 쉽게 렌더링하게 해 줍니다. scaffold를 이용해서 액션 컨트롤러를 생성할 경우 해당 컨트롤러는 다음과 같이 보일 것입니다.

```ruby
class UsersController < ApplicationController
  def index
    @users = User.all
    respond_to do |format|
      format.html # index.html.erb
      format.xml  { render xml: @users}
      format.json { render json: @users}
    end
  end
end
```

위의 경우가 코드가 `render xml: @users.to_xml`이 아니라 `render xml: @users`라는 것을 주목하기 바랍니다. 이것은 입력값이 문자열이 아니기 때문인데, 문자열이 아닌 경우에는 레일스가 자동으로 `to_xml` 을 호출하게 됩니다.

[Filters]필터
-------

필터는 컨트롤러 액션 전, 후, 또는 전후("around")에 실행되는 메소드를 말합니다.

필터는 상속되기 때문에, 특정 필터를 `ApplicationController` 에 작성해 두면 어플리케이션내의 모든 컨트롤러상에서 실행될 것입니다.

"before" 필터는 요청 주기를 중단할 수 있습니다. 흔히 사용하는 "before" 필터는 특정 액션이 실행되기 위해서는 사용자가 로그인해야 하는 경우입니다. 이와 같은 필터 메소드를 다음과 같이 정의할 수 있습니다.

```ruby
class ApplicationController < ActionController::Base
  before_filter :require_login

  private

  def require_login
    unless logged_in?
      flash[:error] = "You must be logged in to access this section"
      redirect_to new_login_url # halts request cycle
    end
  end

  # logged_in? 메소드는 사용자가 로그인한 경우에는 true 값을, 그렇지 않은 경우
  # false 값을 반환합니다. 이것은 이전에 작성했던 current_user 메소드를 !! 연산자를
  # 이용하여 논리값으로 변환하므로써 가능합니다. 이런한 방법은 루비에서 흔하지 않으며
  # 분명하게 특정값을 논리값으로 변환하고자 할 경우 외에는 권장하지 않습니다.
  def logged_in?
    !!current_user
  end
end
```

위의 예에서 require_login 메소드는 에러 메시지를 플래시에 저장하고 사용자가 로그인하지 않은 상태라면 로그인 폼으로 리디렉트하게 됩니다. "before" 필터 메소드가 렌더링을 하거나 리디렉트할 경우 해당 액션은 실행되지 않을 것입니다. 만약 해당 필터이후에 실행되어야할 또 다른 필터가 있는 경우, 그 필터 또한 취소될 것입니다.

위의 예에서, 필터가 `ApplicationController` 에 추가되기 때문에 어플리케이션내에 있는 모든 컨트롤러는 해당 필터를 상속받게 됩니다. 이것은 어플리케이션에 있는 모든 것이 사용자가 그것을 사용하기 위해서는 로그인을 하도록 요구하게 만듭니다. 이런 경우, 최초 사용자가 까지도 로그인을 할 수 없게 되므로 모든 컨트롤러나 액션이 로그인을 요구하게 해서는 안 됩니다. 따라서 `skip_before_filter` 를 사용해서 해당 필터가 특정 before 액션을 실행하지 못하도록 할 수 있습니다.

```ruby
class LoginsController < ApplicationController
  skip_before_filter :require_login, only: [:new, :create]
end
```

이제 `LoginController 컨트롤러의 `new` 액션과 `create` 액션은 사용자들에게 로그인을 요구하지 않고 작업을 수행하게 될 것입니다. `:only` 옵션을 사용하여 특정 액션을 지정하면 이 필터가 적용되지 못하게 할 수 있고, `:except` 옵션을 사용하면, 상반되는 방식으로 작업을 수행하게 됩니다. 이러한 옵션들은 필터를 추가할 때도 사용할 수 있어서 최초에만 해당 액션을 실행할 수 있도록 필터를 추가할 수 있습니다.

### [After Filters and Around Filters]After 필터와 Around 필터

"before" 필터 외에도, 액션이 실행된 이후 또는 전후에 필터가 실행될 수 있도록 할 수 있습니다.

"after" 필터는 "before" 필터와 비슷하지만, 이미 해당 액션이 이미 실행되었기 때문에 액션내에서 클라이언트로 보내게 될 결과 데이터에 접근할 수 있게 됩니다. 분명한 것은, "after" 필터는 액션의 실행을 중단할 수 없는 것입니다.

"around" 필터는, Rack 미들웨어가 동작하는 방법과 비슷하게, 필터 내부에서 액션결과를 yield하여 관련 액션을 실행하도록 합니다.

예를 들면, 변경내용에 대해서, 관리자만이 그 변경내용을 쉽게 검토할 수 있는 승인 절차가 필요한 웹사이트에서는, 그 변경내역을 하나의 트랜잭션내에서 적용해야 합니다.

```ruby
class ChangesController < ActionController::Base
  around_filter :wrap_in_transaction, only: :show

  private

  def wrap_in_transaction
    ActiveRecord::Base.transaction do
      begin
        yield
      ensure
        raise ActiveRecord::Rollback
      end
    end
  end
end
```

"around" 필터가 렌더링을 감싸고 있는 것을 주목해서 보기 바랍니다. 특히, 위의 예에서 뷰 템플릿 자체가 (scope와 같은 것을 통해서) 데이터베이스로부터 읽어 올 경우에는, 트랜잭션 내에서 렌더링하여 미리보기 할 수 있게 해 줍니다.

요청에 대해서 응답을 렌더링하지 않도록 할 수 있는데, 이 때는 해당 액션이 실행되지 않습니다.


### [Other Ways to Use Filters]필터를 사용하는 다른 방법들

필터를 사용할 때 private 메소드를 작성해서 *_filter에 추가하는 것이 가장 일반적인 방법이지만, 여기에는 두가지 방법이 더 있습니다.

첫번째 방법은 *_filter 메소드에 직접 블록을 사용하는 것입니다. 그 블록은 컨트롤러를 인수로 받게 되는데 위에서 언급했던 `require_login` 필터는 블록을 사용해서 다음과 같이 다시 작성할 수 있습니다:

```ruby
class ApplicationController < ActionController::Base
  before_filter do |controller|
    redirect_to new_login_url unless controller.send(:logged_in?)
  end
end
```

이 경우에서 필터가 `send` 메소드를 이용하게 되는데, `logged_in?` 메소드가 private으로 선언되어 있어서 해당 필터가 현재의 컨트롤러의 영역에서 실행되지 않기 때문이라는 것을 주목하기 바랍니다. 이렇게 특별한 필터를 수행하기 위해서 이와 같이 하는 것은 그리 추천할 만한 방법은 아니지만 좀 더 간단한 경우에는 도움이 될 수도 있습니다.

두번째 방법은 하나의 클래스(실제로는 메소드에 대해서 응답을 하는 어떠한 객체라도 가능함)를 이용해서 필터링 작업을 하는 것입니다. 이것은 좀 더 복잡하고, 다른 두가지 메소드를 이용해서 가독성 있고 재사용 가능한 방법으로 수행할 수 없는 경우에 도움이 됩니다. 예를 들어, 클래스를 사용해서 login 필터를 다음과 같이 다시 작성해 볼 수 있습니다.

```ruby
class ApplicationController < ActionController::Base
  before_filter LoginFilter
end

class LoginFilter
  def self.filter(controller)
    unless controller.send(:logged_in?)
      controller.flash[:error] = "You must be logged in"
      controller.redirect_to controller.new_login_url
    end
  end
end
```

또한, 이것은 해당 컨트롤러의 영역에서 실행되지 않고 그 컨트롤러를 인수로서 받기 때문에 그렇게 이상적인 예라고 볼 수는 없습니다. 필터 클래스는 `filter` 클래스 메소드를 가지는데 그것이 before 또는 after 필터인 여부에 따라 액션 전 또는 후에 실행됩니다. around 필터로 사용되는 클래스도 같은 `filter` 메소드를 사용할 수 있는데, 동일한 방식으로 실행될 것입니다. 그러나 이 때는 해당 액션을 실행하여 `yield` 를 해야 합니다. 다른 방법으로, `before` 와 `after` 메소드를 작성해서 액션 전후에 실행되도록 할 수도 있습니다.

[Request Forgery Protection]요청 위조방지
--------------------------

크로스-사이트 요청위조(Cross-site request forgery:CSRF)이란 웹사이트 공격의 한 형태로서 특정 사이트가 특정 사용자를 속여서 다른 사이트에 요청을 하게 하여 해당 사용자 몰래 또는 허락 없이 해당 사이트에서 데이터를 추가, 수정 삭제할 수 있게 하는 것을 말합니다.

이러한 공격을 피하는 첫번째 조치는 create, update, destroy와 같은 모든 "파괴적인" 액션들을 non-GET 요청으로만 접근하도록 했는지 확인하는 것입니다. RESTful 방식을 준수하고 있다면 이미 이러한 조치를 하고 있는 것입니다. 그러나, 특정 사이트가 악의적으로 본인의 사이트에 대해서 non-GET 방식으로도 여전히 요청을 손쉽게 보낼 수 있습니다. 따라서 요청위조에 대한 보호가 필요하게 되는 것입니다. 이름에서 알 수 있듯이, 이것은 위조된 요청으로부터 자신을 보호하게 됩니다.

이를 구현하기 위한 방법은 서버에서만 알 수 있고 어느 누구도 생각해 낼 수 없는 토큰을 각 요청에 대해서 추가해 주는 것입니다. 따라서, 적당한 토큰이 없이 특정 요청이 들어오게 되면 이런식으로 접근이 거부당하게 되는 것입니다.

다음과 같이 폼을 만들 경우라면,

```erb
<%= form_for @user do |f| %>
  <%= f.text_field :username %>
  <%= f.text_field :password %>
<% end %>
```

해당 토큰이 hidden 필드로서 추가되는 방법을 알게 될 것입니다:

```html
<form accept-charset="UTF-8" action="/users/1" method="post">
<input type="hidden"
       value="67250ab105eb5ad10851c00a5621854a23af5489"
       name="authenticity_token"/>
<!-- fields -->
</form>
```

레일스는 [폼 헬퍼메소드](form_helpers.html)를 이용하여 만드는 모든 폼에 대해서 이 토큰을 추가합니다. 따라서 대부분의 경우 이것에 대한 걱정을 할 필요가 없습니다. 직접 폼을 작성한다던지, 다른 어떤 이유로 토큰을 추가할 필요가 있을 때는, `form_authenticity_token` 메소드를 이용하면 토큰을 추가할 수 있습니다

`form_authenticity_token` 메소드는 유효한 인증 토큰을 생성해 줍니다. 특히나, 개발자가 작성한 Ajax 호출시와 같이 레일스가 알아서 추가해 주지 않는 경우에 이 메소드를 유용하게 사용할 수 있습니다.

[Security Guide](security.html)에는, 이에 대해서 뿐만 아니라 웹 개발시 알아야 할 다른 보안관련 문제에 대해서도 자세하게 기술되어 있습니다.

[The Request and Response Objects]요청 및 응답객체
--------------------------------

모든 컨트롤러에서는 현재 진행되고 있는 요청 사이클과 연관된 요청 및 응답 객체를 반환해 주는 2개의 접근자 메소드를 사용할 수 있습니다. `request` 메소드는 `AbstractRequest` 클래스의 인스턴스를 가지고 있고 `response` 메소드는 클라이언트로 보낼 응답 객체를 반환해 줍니다.

### [The `request` Object]`request` 객체

요청 객체는 클라이언트로부터 들어오는 요청에 대한 많은 유용한 정보를 포함하고 있습니다. 사용할 수 있는 모든 리스트를 보기 위해서는 [API documentation](http://api.rubyonrails.org/classes/ActionDispatch/Request.html) 를 참고하기 바랍니다. 이 요청 객체에 대해서 접근할 수 있는 속성들은 다음과 같습니다.

| `request` 속성                    | 설명                                                                          |
| ----------------------------------------- | -------------------------------------------------------------------------------- |
| host                                      | 이 요청에 사용되는 호스트명.                                              |
| domain(n=2)                               | 도메인명 중, 오른쪽으로 시작해서 +n+ 번째 세그먼트에 해당하는 부분(the TLD).            |
| format                                    | 클라이언트에서 요청하는 content의 형태.                                        |
| method                                    | 요청시 사용한 HTTP 메소드.                                            |
| get?, post?, patch?, put?, delete?, head? | HTTP 메소드가 GET/POST/PUT/DELETE/HEAD 인 경우 true 값을 반환함.               |
| headers                                   | 요청과 관련된 헤더를 해쉬형태로 반환함.              |
| port                                      | 요청시 사용한 포트번호(정수).                                  |
| protocol                                  | 사용된 프로토콜에 "://" 를 더한 문자열을 반환함. 예를 들면, "http://". |
| query_string                              | URL의 쿼리문자열, 즉, "?" 다음에 오는 모든 것.                   |
| remote_ip                                 | 클라이언트의 IP 주소.                                                    |
| url                                       | 요청시 사용한 전체 URL.                                             |

#### [`path_parameters`, `query_parameters`, and `request_parameters`]`path_parameters`, `query_parameters`, 그리고 `request_parameters`

레일스는 쿼리스트링이든, POST의 일부로 보내졌던 상관없이 요청시에 보내진 모든 파라메터를 `params` 해쉬에 담아 둡니다. 요청 객체는 3개의 접근자를 가지고 해당 파라메터에 대해 접근할 수 있습니다. `query_parameters` 해쉬는 쿼리스트링으로 보내진 파라메터를 포함하고 있고 `request_parameters` 해쉬는 POST로 보내진 파라메터를 포함하게 됩니다. `path_parameters` 해쉬는 이 특정 컨트롤러와 액션으로 연결되는 경로 중 일부를 파라메터로 포함하게 됩니다.

### [The `response` Object]`response` 객체

응답 객체는 대개는 직접 사용되지 않지만, 액션이 실행될 때 만들어져서 사용자에게 보내질 데이터를 렌더링하게 됩니다. 그러나 때때로, after 필터와 같이, 응답을 직접 접근하는 것이 유용할 경우가 있습니다. 이러한 접근자 메소드 중에는 setter 메소드를 가지고 있어서 직접 그 값을 변경할 수 있게 해 줍니다.

| `response` 속성 | 설명                                                                                             |
| ---------------------- | --------------------------------------------------------------------------------------------------- |
| body                   | 이것은 클라이언트에게 보내질 데이터 문자열입니다. 대부분 HTML입니다.                  |
| status                 | 응답에 대한 HTTP 상태코드입니다. 요청 성공시 200, 파일을 찾지 못할 때 404. |
| location               | 값이 있다면 클라이언트가 리디렉트하게 될 URL.                                                  |
| content_type           | 응답의 content 형태.                                                                   |
| charset                | 응답에 사용하게 될 문자셋. 기본값은 "utf-8"입니다.                                  |
| headers                | 응답에 사용하게 될 헤더.                                                                      |

#### [Setting Custom Headers]커스텀 헤더 셋팅하기

응답에 대해서 커스텀 헤더를 설정하고자 할 때는, `response.headers` 에서 작업하면 됩니다. `headers` 속성은 헤더이름과 값을 연결하는 해시구조로 되어 있으며 레일스는 이 중에 몇가지를 알아서 셋팅해 줍니다. 헤더를 추가하거나 변경하고자 한다면, 다음과 같이 `response.headers` 에 할당하기 하면 됩니다.

```ruby
response.headers["Content-Type"] = "application/pdf"
```

NOTE: 위의 예에서는, `content_type` setter 메소드를 직접사용하는 것이 더 이해가 잘 될 것입니다.

[HTTP Authentications]HTTP 인증
--------------------

레일스에는 2개의 HTTP 인증 메카니즘이 내장되어 있습니다:

* 기본 인증
* Digest 인증

### [HTTP Basic Authentication]HTTP 기본 인증

HTTP 기본 인증은 대부분의 브라우저와 기타 HTTP 클라이언트에서 지원하는 인증 스키마입니다. 예를 들어, 사용자명과 비밀번호를 브라우저의 HTTP 기본 다이알로그 창에 입력하여 접근할 수 있는 관리자 페이지를 생각해 보겠습니다. 이 때 `http_basic_authenticate_with` 메소드만을 이용하여 내장된 인증 시스템을 매우 쉽게 사용할 수 있습니다.

```ruby
class AdminController < ApplicationController
  http_basic_authenticate_with name: "humbaba", password: "5baa61e4"
end
```

이 상태에서 `AdminController` 로부터 상속받는 네이스페이스를 가지는 컨트롤러를 만들 수 있습니다. 이 기본 인증 필터는 해당 컨트롤러의 모든 액션에 대해서 적용이 되어 인증을 보호하게 될 것입니다.

### [HTTP Digest Authentication]HTTP Digest 인증

HTTP digest 인증은 기본 인증보다 더 우수해서 클라이언트로 하여금 네트워크상에서 암호화되지 않는 비밀번호를 보내도록 요구하지 않습니다(물론 HTTP 기본 인증이 HTTPS 보다 안전하기 하지만). 레일스에서 digest 인증을 사용하는 것을 매우 쉬워서 `authenticate_or_request_with_http_digest` 메소드만 필요로 합니다.

```ruby
class AdminController < ApplicationController
  USERS = { "lifo" => "world" }

  before_filter :authenticate

  private

  def authenticate
    authenticate_or_request_with_http_digest do |username|
      USERS[username]
    end
  end
end
```

위의 예에서와 같이 `authenticate_or_request_with_http_digest` 블록은 하나의 인수(username)만을 취합니다. 이 때 블록은 비밀번호를 반환해 주게 됩니다. `authenticate_or_request_with_http_digest` 으로부터 `false` 또는 `nil` 값을 반환하게 되면 인증 실패를 유발하게 될 것입니다.

[Streaming and File Downloads]스트리밍과 파일 다운로드
----------------------------

때때로 HTML 페이지를 렌더링하는 대신 사용자에게 파일을 보내고 싶어할 수 있습니다. 레일스에 있는 모든 컨트롤러는 `send_data` 와 `send_file` 메소드를 가지고 있어서 둘 다 클라이언트에게 데이터를 스트리밍하게 됩니다. `send_file` 메소드는 디스크상의 파일이름을 넘겨 주면 해당 파일의 컨텐츠를 스크리밍해 주는 편리한 메소드입니다.

클라이언트에게 데이터를 스트리밍하기 위해서는 `send_data` 메소드를 사용하면 됩니다:

```ruby
require "prawn"
class ClientsController < ApplicationController
  # client 객체에 있는 정보를 이용해서 PDF 문서를 생성해서 반환합니다. 
  # 이 때, 유저는 파일 다운로드시 PDF 파일을 다운로드 받게 될 것입니다.
  def download_pdf
    client = Client.find(params[:id])
    send_data generate_pdf(client),
              filename: "#{client.name}.pdf",
              type: "application/pdf"
  end

  private

  def generate_pdf(client)
    Prawn::Document.new do
      text client.name, align: :center
      text "Address: #{client.address}"
      text "Email: #{client.email}"
    end.render
  end
end
```

위의 예에서 `download_pdf` 액션은, PDF 문서를 만들어서 문자열로 반환하는 private 메소드를 호출하게 됩니다. 그 때 반환된 문자열은 파일 다운로드시 클라이언트로 스트리밍되며 파일명이 사용자에게 제시될 것입니다. 파일이 사용자에게 스트리밍될 때, 때로는, 파일로 다운로드되기를 원치않을 수 있습니다. 예를 들면 이미지를 받아서 HTML 페이지에 삽입할 수도 있습니다. 브라우저에게 파일을 다운로드하지 않도록 알려주기 위해서, `:disposition` 옵션을 "inline"으로 설정할 수도 있습니다. 이 옵션에 대한 상반되는 값이면서 디폴트값은 "attachement"입니다.

### [Sending Files]파일 보내기

디스크상에 이미 존재하는 파일을 보내고자 할 때는 `send_file` 메소드를 사용하면 됩니다.

```ruby
class ClientsController < ApplicationController
  # 디스크상에 이미 생성되어 저장되어 있는 파일을 스크리밍합니다.
  def download_pdf
    client = Client.find(params[:id])
    send_file("#{Rails.root}/files/clients/#{client.id}.pdf",
              filename: "#{client.name}.pdf",
              type: "application/pdf")
  end
end
```

이것은 한번에 전체 파일을 메모리로 로드하지 않고 4kB씩 읽어서 스트리밍할 것입니다. 이 때, `:stream` 옵션을 사용하여 스트리밍을 해제하거나 `:buffer_size` 옵션을 사용하여 블록 크기를 조절할 수 있습니다.

`:type` 옵션을 별도로 명시하지 않으면, `:filename` 에 명시된 파일 확장자로부터 컨텐츠 유형을 유추하여 판단하게 될 것입니다. 만약 확장자에 대한 컨텐츠 유형이 등록되어 있지 않으면 `application/octet-stream` 이 사용될 것입니다.

WARNING: (params, cookies 등과 같이) 클라이언트로부터 데이터를 가져와서 디스크상의 파일을 찾고자할 때 조심해야 합니다. 왜냐하면, 의도한 바는 아니지만 누군가가 해당 파일들에 대해서 접근권한을 가질 수 있으므로, 보안상의 위험을 초래할 수 있기 때문입니다.

TIP: 웹서버상의 public 폴더에 파일이 있을 때는, 레일스를 통해서 정적 파일을 스트리밍하는 것은 권할만한 일을 아닙니다. 왜냐하면, 요청시 불필요하게 전체 레일스 스택을 찾아보지 않고 바로 아파치나 다른 웹서버를 이용하여 사용자가 직접 파일을 다운로드하도록 하는 것이 훨씬 더 효율적이기 때문입니다.

### [RESTful Downloads]REST방식 다운로드

`send_data` 메소드가 문제없이 작동한다면, REST방식의 어플리케이션을 만들 때, 보통은 파일 다운로드을 위해 별도의 액션을 가질 필요는 없습니다. 왜냐하면, REST 용어상에서 볼 때, 위의 예에서 PDF 파일은 클라이언트 리소스의 또 다른 표현방법에 불과하기 때문입니다. 레일스는 이와 같은 "REST방식 다운로드"를 하기 위한 손쉽고 매우 산뜻한 방법을 제공해 줍니다. 아래는, PDF 다운로드를 스트리밍을 하지 않고 `show` 액션의 일부분이 되도록 다시 코딩하여 보여 줍니다

```ruby
class ClientsController < ApplicationController
  # The user can request to receive this resource as HTML or PDF.
  def show
    @client = Client.find(params[:id])

    respond_to do |format|
      format.html
      format.pdf { render pdf: generate_pdf(@client) }
    end
  end
end
```

위의 코드가 작동하기 위해서는, 레일스에게 PDF MIME 형을 추가해 주어야 합니다. 이것은 `config/initializers/mime_types.rb` 파일에 아래의 코드라인을 추가해 주면 됩니다.

```ruby
Mime::Type.register "application/pdf", :pdf
```

NOTE: 레일스의 구성 파일(configuration file)은 매 요청시마다 다시 로드되지 않기 때문에, 변경내용이 반영되기 위해서는 서버를 다시 시작해야 합니다.

이제 사용자의 요청시, URL 끝에 ".pdf"를 추가해 주기만 하면 클라이언트에 대한 PDF 버전을 다운로드 받을 수 있게 됩니다:

```bash
GET /clients/1.pdf
```

[Parameter Filtering]파라메터 필터하기
-------------------

레일스는 `log` 폴더에 해당 환경에 대한 로그 파일을 유지합니다. 이것은 어플리케이션에서 실제로 일어나는 일을 디버깅할 때 매우 유용하지만, 운영환경에서는 모든 정보를 로그파일에 저장하기를 원치 않을 수 있습니다. 이 때 어플리케이션 구성 파일 내의 `config.filter_parameters` 에 요청하는 파라메터를 지정해 두면 로그파일에 해당 파라메터를 필터할 수 있게 됩니다. 즉, 이 파라메터는 로그파일에서 [FILTERED]로 표기될 것입니다.

```ruby
config.filter_parameters << :password
```

[Rescue]예외처리
------

어플리케이션은, 처리해 주어야 하는, 버그나 예외가 발생할 가능이 많습니다. 예를 들어, 사용자가 데이터베이스에서 더 이상 존재하지 않는 데이터 리소스를 찾고자 한다면, 액티브 레코드는 `ActiveRecord::RecordNotFound` 예외를 발생시킬 것입니다.

레일스의 기본 예외처리 기전은 모든 예외에 대해서 "500 Server Error" 메시지를 보여주게 됩니다. 로컬 웹서버에서 요청이 발생할 경우에는, 코드 추적과 이와 관련된 몇가지 추가 정보들이 나타나서 무슨 문제가 발생했는지를 알 수 있어 금방 해결할 수 있습니다. 그러나 원격 웹서버에 대해서 요청이 발생할 경우, 라우팅 에러나 레코드를 찾을 수 없을 때 레일스는 "500 Server Error" 나 "404 Not Found" 메시지만 단순하게 보여 줄 것입니다. 때때로 이러한 에러를 잡아내는 방법과 사용자에게 표시해 주는 방법을 변경하고자 할 경우가 있습니다. 레일스 어플리케이션에서는 예외 처리하는 방법에 대한 몇가지 레벨이 있습니다:

### [The Default 500 and 404 Templates]디폴트 500과 404 에러 템플릿 파일

기본적으로 운영환경상의 어플리케이션은 404 또는 500 에러 메시지를 보여 줄 것입니다. 이 메시지는 `public` 폴더에 있는 정적 HTML 파일(`404.html` 과 `500.html`)내에 포함되어 있습니다. 이 파일들을 수정해서 몇가지 특수 정보와 레이아웃을 추가할 수 있지만, 기억할 것은 이 파일들은 정적, 즉, 단순한 HTML 파일이라서 RHTML이나 레이아웃을 사용할 수 없다는 것입니다.

### `rescue_from`

에러를 잡아서 좀 더 정교하게 처리하고자 한다면, `rescue_from` 메소드를 이용할 수 있습니다. 이 경우, 전체 컨트롤러와 하부 클래스에서 임의의 형태(들)의 예외를 처리할 수 있습니다.

예외가 발생하여 `rescue_from` 이 잡아낼 경우 해당 예외 객체가 핸들러에게로 넘겨가게 됩니다. 이 때 핸들러는 하나의 메소드이거나 `Proc` 객체일 수 있으며 `:with` 옵션으로 명시할 수 있습니다. 아니면 명시적으로 `Proc` 객체로 지정하는 대신, 바로 블록을 사용할 수도 있습니다.

아래에서, `rescue_from` 을 사용하여, 모든 `ActiveRecord::RecordNotFound` 에러를 감지해서 조치를 취하는 방법을 보여 줍니다.

```ruby
class ApplicationController < ActionController::Base
  rescue_from ActiveRecord::RecordNotFound, with: :record_not_found

  private

  def record_not_found
    render text: "404 Not Found", status: 404
  end
end
```

물론, 위의 예는 복잡할 뿐 기본 예외처리를 전혀 개선하지 않았습니다. 그러나 일단 모든 예외처리를 잡아낼 수 있다면 원하는 데로 자유롭게 처리할 수 있게 됩니다. 예를 들면, 사용자정의 예외 클래스를 만들어서 사용자가 어플리케이션의 특정 부분을 접근할 수 없을 때 예외를 발생시킬 수 있습니다:

```ruby
class ApplicationController < ActionController::Base
  rescue_from User::NotAuthorized, with: :user_not_authorized

  private

  def user_not_authorized
    flash[:error] = "You don't have access to this section."
    redirect_to :back
  end
end

class ClientsController < ApplicationController
  # Check that the user has the right authorization to access clients.
  before_filter :check_authorization

  # Note how the actions don't have to worry about all the auth stuff.
  def edit
    @client = Client.find(params[:id])
  end

  private

  # If the user is not authorized, just throw the exception.
  def check_authorization
    raise User::NotAuthorized unless current_user.admin?
  end
end
```

NOTE: 어떤 예외는 해당 컨트롤러가 초기화되어 액션이 실행되기 전에 발생하기 때문에 `ApplicationController` 클래스에서만 복구할 수 있습니다. Pratik Naik의 [기사](http://m.onkey.org/2008/7/20/rescue-from-dispatching) 를 보면 이것에 대한 대한 더 자세한 내용을 알게 될 것입니다.

[Force HTTPS protocol]강제로 HTTPS 프로토콜 사용하기
--------------------

어떤 경우에는, 보안상의 이유로 HTTPS 프로토콜로만 특정 컨트롤러를 접근하도록 할 때가 있을 수 있습니다. 레일스 3.1 부터는 `force_ssl` 메소드를 이용하여 해결할 수 있습니다:

```ruby
class DinnerController
  force_ssl
end
```

필터와 같이, `:only` 와 `:except` 옵션을 이용하면 특정 액션에 대해서만 보안 연결을 할 수 있습니다.

```ruby
class DinnerController
  force_ssl only: :cheeseburger
  # or
  force_ssl except: :cheeseburger
end
```

다수의 컨트롤러에 대해서 `force_ssl` 을 추가할 경우에는 어플리케이션 전체에 대해서 HTTPS 프로토콜을 사용하는 것을 생각해 볼 필요가 있습니다. 이런 경우에는, 환경파일에 `config.force_ssl` 을 설정할 수 있습니다.

NOTE: 초벌번역: 2012년 5월 7일 ~ 최효성(hschoidr@gmail.com), 2차번역: 2013년 1월 7일 ~ 최효성(hschoidr@gmail.com) 譯
