레일스 어플리케이션 테스트 가이드
=====================================

본 가이드는 레일스에서 제공하는 내장 메카니즘을 이용해서 어플리케이션을 테스트하는 방법에 대해서 다룹니다. 본 가이드를 참고하면 아래의 사항을 할 수 있게 됩니다.

* 레일스 테스트 용어 이해하기
* 어플리케이션에 대한 단위(unit), 기능(functional), 그리고 통합(integration) 테스트 작성하기
* 기타 다른 인기있는 테스트 접근법과 플러그인 알아보기

--------------------------------------------------------------------------------

[Why Write Tests for your Rails Applications?]레일스 어플리케이션을 위한 테스트를 작성해야 하는 이유는? 
--------------------------------------------

레일스에서는 테스트를 매우 쉽게 작성할 수 있습니다. 모델과 컨트롤러를 생성할 때 테스트 코드 템플릿이 자동으로 생성되어 테스트를 시작할 수 있게 해 줍니다. 

많은 양의 코드 리팩토링을 한 후에도, 레일스 테스트를 실행하기만 하면, 원하는 기능이 잘 유지되는 지를 확인할 수 있게 됩니다.

레일스 테스트는 브라우저로부터의 요청을 시뮬레이션할 수 있어서, 실제 브라우저를 통해서 테스트해 보지 않아도, 어플리케이션의 응답을 테스트해 볼 수 있습니다.  

[Introduction to Testing]테스트 소개
-----------------------

테스트를 위한 지원은 레일스가 만들어질 때부터 이미 레일스에 잘 통합되어 있었습니다. 다시 말해서, 레일스가 만들어진 이후에 새로 추가된 것이 아니라는 것입니다. 모든 레일스 어프릴케이션은 많은 부분에서 데이터베이스와 상호작용을 하기 때문에, 테스트 역시 데이터베이스와의 상호작용이 필요하게 될 것입니다. 효과적인 테스트를 작성하기 위해서는 테스트용 데이터베이스를 설정하고, 샘플 데이터를 데이터베이스에 올리는 방법에 대해서 알아 둘 필요가 있습니다.    

### [The Test Environment]테스트 환경

디폴트 상태에서, 모든 레일스 어플리케이션은 3개의 환경을 가지게 됩니다(개발, 테스트, 그리고 운영환경). 또한, 각각에 대한 데이터베이스가 `config/database.yml` 파일에 정의되어 있습니다.   

테스트 전용 데이터베이스는 테스트 데이터를 별도로 셋업해서 작업을 할 수 있게 해 줍니다. 따라서 테스트용 데이터만 가지고 테스트 작업을 하기 때문에, 개발이나 운영환경용 데이터베이스에 있는 데이터에 대해서는 접근을 하지 않게 될 것입니다. 

### [Rails Sets up for Testing from the Word Go]레일스는 처음부터 테스트환경을 셋업한다. 

레일스는 `rails new` _application_name_ 명령으로 레일스 프로젝트를 생성하자마자 `test` 폴더를 생성합니다. 이 폴더의 내용을 보면 다음과 같습니다.

```bash
$ ls -F test

fixtures/  functional/  integration/  performance/  test_helper.rb  unit/
```

`unit` 디렉토리에는 모델에 대한 테스트 파일들이, `functional` 디렉토리에는 컨트롤러에 대한 테스트 파일들이, `integration` 디렉토리에는 컨트롤러들이 상호작용하는 것과 관련된 테스트 파일들이, `performance` 디렉토리에는 전체 어플리케이션의 성능을 테스트하는 파일들이 위치하게 됩니다.

Fixtures라는 것은 테스트 데이터을 구조화하는 방법을 말합니다. 따라서 `fixtures` 폴더에 위치하게 됩니다.

`test_helper.rb` 파일은 테스트를 위한 디폴트 설정을 가지게 됩니다.

### [The Low-Down on Fixtures]Fixtures에 대한 내막

훌륭한 테스트가 되기 위해서는, 신중하게 테스트 데이터를 셋업할 필요가 있습니다. 레일스에서는 fixtures를 정의하고 변경할 수 있기 때문에, 이러한 일이 가능하게 됩니다.

#### [What Are Fixtures?]Fixtures란 무엇인가?

_Fixtures_ 란 샘플 데이터에 대한 매혹적인 단어입니다. Fixtures를 이용하면, 테스트 전에 미리 정의된 데이터를 테스트용 데이터베이스에 올려 놓을 수 있습니다. Fixtures는 YAML로 작성되기 때문에 데이터베이스에 독립적입니다. 모델당 하나의 fixture 파일만이 존재하게 됩니다. 

`test/fixtures` 디렉토리에 fixture 파일들이 위치하게 됩니다. 새 모델을 생성하기 위해서 `rails generate model` 명령을 실행하면 fixture 파일들이 이 디렉토리에 자동으로 생성되어 위치하게 될 것입니다.

#### YAML

YAML 포맷으로 생성되는 fixture 파일들은 인간에게 친숙한 방법으로 샘플 데이터를 기술합니다. 이러한 형태의 fixture 파일들은 `users.yml` 과 같이 **.yml** 파일 확장자를 가집니다.

아래에 샘플 YAML fixture 파일이 있습니다. 

```yaml
# lo & behold!  I am a YAML comment!
david:
 name: David Heinemeier Hansson
 birthday: 1979-10-15
 profession: Systems development

steve:
 name: Steve Ross Kellock
 birthday: 1974-09-27
 profession: guy with keyboard
```

하나의 fixture는, 하나의 이름 다음에, 콜론(:)으로 구분되는 키/값 쌍 목록이 들여쓰기 상태로 오게 됩니다. 레코드는 일반적으로 blank 스페이스로 구분됩니다. 코멘트는 첫번째 컬럼에 # 문자를 사용하여 기술할 수 있습니다.

#### [ERB'in It Up]ERB로 작업하기

ERB는 fixture 템플릿 파일내에서 임베디드 루비코드를 사용할 수 있게 해 줍니다. YAML fixture 포맷은 레일스가 fixtures를 로드할 때 ERB 엔진이 사전 처리하게 됩니다. 따라서 루비를 사용하여 샘플 데이터를 생성할 수도 있습니다. 예를 들면, 다음의 코드는 천개의 사용자를 생성해 줍니다. 

```erb
<% 1000.times do |n| %>
user_<%= n %>:
  username: <%= "user%03d" % n %>
  email: <%= "user%03d@example.com" % n %>
<% end %>
```

#### [Fixtures in Action]Fixtures 파일의 동작

디폴트 상태에서 레일스는 단위 및 기능 테스트를 위해서 `test/fixtures` 폴더에 위치하는 모든 fixtures 파일을 자동으로 로드해 줍니다. 로딩은 3단계를 거치게 됩니다.

* fixture에 해당하는 기존 데이터를 테이블로부터 제거합니다.  
* fixture 데이터를 테이블로 로드합니다. 
* 직접 접근하고자 할 경우, fixture 데이터를 하나의 변수로 덤프해 줍니다. 

