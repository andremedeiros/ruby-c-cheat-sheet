## Getting started

### 1. Your gem skeleton

    $ bundle gem my_awesome_gem
    $ cd my_awesome_gem
    $ mkdir -p ext/my_awesome_gem
    
### 2. Teaching Rake how to compile your gem

`my_awesome_gem/my_awesome_gem.gemspec`

    spec.add_development_dependency "rake-compiler"
    
`my_awesome_gem/Rakefile`

    require "rake/extensiontask"

	Rake::ExtensionTask.new("my_awesome_gem") do |ext|
		ext.lib_dir = "lib/my_awesome_gem"
	end
	
### 3. Boilerplate

`my_awesome_gem/ext/my_awesome_gem/extconf.rb`

    require 'mkmf'
    create_makefile('my_awesome_gem')
    
`my_awesome_gem/ext/my_awesome_gem/my_awesome_gem.c`
    
    #include "ruby.h"
    
    VALUE MyAwesomeGemModule = Qnil;
    
    void Init_my_awesome_gem()
    {
        MyAwesomeGemModule = rb_define_module("MyAwesomeGem");
        rb_init_awesome_class();
    }

### 4. Your first class

`my_awesome_gem/ext/my_awesome_gem/my_awesome_gem.c`

	#include "ruby.h"
	#include "awesome_class.h"
	
	// ...
	
    void Init_my_awesome_gem()
    {
        MyAwesomeGemModule = rb_define_module("MyAwesomeGem");
        rb_init_awesome_class();
    }


`my_awesome_gem/ext/my_awesome_gem/awesome_class.h`

	#include "ruby.h"
	void rb_init_awesome_class();

`my_awesome_gem/ext/my_awesome_gem/awesome_class.c`

    #include "awesome_class.h"
    
    VALUE AwesomeClass = Qnil;
    extern VALUE MyAwesomeGemModule;
    
    void rb_init_awesome_class()
    {
    	AwesomeClass = rb_define_class_under(MyAwesomeGemModule, "AwesomeClass", rb_cObject);
    }
    
### 5. Your first instance method

`my_awesome_gem/ext/my_awesome_gem/awesome_class.h`

	#include "ruby.h"
	void rb_init_awesome_class();
	VALUE rb_awesome_class_say_hi(VALUE self);
	VALUE rb_awesome_class_say_hi_to(VALUE self, VALUE r_name);
	
`my_awesome_gem/ext/my_awesome_gem/awesome_class.c`

    #include "awesome_class.h"
    
    VALUE AwesomeClass = Qnil;
    extern VALUE MyAwesomeGemModule;
    
    void rb_init_awesome_class()
    {
    	AwesomeClass = rb_define_class_under(MyAwesomeGemModule, "AwesomeClass", rb_cObject);
    	rb_define_method(AwesomeClass, "say_hi", rb_awesome_class_say_hi, 0);
    	rb_define_method(AwesomeClass, "say_hi_to", rb_awesome_class_say_hi, 1);
    }
    
	VALUE rb_awesome_class_say_hi(VALUE self)
	{
		return rb_str_new2("Hello!");
	}

	VALUE rb_awesome_class_say_hi_to(VALUE self, VALUE r_name)
	{
		VALUE r_greeting = rb_str_new2("Hello, ");
		rb_str_append(r_greeting, r_name);
		rb_str_append(r_greeting, INT2NUM(33));
		
		return r_greeting;
	}
	
### 6. Including your new class

`my_awesome_gem/lib/my_awesome_gem.rb`

    require 'my_awesome_gem/my_awesome_gem'
    
### 7. Compiling

    $ rake clean compile

## Modules and Classes

### rb_define_module(`module_name`)

Defines the module named `module_name`.

    VALUE AwesomeNewModule = rb_define_module("Awesome");
    
### rb_define_module_function(`ruby_module`, `method_name`, `implementation`, `args`)

Defines a function called `method_name` under the module `ruby_module`. Expects `args` arguments and calls `implementation` when invoked in Ruby.

	VALUE rb_module_say_hi(VALUE klass)
	{
	    VALUE r_hello = rb_str_new2("Hello!");
	    return r_hello;
	}
	
	void Init_example()
	{
		VALUE AwesomeNewModule = rb_define_module("Awesome");
		rb_define_module_function(AwesomeNewModule, "say_hi", rb_module_say_hi, 0);
	}

