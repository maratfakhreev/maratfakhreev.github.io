---
layout: post
title: "Anonymous function wrapper for ES6 files in Rails application"
date: 2015-12-19
type: code
---
Once upon a time Marat decided to use ES6 in Rails application. It is not a bad idea but as it turned out `Rails` does not offer a good integration with ES6 like `Babel + Gulp` does, for example. Ok, now omit the details and let's setup our application to use ES6 syntax.

### 1. First install the sprockets-es6 gem:

```bash
gem install sprockets-es6
```

### 2. Add gem to Gemfile

```ruby
gem "sprockets", ">= 3.0.0"
gem "sprockets-es6", require: "sprockets/es6"
```

That is cool, now you can use [ES2015](https://babeljs.io/docs/learn-es2015/) features. I am not sure that in the near future it will be possible to use such yummy features as class properties, decorators e.t.c from ES7 but thanks even for what we have. So, let's write our first very usefull `HelloWorld` class and use `.es6` extension for this.

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

The code works, it is cool and then it is time to finish the article if only there was not one problem. If you try to write `window.HelloWorld` you get your class in global scope. In global scope, IN GLOBAL SCOPE? Are you seriously sprockets?

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

As you can see the posted above code creates a wrapper for each es6 file in `app/assets/javascripts` directory which does not have the same wrapper. The last actions are to clear cache, compiled assets <br /> `rm -rf public/assets tmp` and run `bin/rake assets:precompile`.

That's it!  Now you have wrapped the files (you can call them modules) and perhaps you are super happy because of this opportunity to use es6 syntax instead of CoffeeScript. Say goodbye to CoffeeScript :)