#### [Fixtures are ActiveRecord objects]Fixtures는 ActiveRecord 객체이다 

Fixtures는 ActiveRecord의 인스턴스들입니다. 위의 세번째 단계에서 언급했던 바와 같이, 테스트 케이스의 로컬변수로 해당 객체를 자동으로 셋업해 주기 때문에, ActiveRecord 객체를 직접 접근할 수 있습니다. 예를 들면, 

```ruby
# 이것은 david 라는 이름의 fixture에 대한 User 객체를 반환해 줍니다. 
users(:david)

# 이것은 david에 대한 id 속성값을 반환해 줍니다. 
users(:david).id

# User 클래스에 대해서 사용가능한 메소드를 접근할 수 있습니다. 
email(david.girlfriend.email, david.location_tonight)
```

[Unit Testing your Models]모델을 단위 테스트하기
------------------------

레일스에서 단위 테스트는 모델을 테스트하기 위해 작성하는 것입니다. 

본 가이드에서는 레일스의 _scaffolding_ 을 사용할 것입니다. 이것은 한번의 명령으로 모델, 마이그레이션, 컨트롤러와 뷰 파일들을 생성할 것입니다. 또한 레일스의 최적화를 유지하기 위한 완벽한 테스트 환경을 만들게 될 것입니다. 이렇게 생성된 코드로부터 examples를 사용할 것이고 필요시에 examples를 추가하게 될 것입니다. 

NOTE: 레일스 <i>scaffolding</i>에 대한 자세한 정보는 [Getting Started with Rails](getting_started.html)를 참고하기 바랍니다. 

`rails generate scaffold` 명령을 실행할 때, `test/unit` 폴더상에 하나의 리소스에 대한 테스트 파일을 생성하게 됩니다. 

```bash
$ rails generate scaffold post title:string body:text
...
create  app/models/post.rb
create  test/unit/post_test.rb
create  test/fixtures/posts.yml
...
```

`test/unit/post_test.rb` 파일에 있는 디폴트 테스트 코드는 다음과 같습니다. 

```ruby
require 'test_helper'

class PostTest < ActiveSupport::TestCase
  # Replace this with your real tests.
  test "the truth" do
    assert true
  end
end
```

이 파일내의 코드를 살펴보는 것은 레일스의 테스트 코드와 용어에 대한 감을 잡는데 도움을 줄 것입니다. 

```ruby
require 'test_helper'
```

이제까지 알고 있는 바와 같이 `test_helper.rb` 파일은 테스트를 수행하기 위한 디폴트 설정을 지정해 줍니다. 이것은 모든 테스트 파일에 포함되어서 이 파일에 추가되는 모든 메소드는 모든 테스트 파일에서 사용할 수 있게 됩니다. 

```ruby
class PostTest < ActiveSupport::TestCase
```

`PostTest` 클래스는 `ActiveSupport::TestCase`로부터 상속받기 때문에 하나의 _test case_ 를 정의하게 됩니다. 따라서 `PostTest`는 `ActiveSupport::TestCase`에서 사용할 수 있는 모든 메소드를 가지게 됩니다. 잠시 후에 이 메소드를 보게 될 것입니다.

`Test::Unit` 테스트 케이스에 정의된 소문자 `test`로 시작하는 모든 메소드를 간단하게 하나의 test라고 호칭하게 됩니다. 따라서 `test_password`, `test_valid_password`, `testValidPassword` 는 모두 유효한 테스트명이 되며 테스트 케이스가 실행될 때 자동으로 실행됩니다. 

레일스는 하나의 테스트명과 블록을 가지는 `test` 메소드를 추가해 줍니다. 이것은 `test_`로 시작하는 메소드명을 가지는 일반적인 `Test::Unit` 테스트를 생성합니다. 그래서, 

```ruby
test "the truth" do
  assert true
end
```

이것은 다음과 같이 동일하게 동작합니다. 

```ruby
def test_the_truth
  assert true
end
```

`test` 매크로는 테스트명을 단지 훨신 더 읽기 쉽게 해 줍니다. 그러나 여전히 일반적인 메소드 정의를 사용할 수 있습니다. 

NOTE: 메소드명은 스페이스를 밑줄문자로 대체하여 생성하게 됩니다. 이렇게 만들어진 메소드명은 루비의 유효한 구분자일 필요는 없어서, 마침표 문자 등도 포함할 수 있습니다. 이것은 루비에서 기술적으로 어떤한 문자열도 메소드명으로 사용할 수 있기 때문입니다. 특이한 것들은 `define_method`와 `send` 메소드를 호출할 필요가 있지만 공식적으로는 제한은 없습니다. 

```ruby
assert true
```

이 코드라인을 _assertion(가정)_ 이라고 합니다. 가정이란, 하나의 객체 또는 expression에 대해서 예상되는 결과값과 비교하는 코드라인을 말합니다.

* 이 값이 저 값과 같은가? 
* 이 객체는 nil값을 가지는가?
* 이 코드라인은 예외를 발생하는가?
* 해당 유저의 비밀번호는 길이가 5이상인가?

모든 테스트는 하나 또는 그 이상의 가정을 포함게 됩니다. 모든 가정이 성공할 때만 테스트는 통과하게 됩니다. 

### [Preparing your Application for Testing]테스트를 위해 어플리케이션을 준비하기

테스트를 실행하기 전에, 테스트 데이터베이스 구조가 현재 작동 가능한 지를 확인할 필요가 있습니다. 이를 위해서, 다음과 같이 rake 명령을 사용할 수 있습니다.

```bash
$ rake db:migrate
...
$ rake db:test:load
```

위의 명령에서 `rake db:migrate`는 _development_ 환경에서 대기 중인 모든 마이그레이션을 실행해서 `db/schema.rb` 파일을 업데이트하게 됩니다. `rake db:test:load`는 현재의 `db/schema.rb`파일로부터 테스트 데이터베이스를 재생성합니다. 그래서 이후에는 먼저 `db:test:prepare` 명령을 실행하여 대기 중인 마이그레이션이 있는지를 확인해서 적절하게 경고 메시지를 보여주게 하는 것이 좋은 아이디어가 될 것입니다.

NOTE: `db:test:preapre`는 `db/schema.rb`파일이 없을 경우 에러와 함께 실패하게 될 것입니다. 

#### [Rake Tasks for Preparing your Application for Testing]어플리케이션 테스트 준비를 위한 Rake Tasks

| Tasks                          | Description                                                               |
| ------------------------------ | ------------------------------------------------------------------------- |
| `rake db:test:clone`           | Recreate the test database from the current environment's database schema |
| `rake db:test:clone_structure` | Recreate the test database from the development structure                 |
| `rake db:test:load`            | Recreate the test database from the current `schema.rb`                   |
| `rake db:test:prepare`         | Check for pending migrations and load the test schema                     |
| `rake db:test:purge`           | Empty the test database.                                                  |