### rb_define_const(`ruby_module_or_class`, `constant_name`, `value`)

Defines a constant under the Ruby Module or Class `ruby_module_or_class` with the name `constant_name` and value `value`.

	void Init_example()
	{
		VALUE AwesomeNewModule = rb_define_module("Awesome");
		rb_define_const(AwesomeNewModule, "UNIVERSAL_ANSWER", INT2NUM(42));
	}

### rb_define_class_under(`ruby_module`, `class_name`, `super_class`)

Defines a class under the Ruby module `ruby_module` called `class_name` and inheriting from `super_class`.

	void Init_example()
	{
		VALUE AwesomeNewModule = rb_define_module("Awesome");
		rb_define_module_function(AwesomeNewModule, "say_hi", rb_module_say_hi, 0);
		
		VALUE AwesomeClass = rb_define_class_under(AwesomeNewModule, "AwesomeClass", rb_cObject);
	}

### rb_define_singleton_method(`ruby_class`, `method_name`, `implementation`, `args`)

Defines a class method called `method_name` under the class `ruby_class`. Expects `args` arguments and calls `implementation` when invoked in Ruby.

	VALUE rb_class_say_hi(VALUE klass)
	{
	    VALUE r_hello = rb_str_new2("Hello!");
	    return r_hello;
	}
	
	void Init_example()
	{
		VALUE AwesomeNewModule = rb_define_module("Awesome");
		VALUE AwesomeClass = rb_define_class_under(AwesomeNewModule, "AwesomeClass", rb_cObject);
		
		rb_define_singleton_method(AwesomeClass, "say_hi", rb_class_say_hi, 0);
	}

	
### rb_define_method(`ruby_class`, `method_name`, `implementation`, `args`)

Defines an instance called `method_name` under the class `ruby_class`. Expects `args` arguments and calls `implementation` when invoked in Ruby.

	VALUE rb_class_say_hi(VALUE klass)
	{
	    VALUE r_hello = rb_str_new2("Hello!");
	    return r_hello;
	}
	
	void Init_example()
	{
		VALUE AwesomeNewModule = rb_define_module("Awesome");
		VALUE AwesomeClass = rb_define_class_under(AwesomeNewModule, "AwesomeClass", rb_cObject);
		
		rb_define_method(AwesomeClass, "say_hi", rb_class_say_hi, 0);
	}

### rb_ivar_get(`ruby_object`, `ivar_name`, `value`)

Sets the instance variable `ivar_name` into the object `ruby_object`

    rb_ivar_set(self, rb_str_new2("@universal_answer"), INT2NUM(42));
	
### rb_ivar_get(`ruby_object`, `ivar_name`)

Gets the instance variable `ivar_name` from the object `ruby_object`

    VALUE r_universal_answer = rb_ivar_get(self, rb_str_new2("@universal_answer"));
    // => 42


## Strings

### rb_str_new(`c_string`, `length`)

Creates a new Ruby string with the value `c_string` and length `length`.

	char *message = "Hello, World!";
	VALUE r_message = rb_str_new(message, 13);

### rb_str_new2(`c_string`)

