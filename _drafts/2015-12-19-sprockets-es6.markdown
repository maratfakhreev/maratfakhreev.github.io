---
layout: post
title: "Anonymous function wrapper for ES6 files in Rails applications"
date: 2015-12-19
type: code
---
Once upon a time Marat decided to use ES6 in Rails application. It is not a bad idea but as it turned out `Rails` does not offer a good integration with ES6 like gives `Babel + Gulp` for example. Ok, omit the details and let setup our application to use ES6 syntax.

### 1. First install the sprockets-es6 gem:

```bash
gem install sprockets-es6
```

### 2. Add gem to Gemfile

```ruby
gem "sprockets", ">= 3.0.0"
gem "sprockets-es6", require: "sprockets/es6"
```

Cool, now you can use [ES2015](https://babeljs.io/docs/learn-es2015/) features. I am not sure that in the near future it will be possible to use some delicious like class properties, decorators e.t.c from ES7 but thanks for that. So let write our first very usefull `HelloWorld` class and use `.es6` extension for this.

```javascript
class HelloWorld {
  constructor() {
    this.title = 'Hello World';
  }

  call() {
    console.log(this.title);
  }
}

const hw = new HelloWorld();
hw.call();
```

The code works, it is cool and it was time to finish the article if it was not for one problem. If you try to write `window.HelloWorld` you will get your class in global scope. In global scope, IN GLOBAL SCOPE? Are you seriously sprockets?

Rails with CoffeeScript wraps every `.coffee` file with anonymous function wrapper to avoid global scope issues. It looks like `(function() { //yourCode// }).call(this);` and sprockets-es6 does not have such functionality out of the box.

To fix it we should create simple initializer `anonymous_function_wrapper.rb` in `config/initializers` directory and put this stuff to it:

```ruby
Rails.application.assets.register_postprocessor 'application/javascript', :anon_wrap do |context, data|
  pathname = context.pathname.to_s
  check_anon_function_wrap = !/^\(function/.match(data)

  if pathname['/app/assets/javascripts'] && pathname['.es6'] && check_anon_function_wrap
    "(function() {\n#{data}\n}).call(this);"
  else
    data
  end
end
```

As you can see above code create wrapper for each es6 file in `app/assets/javascripts` directory which does not have same wrapper. The last actions are to clear cache, compiled assets <br /> `rm -rf public/assets tmp` and run `bin/rake assets:precompile`.

That is all. Now you have wrapped files which you can call modules and a lot of happiness from the possibility of using es6 syntax instead of CoffeeScript. Say goodbye to CoffeeScript :)