TIP: `rake --tasks --describe` 명령을 실행하면 모든 관련 rake tasks와 설명을 볼 수 있다. 

### [Running Tests]테스트 실행하기

하나의 테스트를 실행하는 것은 루비를 이용해서 테스트 케이스를 포함하는 파일을 호출하는 것만큼 간단합니다. 

```bash
$ ruby -Itest test/unit/post_test.rb

Loaded suite models/post_test
Started
.
Finished in 0.023513 seconds.

1 tests, 1 assertions, 0 failures, 0 errors
```

이것은 테스트 케이스로부터 모든 테스트 메소드를 실행하게 됩니다. 주의할 것은 `test_helper.rb` 파일이 `test` 디렉토리에 위치하기 때문에 `-I` 스위치 옵션을 사용해서 이 디렉토리에 대한 로드 경로를 추가해 줄 필요가 있습니다. 

테스트 메소드 이름과 함께 `-n` 스위치 옵션을 이용하면 테스트 케이스로부터 특정 테스트 메소드만을 실행할 수도 있습니다.

```bash
$ ruby -Itest test/models/post_test.rb -n test_the_truth

Loaded suite models/post_test
Started
.
Finished in 0.023513 seconds.

1 tests, 1 assertions, 0 failures, 0 errors
```

위에서 `.`(dot) 문자는 통과한 테스트를 표시해 줍니다. 만약 테스트가 실패하게 되면 `F` 문자가 표시됩니다. 또한 에러가 발생할 경우에는 `E` 문자가 표시됩니다. 결과문의 마지막 라인은 테스트 요약을 표시해 줍니다. 

테스트 실패시에 보고되는 방식을 알기 위해 `post_test.rb` 테스트 케이스에 failing 테스트를 하나 추가하도록 합니다. 

```ruby
test "should not save post without title" do
  post = Post.new
  assert !post.save
end
```

새로 추가된 테스트를 실행하도록 합니다. 

```bash
$ ruby unit/post_test.rb -n test_should_not_save_post_without_title
Loaded suite -e
Started
F
Finished in 0.102072 seconds.

  1) Failure:
test_should_not_save_post_without_title(PostTest) [/test/models/post_test.rb:6]:
<false> is not true.

1 tests, 1 assertions, 1 failures, 0 errors
```

테스트 결과에서 `F` 문자는 실패를 나타내는 것입니다. `1)` 아래에 failing 테스트 이름과 함께 해당 추적내용을 볼 수 있습니다. 그 다음에는, 가정에 의한 기대값과 실제 값을 나타내는 메시지가 스택 추적내용 아래에 표시됩니다. 디폴트 가정 메시지는 에러의 원인을 정확하게 알아낼 수 있을 만큼 충분한 정보를 제공해 줍니다. 가정에 대한 실패 메시지를 더 판독하기 쉽게 하기 위해서는 아래와 같이 모든 가정에 message 파라메터 옵션을 제공해 주면 됩니다. 

```ruby
test "should not save post without title" do
  post = Post.new
  assert !post.save, "Saved the post without a title"
end
```

이 테스트를 실행하면 보다 더 친숙한 가정 메시지를 보여 주게 됩니다. 

```bash
  1) Failure:
test_should_not_save_post_without_title(PostTest) [/test/models/post_test.rb:6]:
Saved the post without a title.
<false> is not true.
```

이제 이 테스트가 통과할 수 있도록 _title_ 필드에 대해서 모델 레벨의 유효성 검증을 추가할 수 있습니다. 

```ruby
class Post < ActiveRecord::Base
  validates :title, presence: true
end
```

이제 이 테스트는 통과해야만 합니다. 다시 이 테스트를 실행해서 확인해 보도록 합니다. 

```bash
$ ruby unit/post_test.rb -n test_should_not_save_post_without_title
Loaded suite unit/post_test
Started
.
Finished in 0.193608 seconds.

1 tests, 1 assertions, 0 failures, 0 errors
```

이제까지 작업한 내용을 보와 왔다면, 먼저, 원하는 기능에 대한 가정이 실패하도록 테스트를 작성하고, 이후에 해당 기능을 구현하기 위해 약간의 코드를 작성합니다. 그리고나서, 최종적으로 테스트가 통과한 것을 확인하게 됩니다. 소프트웨어 개발에 대한 이러한 접근법을 _Test-Driven Development_ (TDD)라고 말합니다. 