Creates a new Ruby string with the value `string`. Length is inferred from the string, as long as it is [null terminated](http://c.learncodethehardway.org/book/ex9.html).

	char *message = "Hello, World!";
	VALUE r_message = rb_str_new2(message);
    // => Hello, World!


### rb_str_dup(`ruby_string_object`)

Creates a copy from a Ruby String object.

	char *message = "Hello, World!";
	VALUE r_message = rb_str_new2(message);
	VALUE r_message_copy = rb_str_dup(r_message);
    // => Hello, World!

	
### rb_str_plus(`ruby_string_object_1`, `ruby_string_object_2`)

Concatenates two Ruby String objects, returning a new Ruby String as a result.

    VALUE r_hello = rb_str_new2("Hello");
    VALUE r_world = rb_str_new2(", World!");
    VALUE r_hello_world = rb_str_plus(r_hello, r_world);
    // => Hello, World!
    
### rb_str_times(`ruby_string_object`, `ruby_fixnum_object`)

Repeats a Ruby String `ruby_fixnum_object` times.

	VALUE r_times = INT2NUM(16);
	VALUE r_string = rb_str_new2("Na");
	VALUE r_result = rb_str_times(r_string, r_times);
	VALUE r_batman = rb_str_plus(r_result, rb_str_new2(" Batman"));
    // => NaNaNaNaNaNaNaNaNaNaNaNaNaNaNaNa Batman!

### rb_str_substr(`ruby_string_object`, `begin`, `length`)

Grabs a substring of a Ruby String object, starting at `begin` with `length` characters.

	VALUE r_hello_world = rb_str_new2("Hello, World!");
	VALUE r_hello = rb_str_substr(r_hello_world, 0, 5);
    // => Hello

### rb_str_cat(`ruby_string_object`, `c_string`, `length`)

Takes the `c_string`'s first `length` characters and appends it to an existing Ruby String.

	VALUE r_batman = rb_str_times(rb_str_new2("Na"), INT2NUM(16));
	rb_str_cat(r_batman, " Batman!", 8);
    // => NaNaNaNaNaNaNaNaNaNaNaNaNaNaNaNa Batman!


### rb_str_cat2(`ruby_string_object`, `c_string`)

Takes the `c_string` and appends it to an existing Ruby String. Length is inferred from the string, as long as it is [null terminated](http://c.learncodethehardway.org/book/ex9.html).

	VALUE r_batman = rb_str_times(rb_str_new2("Na"), INT2NUM(16));
	rb_str_cat2(r_batman, " Batman!");
    // => NaNaNaNaNaNaNaNaNaNaNaNaNaNaNaNa Batman!
	
### rb_str_append(`ruby_string_object_1`, `ruby_string_object_2`)

Appends `ruby_string_object_2` into `ruby_string_object_1`.

	VALUE r_batman = rb_str_times(rb_str_new2("Na"), INT2NUM(16));
	rb_str_append(r_batman, rb_str_new2(" Batman!"));
    // => NaNaNaNaNaNaNaNaNaNaNaNaNaNaNaNa Batman!

### rb_str_concat(`ruby_string_object`, `ruby_object`)

Concatenates `ruby_object` into `ruby_string_object`. If `ruby_object` is a Fixnum between 0 and 255, then it is converted to a character before it is copied. Otherwise, this function behaves exactly the same as `rb_str_append`.

    VALUE r_dull_hello_world = rb_str_new2("Hello, World");
    rb_str_concat(r_dull_hello_world, INT2NUM(33)); 
    // => Hello, World!
    
### StringValueCStr(`ruby_string_object`)

Takes a Ruby String object and converts it to a NULL terminated `char *`

    VALUE r_hello_world = rb_str_new2("Hello, World!");
    char *hello_world = StringValueCStr(r_hello_world);
    // => Hello, World!
	
## Arrays

### rb_ary_new()

Creates a new Ruby Array object with 0 elements.

    VALUE r_boring_empty_ary = rb_ary_new();
    // => []
    
### rb_ary_new2(`size`)

Creates a new Ruby Array object with `size` elements.

    VALUE r_array = rb_ary_new2(10);
    
### rb_ary_store(`ruby_array`, `index`, `value`)

Stores `value` (a Ruby object) on the index `index` in the array `ruby_array`.

    VALUE r_array = rb_ary_new2(1);
    rb_ary_store(r_array, 0, rb_str_new2("Hello!"));
    // => ["Hello!"]
    
### rb_ary_push(`ruby_array`, `value`)

Stores `value` (a Ruby object) at the end of the array `ruby_array`.

    VALUE r_array = rb_ary_new2(1);
    rb_ary_push(r_array, rb_str_new2("Hello!"));
    // => ["Hello!"]
    
### rb_ary_unshift(`ruby_array`, `value`)

Stores `value` (a Ruby object) at the beginning of the array `ruby_array`.

    VALUE r_array = rb_ary_new2(2);
    rb_ary_push(r_array, rb_str_new2("World!"));
    rb_ary_unshift(r_array, rb_str_new2("Hello"));
    // => ["Hello", "World!"]
    
### rb_ary_pop(`ruby_array`)

Removes the last element off of the array `ruby_array` and returns it.

    VALUE r_array = rb_ary_new2(2);
    rb_ary_push(r_array, rb_str_new2("Hello"));
    rb_ary_push(r_array, rb_str_new2("World!"));
	VALUE r_world = rb_ary_pop(r_array);
	// => World!

### rb_ary_shift(`ruby_array`)

Removes the first element off of the array `ruby_array` and returns it.

    VALUE r_array = rb_ary_new2(2);
    rb_ary_push(r_array, rb_str_new2("Hello"));
    rb_ary_push(r_array, rb_str_new2("World!"));
	VALUE r_hello = rb_ary_shift(r_array);
	// => Hello
	
### rb_ary_entry(`ruby_array`, `index`)

Returns the array entry at index `index`

    VALUE r_array = rb_ary_new2(2);
    rb_ary_push(r_array, rb_str_new2("Hello"));
    rb_ary_push(r_array, rb_str_new2("World!"));
	VALUE r_hello = rb_ary_entry(r_array, 0);
	// => Hello

### rb_ary_dup(`ruby_array`)

Creates and returns a copy of the array `ruby_array`.

    VALUE r_array = rb_ary_new2(2);
    rb_ary_push(r_array, rb_str_new2("Hello"));
    rb_ary_push(r_array, rb_str_new2("World!"));

    VALUE r_array_copy = rb_ary_dup(r_array);
    // => ["Hello", "World!"]
    
### rb_ary_to_s(`ruby_array`)

Invokes the `to_s` on the array.

    VALUE r_array = rb_ary_new2(2);
    rb_ary_push(r_array, rb_str_new2("Hello"));
    rb_ary_push(r_array, rb_str_new2("World!"));
    VALUE r_array_str = rb_ary_to_s(r_array);
	# => "[\"Hello\", \"World!\"]"

### rb_ary_join(`ruby_array`, `ruby_string_object`)

Creates a Ruby String by converting the elements of the array `ruby_array` to Strings and joining them by `ruby_string_object`. If `Qnil` is passed, then no separator is used.

    VALUE r_array = rb_ary_new2(2);
    rb_ary_push(r_array, rb_str_new2("Hello"));
    rb_ary_push(r_array, rb_str_new2("World!"));
    VALUE r_greeting = rb_ary_join(r_array, rb_str_new2(", "));
    # => "Hello, World!"
    
### rb_ary_reverse(`ruby_array`)

Reverts the order of the array `ruby_array`.

    VALUE r_array = rb_ary_new2(2);
    rb_ary_push(r_array, rb_str_new2("Hello"));
    rb_ary_push(r_array, rb_str_new2("World!"));

    VALUE r_array_copy = rb_ary_reverse(r_array);
    // => ["World!", "Hello"]
    
### rb_ary_to_ary(`ruby_object`)

Same as calling `Array()` in Ruby.

    VALUE r_value = INT2NUM(42);
    VALUE r_array = rb_ary_to_ary(r_value);
    // => [42]
 
## Hashes

### rb_hash_new()

Creates a new Ruby Hash

	VALUE r_hash = rb_hash_new();
	// => {}
	
### rb_hash_aset(`ruby_hash`, `key`, `value`)

Sets the `key` as `value` in the hash `ruby_hash`. All of the 3 parameters have to be Ruby objects.

	VALUE r_hash = rb_hash_new();
	rb_hash_aset(r_hash, rb_str_new2("universal_answer"), INT2NUM(42));
	// => {"universal_answer" => 42}
	
### rb_hash_aref(`ruby_hash`, `key`)

Gets the value under `key` in the hash `ruby_hash`. Both parameters have to be Ruby objects.

	VALUE r_hash = rb_hash_new();
	rb_hash_aset(r_hash, rb_str_new2("universal_answer"), INT2NUM(42));
	
	VALUE r_universal_answer = rb_hash_aref(r_hash, rb_str_new2("universal_answer"));
	// => 42

## Blocks

### rb_block_proc()

Returns the proc that was given to the method.

    if(rb_block_given_p()) {
        VALUE r_proc = rb_block_proc();
    }

### rb_block_given_p()

Returns 1 if a block was given to the method. Otherwise, returns 0.

    if(rb_block_given_p()) {
        VALUE r_proc = rb_block_proc();
    }
    
### rb_yield(`ruby_object`)

Yields `ruby_object`.

    if(rb_block_given_p()) {
        VALUE r_val = INT2NUM(42);
        r_yield(r_val);
    }

### rb_yield_values(`ruby_object`, `ruby_object`, ...)

Yields the values.

    if(rb_block_given_p()) {
        r_yield_values(INT2NUM(42), DBL2NUM(3.1415));
    }