TIP: 많은 수의 레일스 개발자들은 _Test-Driven Development_ (TDD) 방식을 이용하여 작업을 합니다. 이것은 어플리케이션의 모든 부분을 점검하는 테스트 셋을 구축하기 위한 우수한 방법입니다. TDD는 본 가이드의 영역을 넘어서는 것이지만 처음 시작하기에 좋은 곳은 [15 TDD steps to create a Rails application](http://andrzejonsoftware.blogspot.com/2007/05/15-tdd-steps-to-create-rails.html) 입니다. 

에러가 발생할 때 어떻게 되는 알아 보기 위해 아래와 같이 에러를 포함하는 테스트 하나를 작성합니다. 

```ruby
test "should report error" do
  # some_undefined_variable is not defined elsewhere in the test case
  some_undefined_variable
  assert true
end
```

이제 테스트를 실행하면 콘솔에 더 많은 결과를 볼 수 있게 됩니다. 

```bash
$ ruby unit/post_test.rb -n test_should_report_error
Loaded suite -e
Started
E
Finished in 0.082603 seconds.

  1) Error:
test_should_report_error(PostTest):
NameError: undefined local variable or method `some_undefined_variable' for #<PostTest:0x249d354>
    /test/models/post_test.rb:6:in `test_should_report_error'

1 tests, 0 assertions, 0 failures, 1 errors
```

결과에서 `E` 문자를 보게 될 것입니다. 이것은 테스트 상의 에러를 표시해 줍니다. 

NOTE: 각 테스트 메소드는 에러가 발생하거나 가정이 실패하자마자 바로 중단되지만 테스트 셋은 다음 테스트 메소드와 함께 계속진행됩니다. 모든 테스트 메소드는 알파벳순으로 실행됩니다. 

### [What to Include in Your Unit Tests]단위 테스트에서 포함해야 할 사항

이상적으로는, 문제가 있을 수 있는 모든 것에 대해서 테스트를 작성하는 것일 것입니다. 유효성 검증 각각에 대해서 적어도 하나의 테스트를 작성하고 모델의 모든 메소드에 대해서 적어도 하나의 테스트를 작성하는 것이 좋습니다. 

### [Assertions Available]사용가능한 가정들 

지금까지 사용가능한 가정들 중 몇가지를 맛보기로 알게 되었습니다. 가정이란 테스트를 하는 일벌과 같습니다. 가정이란, 구현한 내용들이 계획한 대로 제대로 동작하는지를 확신하기 위해, 실제로 확인 작업을 하는 것입니다.

사용할 수 있는 여러가지 종류의 가정들이 있습니다. 아래에는 레일스가 사용하는 디폴트 테스트 라이브러리인 `test/unit`와 함께 배포되는 전체 가정 목록이 있습니다. `[msg]` 파라메터는 문자열 메시지 옵션으로, 테스트가 실패할 때 좀 더 명확한 메시지를 표시하기 위해서 사용할 수 있지만 필수옵션을 아닙니다. 

| Assertion                                                        | Purpose |
| ---------------------------------------------------------------- | ------- |
| `assert( boolean, [msg] )`                                       | Ensures that the object/expression is true.|
| `assert_equal( expected, actual, [msg] )`                        | Ensures that `expected == actual` is true.|
| `assert_not_equal( expected, actual, [msg] )`                    | Ensures that `expected != actual` is true.|
| `assert_same( expected, actual, [msg] )`                         | Ensures that `expected.equal?(actual)` is true.|
| `assert_not_same( expected, actual, [msg] )`                     | Ensures that `!expected.equal?(actual)` is true.|
| `assert_nil( obj, [msg] )`                                       | Ensures that `obj.nil?` is true.|
| `assert_not_nil( obj, [msg] )`                                   | Ensures that `!obj.nil?` is true.|
| `assert_match( regexp, string, [msg] )`                          | Ensures that a string matches the regular expression.|
| `assert_no_match( regexp, string, [msg] )`                       | Ensures that a string doesn't match the regular expression.|
| `assert_in_delta( expecting, actual, delta, [msg] )`             | Ensures that the numbers `expecting` and `actual` are within `delta` of each other.|
| `assert_throws( symbol, [msg] ) { block }`                       | Ensures that the given block throws the symbol.|
| `assert_raise( exception1, exception2, ... ) { block }`          | Ensures that the given block raises one of the given exceptions.|
| `assert_nothing_raised( exception1, exception2, ... ) { block }` | Ensures that the given block doesn't raise one of the given exceptions.|
| `assert_instance_of( class, obj, [msg] )`                        | Ensures that `obj` is of the `class` type.|
| `assert_kind_of( class, obj, [msg] )`                            | Ensures that `obj` is or descends from `class`.|
| `assert_respond_to( obj, symbol, [msg] )`                        | Ensures that `obj` has a method called `symbol`.|
| `assert_operator( obj1, operator, obj2, [msg] )`                 | Ensures that `obj1.operator(obj2)` is true.|
| `assert_send( array, [msg] )`                                    | Ensures that executing the method listed in `array[1]` on the object in `array[0]` with the parameters of `array[2 and up]` is true. This one is weird eh?|
| `flunk( [msg] )`                                                 | Ensures failure. This is useful to explicitly mark a test that isn't finished yet.|

테스트 프레임워크는 모듈방식으로 만들어졌기 때문에, 자신만의 가정을 만들 수 있습니다. 사실, 이것이야 말로 바로 레일스가 추구하는 바입니다. 테스트 프레임워크는 더 편하게 해주는 몇가지 특수한 가정을 제공해 줍니다. 

NOTE: 자신만의 가정을 만드는 것을 고급 주제라서 이 튜토리얼에서는 다루지 않을 것입니다. 

### [Rails Specific Assertions]레일스 전용 가정들 

레일스는 `test/unit` 프레임워크에 레일스 전용 가정을 몇가지 더 추가해 줍니다. 

| Assertion                                                                         | Purpose |
| --------------------------------------------------------------------------------- | ------- |
| `assert_difference(expressions, difference = 1, message = nil) {...}`             | Test numeric difference between the return value of an expression as a result of what is evaluated in the yielded block.|
| `assert_no_difference(expressions, message = nil, &amp;block)`                    | Asserts that the numeric result of evaluating an expression is not changed before and after invoking the passed in block.|
| `assert_recognizes(expected_options, path, extras={}, message=nil)`               | Asserts that the routing of the given path was handled correctly and that the parsed options (given in the expected_options hash) match path. Basically, it asserts that Rails recognizes the route given by expected_options.|
| `assert_generates(expected_path, options, defaults={}, extras = {}, message=nil)` | Asserts that the provided options can be used to generate the provided path. This is the inverse of assert_recognizes. The extras parameter is used to tell the request the names and values of additional request parameters that would be in a query string. The message parameter allows you to specify a custom error message for assertion failures.|
| `assert_response(type, message = nil)`                                            | Asserts that the response comes with a specific status code. You can specify `:success` to indicate 200-299,  `:redirect` to indicate 300-399, `:missing` to indicate 404, or `:error` to match the 500-599 range|
| `assert_redirected_to(options = {}, message=nil)`                                 | Assert that the redirection options passed in match those of the redirect called in the latest action. This match can be partial, such that `assert_redirected_to(controller: "weblog")` will also match the redirection of `redirect_to(controller: "weblog", action: "show")` and so on.|
| `assert_template(expected = nil, message=nil)`                                    | Asserts that the request was rendered with the appropriate template file.|

다음 장에서 이 가정들 중 몇가지의 사용법을 알게 될 것입니다. 

컨트롤러에 대한 기능 테스트 
-------------------------------------

레일스에서는, 단일 컨트롤러의 다양한 액션에 대해서 테스트하는 것을, 해당 컨트롤러에 대한 기능 테스트를 작성한다고 말합니다. 컨트롤러는 외부로부터 들어오는 웹요청을 처리하고 최종적으로 뷰 템플릿을 렌더링하여 클라이언트 브라우저로 응답결과를 보내 줍니다. 

### [What to Include in your Functional Tests]기능 테스트에서 포함해야 할 것들 

다음과 같은 것들을 테스트해야 합니다. 

* 웹요청이 성공적으로 이루어졌는가?
* 유저가 올바른 페이지로 리디렉트되었는가? 
* 유저가 성공적으로 인증되었는가? 
* 올바른 객체가 뷰 템플릿에 저장되었는가? 
* 적절한 메시지가 뷰 상에서 유저에게 표시되었는가? 

`Post` 리소스에 대해서 레일스의 scaffold 제너레이터를 사용했기 때문에 이미 해당 컨트롤러와 테스트 환경이 만들어진 상태입니다. 따라서, `test/functional` 디렉토리에서 `posts_controller_test.rb` 파일을 볼 수 있을 것입니다. 

이제 `posts_controller_test.rb` 파일내의 `test_should_get_index` 테스트를 살펴보도록 하겠습니다. 

```ruby
test "should get index" do
  get :index
  assert_response :success
  assert_not_nil assigns(:posts)
end
```

`test_should_get_index` 테스트에서, 레일스는 `index` 액션에 대한 요청을 시뮬레이션 해서, 요청이 성공적으로 이루어졌고 `posts` 인스턴스 변수가 할당된 것을 확인하게 될 것입니다. 

`get` 메소드는 웹요청을 하게 되고 그 결과를 반송하게 될 것입니다. 이 메소드는 4개의 인수를 가집니다. 

* 요청을 하게 될 컨트롤러의 액션이름. 이것은 문자열 또는 심볼형태를 가질 수 있습니다. 
* 액션으로 넘겨 주게 되는 요청 파라메터에 대한 해쉬 옵션(예, 쿼리 스트링 파라메터 또는 post 변수) 
* 요청시에 함께 넘겨지게 되는 세션 변수에 대한 해쉬 옵션 
* 플래시 값에 대한 옵션 해쉬 

예: `params` 해쉬로 12값을 가지는 `id`와 세션 변수로 5값을 가지는 `user_id`와 함께 `:show` 액션을 호출하기 

```ruby
get(:show, {'id' => "12"}, {'user_id' => 5})
```

또 다른 예: `params` 해쉬로 12값을 가지는 `id`와, 이번에는 세션 변수 없이, 하나의 플래시 메시지와 함께 `:view` 액션을 호출하기 

```ruby
get(:view, {'id' => '12'}, nil, {'message' => 'booya!'})
```

NOTE: `posts_controller_test.rb` 파일로부터 `test_should_create_post` 테스트를 실행한다면, 새로 추가된 모델 레벨의 유효성 검증 등의 이유로 실패하게 될 것이고 이것은 당연한 것입니다. 

`posts_controller_test.rb` 파일에 있는 `test_should_create_post` 테스트를 아래와 같이 수정하면, 테스트는 통과할 것입니다. 

```ruby
test "should create post" do
  assert_difference('Post.count') do
    post :create, post: {title: 'Some title'}
  end

  assert_redirected_to post_path(assigns(:post))
end
```

이제 모든 테스트를 실행하면 모두 통과되어야 합니다. 

### [Available Request Types for Functional Tests]기능 테스트에서 사용가능한 요청 종류 

HTTP 프로토콜에 익숙하다면, `get`이 요청의 한 종류라는 것을 알 것입니다. 레일스의 기능 테스트에서 사용할 수 있는 요청은 6가지 종류가 있습니다. 

* `get`
* `post`
* `patch`
* `put`
* `head`
* `delete`

모든 종류의 요청을 기능 테스트에서 메소드로 사용할 수 있지만, 결국에는 처음 두개만을 더 자주 사용하게 될 것입니다. 

NOTE: 기능 테스트는 명시된 액션이 해당 요청의 종류를 수락해야 하는지의 여부를 확인하지는 않습니다. 이 문맥에서 요청의 종류는 그저 테스트를 더 알기 쉽게 기술해 주는 용도로 사용되는 것입니다. 

### [The Four Hashes of the Apocalypse]최후의 4 해시들 

5개의 메소드(`get`, `post` 등) 중의 하나를 이용한 요청에 대한 처리가 마무리되면, 다음과 같은 4개의 해시 객체를 사용할 수 있게 됩니다. 

* `assigns` - 뷰에서 사용할 용도로 쓰이는 액션내의 인스턴스 변수로 저장되는 모든 종류의 객체 
* `cookies` - 결과로서 할당되는 모든 쿠키 
* `flash` - 플래시 해쉬내에 존재하는 모든 객체 
* `session` - 세션 변수형태로 존재하는 모든 객체 

일반적인 해시 객체와 같이, 문자열로 키를 참조하여 키값을 사용할 수 있습니다. 또한 `assigns`를 제외하고 모든 키를 심볼명으로 참조할 수도 있습니다. 예를 들면, 

```ruby
flash["gordon"]               flash[:gordon]
session["shmession"]          session[:shmession]
cookies["are_good_for_u"]     cookies[:are_good_for_u]

# 관례적으로 assigns[:something]은 사용할 수 없습니다. 
assigns["something"]          assigns(:something)
```

### [Instance Variables Available]사용가능한 인스턴스 변수들 

또한 기능 테스트에서 3개의 인스턴스 변수를 사용할 수 있습니다. 

* `@controller` - 요청을 처리하는 컨트롤러 
* `@request` - 요청 
* `@response` - 응답 

### [Testing Templates and Layouts]템플릿과 레이아웃 테스트하기 

렌더링 결과가 올바른 템플릿과 레이아웃을 사용하여 만들어졌는지를 확인하고자 한다면, asset_template 메소드를 사용할 수 있습니다. 

```ruby
test "index should render correct template and layout" do
  get :index
  assert_template :index
  assert_template layout: "layouts/application"
end
```

주의할 것은, `asset_template` 메소드를 한번 호출하여 템플릿과 레이아웃에 대한 테스트를 동시에 할 수 없다는 것입니다. 또한 레이아웃 테스트에 대해서 문자열 대신에 정규표현식을 사용할 수 있지만 문자열을 사용할 경우에는 더 명확하게 알 수 있습니다. 한편, 레이아웃 파일을 표준 레이아웃 디렉토리에 저장해 두었다 하더라도 “layouts”이라는 디렉토리 이름을 포함해야 한다는 것입니다. 따라서, 

```ruby
assert_template layout: "application"
```

위의 가정은 작동하지 않을 것입니다.

레이아웃을 테스트할 때 뷰 템플릿 파일이 partial을 포함고 있다면 동시에 partial에 대한 가정도 해주어야 합니다. 그렇게 하지 않으면 실패하게 될 것입니다. 

그러므로

```ruby
test "new should render correct layout" do
  get :new
  assert_template layout: "layouts/application", partial: "_form"
end
```

위와 같이 지정하면, 뷰가 `_form`이라는 partial을 렌더링할 때, 레이아웃에 대한 가정을 하는 것이 올바른 방법이 되는 것입니다. `assert_template` 메소드를 호출할 때 `:partial` 키를 생략하게 되면 문제가 발생하게 됩니다.  

### [A Fuller Functional Test Example]더 완벽한 기능 테스트에 대한 예 

아래에 또 다른 예가 있습니다. 여기서는 flash`, `assert_redirected_to`, 그리고 `assert_difference`를 사용합니다. 

```ruby
test "should create post" do
  assert_difference('Post.count') do
    post :create, post: {title: 'Hi', body: 'This is my first post.'}
  end
  assert_redirected_to post_path(assigns(:post))
  assert_equal 'Post was successfully created.', flash[:notice]
end
```

### [Testing Views]뷰 테스트하기

주요 HTML 엘리먼트와 그 내용이 존재함을 가정하므로써 요청에 대한 응답을 테스트하는 것은 어플리케이션의 뷰들을 테스트하기 위한 유용한 방법입니다. `assert_select` 가정메소드를 사용하면 간단하지만 강력한 문법을 이용하여 이러한 테스트를 할 수 있게 됩니다. 

NOTE: 다른 문서에서 `assert_tag`에 대한 참조를 찾아 볼 수 있지만 이것은 이제 사용하지 않게 되었고 `assert_select`를 우선적으로 사용하게 되었습니다. 

`assert_select`는 두가지 형태로 사용할 수 있습니다. 

`assert_select(selector, [equality], [message])`는 selector를 통해서 선택되어진 엘리먼트가 조건에 부합하는지를 확인할 때 사용합니다. selector는 문자열 형태의 CSS selector 표현식, 대체값을 가지는 표현식, 또는 `HTML::Selector` 객체가 될 수 있습니다. 

`assert_select(element, selector, [equality], [message])`는 `HTML::Node`의 인스턴스인 _element_ 와 하위 엘리먼트를 포함하여 selector를 통해서 선택된 모든 엘리먼트가 조건에 부합하는지를 확인할 때 사용합니다. 

예를 들어, 아래와 같이 select 가정을 사용하면, 요청에 대한 응답으로 만들어지는 내용 중에서 title 엘리먼트의 내용을 확인할 수 있을 것입니다. 

```ruby
assert_select 'title', "Welcome to Rails Testing Guide"
```

또한 `assert_select` 블록을 중첩해서 사용할 수도 있습니다. 이 경우에는,  내부 `asset_select`는 외부 `assert_select` 블록에서 선택한 모든 엘리먼트 컬렉션에 대해서 가정을 수행하게 됩니다. 

```ruby
assert_select 'ul.navigation' do
  assert_select 'li.menu_item'
end
```

또 다른 방법으로는, 외부 `assert_select`가 선택한 엘리먼트 컬렉션에 대해서 반복작업을 수행할 수 있기 때문에, `assert_select`를 각 엘리먼트에 대해서 별도로 호출할 수 있게 됩니다. 예를 들어, 응답내용 중에 각각 4개의 리스트 엘리먼트를 가지는 두개의 ordered list(ol)를 포함할 경우, 다음과 같이 테스트를 작성할 경우 실패없이 통과할 것입니다. 

```ruby
assert_select "ol" do |elements|
  elements.each do |element|
    assert_select element, "li", 4
  end
end

assert_select "ol" do
  assert_select "li", 8
end
```

`asset_select`는 기능이 매우 강력합니다. 더 고급기능을 사용하고자 한다면, [documentation](http://api.rubyonrails.org/classes/ActionDispatch/Assertions/SelectorAssertions.html)를 참고하기 바랍니다. 

#### [Additional View-Based Assertions]추가사용 가능한 뷰 기반의 가정들 

뷰 테스트할 때 주로 사용하는 가정들이 더 있습니다. 

| Assertion                                                  | Purpose |
| ---------------------------------------------------------- | ------- |
| `assert_select_email`                                      | Allows you to make assertions on the body of an e-mail. |
| `assert_select_encoded`                                    | Allows you to make assertions on encoded HTML. It does this by un-encoding the contents of each element and then calling the block with all the un-encoded elements.|
| `css_select(selector)`  or `css_select(element, selector)` | Returns an array of all the elements selected by the _selector_. In the second variant it first matches the base _element_ and tries to match the _selector_ expression on any of its children. If there are no matches both variants return an empty array.|

아래에 `assert_select_email`을 사용하는 예가 있습니다. 

```ruby
assert_select_email do
  assert_select 'small', 'Please click the "Unsubscribe" link if you want to opt-out.'
end
```

[Integration Testing]통합 테스트하기 
-------------------

통합 테스트는 여러개의 컨트롤러 사이의 상호작용을 테스트하기 위해 사용합니다. 일반적으로 통합 테스트는 어플리케이션내에서 중요한 작업 흐름을 테스트하기 위해서 사용합니다. 

단위 테스트와 기능 테스트와는 달리 통합 테스트는 어플리케이션내의 `test/integration` 폴더 아래에 명시적으로 생성해 주어야 합니다. 따라서 레일스는 개발자를 대신해서 통합 테스트를 위한 골격을 생성하기 위한 제너레이터를 제공해 줍니다. 

```bash
$ rails generate integration_test user_flows
      exists  test/integration/
      create  test/integration/user_flows_test.rb
```

아래에는 방금 전에 생성된 통합 테스트를 보여 줍니다. 

```ruby
require 'test_helper'

class UserFlowsTest < ActionDispatch::IntegrationTest
  fixtures :all

  # Replace this with your real tests.
  test "the truth" do
    assert true
  end
end
```

통합 테스트는 `ActionDispatch::IntegrationTest`로부터 상속을 받습니다. 따라서 몇가지 헬퍼 메소드들을 통합 테스트에서 추가로 사용할 수 있게 됩니다. 또한, 통합 테스트에서 사용할 수 있도록, 명시적으로 fixtures(테스트 데이터)를 포함시켜 줄 필요가 있습니다. 

### [Helpers Available for Integration Tests]통합 테스트에 사용할 수 있는 헬퍼 메소드 

표준 테스트 헬퍼메소드 외에도, 통합 테스트에서 사용할 수 있는 헬퍼메소드들이 더 있습니다. 

| Helper                                                             | Purpose |
| ------------------------------------------------------------------ | ------- |
| `https?`                                                           | Returns `true` if the session is mimicking a secure HTTPS request.|
| `https!`                                                           | Allows you to mimic a secure HTTPS request.|
| `host!`                                                            | Allows you to set the host name to use in the next request.|
| `redirect?`                                                        | Returns `true` if the last request was a redirect.|
| `follow_redirect!`                                                 | Follows a single redirect response.|
| `request_via_redirect(http_method, path, [parameters], [headers])` | Allows you to make an HTTP request and follow any subsequent redirects.|
| `post_via_redirect(path, [parameters], [headers])`                 | Allows you to make an HTTP POST request and follow any subsequent redirects.|
| `get_via_redirect(path, [parameters], [headers])`                  | Allows you to make an HTTP GET request and follow any subsequent redirects.|
| `patch_via_redirect(path, [parameters], [headers])`                | Allows you to make an HTTP PATCH request and follow any subsequent redirects.|
| `put_via_redirect(path, [parameters], [headers])`                  | Allows you to make an HTTP PUT request and follow any subsequent redirects.|
| `delete_via_redirect(path, [parameters], [headers])`               | Allows you to make an HTTP DELETE request and follow any subsequent redirects.|
| `open_session`                                                     | Opens a new session instance.|

### [Integration Testing Examples]통합 테스트 예 

여러개의 컨트롤러를 함께 테스트하는 간단한 통합 테스트 예가 아래에 있습니다. 

```ruby
require 'test_helper'

class UserFlowsTest < ActionDispatch::IntegrationTest
  fixtures :users

  test "login and browse site" do
    # login via https
    https!
    get "/login"
    assert_response :success

    post_via_redirect "/login", username: users(:avs).username, password: users(:avs).password
    assert_equal '/welcome', path
    assert_equal 'Welcome avs!', flash[:notice]

    https!(false)
    get "/posts/all"
    assert_response :success
    assert assigns(:products)
  end
end
```

보시다시피, 통합 테스트는 여러개의 컨트롤러가 관련되고 데이터베이스로부터 dispatcher에 이르는 전체 스택을 점검하게 됩니다. 게다가, 하나의 테스트 내에서 동시에 여러개의 세션을 만들 수 있고, 가정 메소드를 이용하여 이 세션들을 확장하면 해당 어플케이션에서만 사용할 수 있는 강력한 테스트용 DSL(domain-specific language)를 만들 수도 있습니다. 

아래에 하나의 통합 테스트내에서 다중 세션과 전용 DSL을 만드는 예가 있습니다. 

```ruby
require 'test_helper'

class UserFlowsTest < ActionDispatch::IntegrationTest
  fixtures :users

  test "login and browse site" do

    # User avs logs in
    avs = login(:avs)
    # User guest logs in
    guest = login(:guest)

    # Both are now available in different sessions
    assert_equal 'Welcome avs!', avs.flash[:notice]
    assert_equal 'Welcome guest!', guest.flash[:notice]

    # User avs can browse site
    avs.browses_site
    # User guest can browse site as well
    guest.browses_site

    # Continue with other assertions
  end

  private

  module CustomDsl
    def browses_site
      get "/products/all"
      assert_response :success
      assert assigns(:products)
    end
  end

  def login(user)
    open_session do |sess|
      sess.extend(CustomDsl)
      u = users(user)
      sess.https!
      sess.post "/login", username: u.username, password: u.password
      assert_equal '/welcome', path
      sess.https!(false)
    end
  end
end
```

[Rake Tasks for Running your Tests]테스트를 실행하기 위한 Rake Tasks 
---------------------------------

모든 테스트를 하나씩 셋업하고 실행할 필요는 없습니다. 레일스는 이미 테스트에 필요한 많은 rake tasks를 제공해 줍니다. 아래 테이블에는, 레일스 프로젝트 처음 생성될 때, 이미 디폴트 Rakefile에 작성되어 있는 모든 rake tasks에 대한 리스트를 보여 줍니다. 

| Tasks                           | Description |
| ------------------------------- | ----------- |
| `rake test`                     | Runs all unit, functional and integration tests. You can also simply run `rake` as the _test_ target is the default.|
| `rake test:benchmark`           | Benchmark the performance tests|
| `rake test:controllers`         | Runs all the controller tests from `test/controllers`|
| `rake test:functionals`         | Runs all the functional tests from `test/controllers`, `test/mailers`, and `test/functional`|
| `rake test:helpers`             | Runs all the helper tests from `test/helpers`|
| `rake test:integration`         | Runs all the integration tests from `test/integration`|
| `rake test:mailers`             | Runs all the mailer tests from `test/mailers`|
| `rake test:models`              | Runs all the model tests from `test/models`|
| `rake test:profile`             | Profile the performance tests|
| `rake test:recent`              | Tests recent changes|
| `rake test:uncommitted`         | Runs all the tests which are uncommitted. Supports Subversion and Git|
| `rake test:units`               | Runs all the unit tests from `test/models`, `test/helpers`, and `test/unit`|


[Brief Note About `Test::Unit`]`Test::Unit`에 대한 간단한 노트 
-----------------------------

루비는 많은 라이브러리와 함께 배포됩니다. 그 중의 한 라이브러리 상의 작은 젬 하나가, 루비의 단위 테스트를 위한 프레임워크인, `Test::Unit`인 것입니다. 위에서 언급했던 모든 기본 가정들은 실제로 `Test::Unit::Assertions`에 정의되어 있습니다. 단위 테스트와 기능 테스트에서 사용해 왔던 `ActiveSupport::TestCase` 클래스는 `Test::Unit::TestCase`의 기능을 확장하여 개발자가 테스트를 작성할 때 모든 기본 가정들을 사용할 수 있게 해줍니다. 

NOTE: `Test::Unit`에 대한 더 자세한 정보는 [test/unit Documentation](http://ruby-doc.org/stdlib/libdoc/test/unit/rdoc/)를 참고하기 바랍니다. 

[Setup and Teardown]Setup 메소드와 Teardown(해체) 메소드 
------------------

모든 테스트를 시작하기 전과 후에 별도의 코드블록을 실행하고자 한다면, 두개의 특별한 콜백을 작성할 수 있습니다. `Posts` 컨트롤러에서 기능 테스트를 하도록 작성한 예를 보면서 이것에 대해 주의깊게 살펴 보도록 하겠습니다. 

```ruby
require 'test_helper'

class PostsControllerTest < ActionController::TestCase

  # called before every single test
  def setup
    @post = posts(:one)
  end

  # called after every single test
  def teardown
    # as we are re-initializing @post before every test
    # setting it to nil here is not essential but I hope
    # you understand how you can use the teardown method
    @post = nil
  end

  test "should show post" do
    get :show, id: @post.id
    assert_response :success
  end

  test "should destroy post" do
    assert_difference('Post.count', -1) do
      delete :destroy, id: @post.id
    end

    assert_redirected_to posts_path
  end

end
```

위의 예에서 보면, `setup` 메소드는 모든 테스트 전에 호출되기 때문에 `@post` 인스턴스 변수는 모든 테스트상에서 사용할 수 있게 됩니다. 레일스는 `ActiveSupport::Callbacks`을 이용하여 `setup`과 `teardown`을 구현합니다. 이것은 결코 테스트내에서 `setup`과 `teardown`을 메소드의 형태로서만 사용할 필요가 없다는 것을 의미합니다. 즉, 아래의 4가지 형태를 이용하여 구현할 수 있습니다. 

* a block
* a method (like in the earlier example)
* a method name as a symbol
* a lambda

메소드 이름을 심볼로 지정하여 `setup` 콜백을 지정하면 위의 예를 아래와 같이 수정할 수 있습니다. 

```ruby
require '../test_helper'

class PostsControllerTest < ActionController::TestCase

  # called before every single test
  setup :initialize_post

  # called after every single test
  def teardown
    @post = nil
  end

  test "should show post" do
    get :show, id: @post.id
    assert_response :success
  end

  test "should update post" do
    patch :update, id: @post.id, post: {}
    assert_redirected_to post_path(assigns(:post))
  end

  test "should destroy post" do
    assert_difference('Post.count', -1) do
      delete :destroy, id: @post.id
    end

    assert_redirected_to posts_path
  end

  private

  def initialize_post
    @post = posts(:one)
  end

end
```

[Testing Routes]라우트 테스트하기
--------------

레일스 어플리케이션에 있는 모든 것을 테스트 하듯이 라우트도 테스트하도록 권장하고 있습니다. 위의 `Posts` 컨트롤러의 디폴트 상태의 `show` 액션에 있는 라우트에 대한 테스트 예는 아래와 같습니다. 

```ruby
test "should route to post" do
  assert_routing '/posts/1', {controller: "posts", action: "show", id: "1"}
end
```

[Testing Your Mailers]메일러 테스트하기
--------------------

메일러 클래스에 대한 테스트를 꼼꼼하게 하기 위해서는 특별한 툴들이 필요합니다. 

### [Keeping the Postman in Check]Keeping the Postman in Check

레일스 어플리케이션의 모든 파트에 대해서와 같이 메일러 클래스에 대해서도 예상하는 데로 작동하는지를 테스트해야만 합니다. 

메일러 클래스를 테스트하는 목적은 아래의 사항을 확인하기 위한 것입니다. 

* 이메일이 제대로 생성되어 발송되고 있는지. 
* 이메일의 내용이 정확한지(subjet, sender, body 등). 
* 정확안 이메일이 정확한 시간에 발송되고 있는지. 

#### [From All Sides]From All Sides

메일러 테스트에는 두가지가 있습니다. 하나는 단위 테스트이고 다른 하나는 기능 테스트입니다. 단위 테스트에서는, 입력을 철저히 통제한 상태에서 별개로 메일러를 실행하여 결과를 알려진 값(a fixture)과 비교하는 것입니다. 기능 테스트에서는, 메일러가 만들어 내는 내용을 아주 상세하게 테스트하지는 않습니다. 대신에, 컨트롤러와 모델들이 제대로 메일러를 사용하고 있는지를 테스트하게 됩니다. 제대로 작성된 메일이 제 시간에 발송되었는지를 테스트합니다. 

### [Unit Testing]단위 테스트하기 

메일러가 예상하는 데로 작동하고 있는지를 테스트하기 위해서는, 단위 테스트를 이용하여 메일러가 만들어내는 실제 결과를 미리 작성된 결과와 비교할 수 있습니다. 

#### [Revenge of the Fixtures]Fixtures의 복수 

메일러에 대해서 단위 테스트를 위해서, 어떤 결과물이 _나와야_ 하는지에 대한 견본을 보여주기 위해서 fixtures를 사용하게 됩니다. 이것들은, 다른 fixture와 같이 ActiveRecord 데이터가 아니고, 결과물에 대한 견본 이메일이므로 다른 fixtures와는 별개로 하위 디렉토리를 만들어 관리하도록 합니다. `test/fixtures`내의 디렉토리 이름은 메일러의 이름에 해당합니다. 그래서, `UserMailer`라는 이름을 가지는 메일러에 대해서는, `test/fixtures/user_mailer` 디렉토리에 fixtures가 위치해야만 합니다. 

메일러를 생성할 때, 메일러 제너레이터는 메일러 액션 각각에 대한 fixtures를 만들어 줍니다. 제너레이터를 사용하지 않을 경우에는, 직접 이러한 파일들을 만들어 주어야 할 것입니다. 

#### [The Basic Test Case]기본 테스트 케이스 

아래에는,`invite` 액션을 이용해서 친구에게 초대장을 발송하는 `UserMailer`라고 명명된 메일러를 테스트하기 위한 단위 테스트가 있습니다. 이것은 `invite`액선에 대해서 제너레이터가 만들어 주는 기본 테스트을 상황에 맞도록 수정한 버전입니다. 

```ruby
require 'test_helper'

class UserMailerTest < ActionMailer::TestCase
  tests UserMailer
  test "invite" do
    @expected.from    = 'me@example.com'
    @expected.to      = 'friend@example.com'
    @expected.subject = "You have been invited by #{@expected.from}"
    @expected.body    = read_fixture('invite')
    @expected.date    = Time.now

    assert_equal @expected.encoded, UserMailer.create_invite('me@example.com', 'friend@example.com', @expected.date).encoded
  end

end
```

이 테스트에서, `@expected`는 테스트에서 사용할 수 있는 `TMail::Mail`의 인스턴스 변수입니다. 이것은 `ActionMailer::TestCase`에 정의되어 있습니다. 위의 테스트는 `@expected`를 이용해서 이메일을 작성하고 이것을 메일러가 만들게 되는 이메일과 비교하게 되는 것입니다. `invite` fixture 는 이메일의 body 내용이 되고 비교할 견본 컨텐츠가 되는 것입니다. `read_fixture` 헬퍼메소드를 사용하면 이 파일로부터 내용을 읽어 들일 수 있습니다. 

아래는 `invite` fixture의 내용을 보여 줍니다. 

```
Hi friend@example.com,

You have been invited.

Cheers!
```

지금이 메일러에 대한 테스트를 작성하는 것에 대해 조금 더 많은 것을 이해할 수 있는 적기입니다. `config/environments/test.rb` 파일내의 `ActionMailer::Base.delivery_method = :test` 코드라인은 이메일 발송 메소드를 테스트 모드로 설정하기 때문에, 실제로는 이메일이 발송되지 않게 되며, 이것은 테스트 중에 유저들에게 스탬메일을 발송하지 않도록 해 줍니다. 대신에 이메일은 하나의 배열(`ActionMailer::Base.deliveries`)에 추가될 것입니다. 

그러나 단위 테스트에서 종종, 위의 예에서 이메일의 정확한 내용과 견본을 비교한 것과 같이, 간단히 만들어진 이메일이 실제로 발송되지 않을 경우가 있게 됩니다. 

### [Functional Testing]기능 테스트하기 

메일러에 대한 기능 테스트는 이메일 body, recipients 등이 정확한지를 확인하는 것 이상을 하게 됩니다. 메일의 기능 테스트에서 메일 발송 메소드를 호출하여 적절한 이메일들이 발송 리스트에 추가되었는지를 확인하게 됩니다. 이렇게 하면 발송 메소드가 자신의 일을 제대로 한다고 안전하게 가정할 수 있게 되는 것입니다. 아마도 메일이 발송되었을 것으로 생각할 때 비즈니스 로직상 이메일이 발송되고 있는지에 대해서 더 관심을 가지게 될 것입니다. 예를 들어, 친구를 초대하는 동작이 제대로 이메일을 발송하는지를 확인할 수 있습니다. 

```ruby
require 'test_helper'

class UserControllerTest < ActionController::TestCase
  test "invite friend" do
    assert_difference 'ActionMailer::Base.deliveries.size', +1 do
      post :invite_friend, email: 'friend@example.com'
    end
    invite_email = ActionMailer::Base.deliveries.last

    assert_equal "You have been invited by me@example.com", invite_email.subject
    assert_equal 'friend@example.com', invite_email.to[0]
    assert_match(/Hi friend@example.com/, invite_email.body)
  end
end
```

[Other Testing Approaches]기타 다른 테스트 방법들 
------------------------

내장된 `test/unit` 기반의 테스트만이 레일스 어플리케이션을 테스트하는 유일한 방법은 아닙니다. 레일스 개발자들은 아래와 같이 다양한 종류의 테스트 방법을 찾아서 테스트를 위한 도움을 받고 있습니다. 

* [NullDB](http://avdi.org/projects/nulldb/), a way to speed up testing by avoiding database use.
* [Factory Girl](https://github.com/thoughtbot/factory_girl/tree/master), a replacement for fixtures.
* [Machinist](https://github.com/notahat/machinist/tree/master), another replacement for fixtures.
* [Shoulda](http://www.thoughtbot.com/projects/shoulda), an extension to `test/unit` with additional helpers, macros, and assertions.
* [RSpec](http://relishapp.com/rspec), a behavior-driven development framework
