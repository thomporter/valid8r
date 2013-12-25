# HAPPY HOLIDAYS & A WONDERFUL NEW YEAR TO ALL

![Valid8r - Validation for multiple programming languages.](https://raw.github.com/thomporter/valid8r/master/logo.png)

# Valid8r

Valid8r is a validation library written for multiple programming languages, but
all using a common configuration file.  Create a single JSON file defining
rules for fields, and then use that file to validate data in the browser 
via JavaScript, or on the server via PHP or Node (other 
languages may be added in the future.)

## Demo

You can see the Kitchen Sink demo running live at: 

http://thomporter.com/valid8r

Links to both JavaScript enabled, and PHP Server-Side only validation are there.
I'll be adding Mocha browser tests to that when I get them done.

## Quick Example

Here is a quick and dirty example of using Valid8r to on `form` data.  We'll
start with a form: 

	<form id="my_form">
		<input type="text" id="my_field" name="my_field">
		<input type="submit" value="Submit">
	</form>
	
Now we'll setup our JSON configuration.  We'll require that `my_field` be
filled in, and at least 5 characters long.

	{
		"my_field": {
			"rules": [
				{ "rule": "required" },
				{ "rule": "len", "min": 5 },
			]
		}
	}

Now we can use this configuration to validate the form in...

JavaScript:

	$(function(){
		var validator = new Valid8r({
			
			// instruct Valid8r to validate the form in real-time 
			bindToBlur: true, 
			
			// a selector for our form (required to prevent submit)
			form: "#my_form",
			
			// our callback function to deal with errors, 
			// for this simple demo, we'll just alert the result:
			callback: function(field, err) {
			  
			  if (err)  alert(field + ': ' + err);
			  else alert('Validation Passed');
			}
		});
		
		// I'll load the rules via AJAX here, but you could include them using
		// server side code (and should if you can!)
		$.get('./form-rules.json', function(rules) {
			validator.setRules(rules);		
		}, 'json');
	});
	
PHP:

	<php
	require 'Valid8r/Valid8r.php';
	$validator = new Valid8r();
	$rules = json_decode(file_get_contents('form-rules.json'));
	$validator->setRules($rules);
	$errors = $v->validateFields($_POST);
	$success = empty($errors);
	?>
	

### MoreExamples

Each language has it's own `examples` folder, which for now just has the
Kitchen Sink demo.  See each language's documentation for more.

#### For JavaScript: 

Clone the [JavaScript repo](https://github.com/thomporter/valid8r_js) and run npm install & start in the examples folder:

	git clone https://github.com/thomporter/valid8r_js
	cd valid8r_js/examples
	npm install
	npm start

Then you can access http://localhost:3737 to see the examples.

You can also setup the JavaScript examples folder to run under a web server
you already have setup - eg, the JavaScript Kitchen Sink Demo on my website
is running under Apache.

#### For Node:

Clone the [Node repo](https://github.com/thomporter/valid8r_node), change into the directory and run npm install & start:

	git clone https://github.com/thomporter/valid8r_node
	cd valid8r_node
	npm install
	npm start

Then you can access http://localhost:3737 to see the examples. 

#### For PHP: 

The PHP repo includes an examples directory.  Set it up on an PHP server and
you can test it out.  I've only tested it under Apache, but any PHP environment
*should* work.  If you have any issues, please let me know.  
(NOTE: JavaScript is disabled in the PHP example.)

## Installation

Both PHP & JavaScript require only one file.  You'll find them in the dist 
folder.

Note: The PHP class is name-spaced for PHP5 - feel free to change that to suit 
your needs.

Alternatively, you can clone the repos you need:

	git clone https://github.com/thomporter/valid8r_js
	git clone https://github.com/thomporter/valid8r_php
	git clone https://github.com/thomporter/valid8r_node

Or use a package manager:

### Installing Valid8r for JavaScript via Bower

`bower install valid8r_js`

### Installing Valid8r for Node via npm

`npm install valid8r_node`

### Installing Valid8r for PHP via Composer

	{
	  "require": {
	    "valid8r/valid8r_php": "v0.0.2"
	  }
	}
	

## Configuration

Valid8r uses JSON for configuration, making it simple to use across
multiple languages and platforms.  The structure of the object is fairy simple:

	{
	  "field": { // unique key that is also the key in the data array, eg $_POST for PHP.
	    "selector": "jQuerySelector", // optional, used only for JavaScript in browser
	    "rules": [ // required - array of validation rules to run against data in field.
	      {"rule": "rule_name"},
	      {"rule": "another_rule_name", "someRuleOption": true},
	    ]
	  }
	}

That's it.  Nothing much to the rule files, they are simple and clean.
  
**JavaScript Updated in v0.0.4:** It is no longer necessary to define the `selector` 
property if you have an ID on the input that is the same as `field`.  eg:

	&lt;input type="text" name="my_field" id="my_field" />
	
Can be defined as:

	{
	  "my_field": {
	    "rules": [...]
	  }
	}

## Valid8r Rules

Here we will document the rules for Valid8r.  These are available in all languages.
For simplicity's sake, the examples below are of just the `rule` object - not much
use unless you put it in a `rules` array. 

<a name="required"></a>
### required

The `required` rule requires that a field be filled in, selected or checked.

	{"rule": "required"}
	
### len

The `len` rule allows you to validate the length of a field:

	{"rule": "len", "min": 5} // require min 5 characters.
	{"rule": "len", "max": 20} // require max 20 characters.
	{"rule": "len", "min": 5, "max": 20} // require min 5 & max 20 characters.

### val

Validate your data for a particular numeric value using the `val` rule:

	{"rule":"val","min":5} // accept any number greater than 4
	{"rule":"val","max":10} // accept any number lower than 11
	{"rule":"val","min":5,"max":10} // accept any number from 5-10
	{"rule":"val", "outside":[5,10]} // accept any number less than 5 and greater than 10
	
### isAlpha

The `isAlpha` rule requires all characters in the field be alphabetic (A-z)

	{"rule": "isAlpha"}
	
### isNum

The `isNum` rule requires all characters in the field be numeric (0-9)

	{"rule": "isNum"} // allows negative numbers
	{"rule": "isNum", "nonNeg": true} // requires a non-negative number

### isAlnum

The `isAlnum` rule requires all characters in the field be alphanumeric (A-z,0-9)

	{"rule": "isNum"}

### formatted_as

The `formatted_as` rule allows you to specify a format for a string.  
A = alpha, D = digit, and you can use symbols:

	{"rule": "formatted_as", "format": "DD/DD/DDDD"}
	{"rule": "formatted_as", "format": "(DDD) DDD-DDDD"}
	{"rule": "formatted_as", "format": "AADDAADDDDDD"}

### regex

Create your own regular expressions to validate with using the `regex` rule.

	{"rule":"regex", pattern:"[a-Z0-9.-]{2,7}", modifiers:"i"}
	
### email

Validate email addresses:

	{"rule": "email"}
	{"rule": "email", "validator": "simple" } // very basic validator, will let a lot fly
	{"rule": "email", "validator": "default" } // the default validator, works well. 
	{"rule": "email", "validator": "rfc5322" } // full blown RFC 5322 RegExp - not tested yet 

the `validator` property defaults to "default" if not specified.

### url

Use the `url` rule to validate URLs, with or without protocols:

	{"rule": "url"} // validates a url, allowing any protocol (even made up ones.)
	{"rule": "url", "protocols": ["http","https","ftp","git"]} // set the protocols to accept
	{"rule": "url", "noProtocols": true} // require there be no protocols 

**TODO:** 

* Add `host` property requiring the url be of a particular host.
* Add `domain` property requiring host be of particular domain (less restrictive than the `host` option.)


### checks

Use the `checks` rule to validate that a certain number of checkboxes have been 
checked.

Example Rules:

	{"rule": "checks"} // requires at least one be checked
	{"rule": "checks", "min": 3} // requires at least 3 boxes be checked
	{"rule": "checks", "max": 5} // requires at no more than 5 boxes be checked
	{"rule": "checks", "min": 3, "max": 5} // requires between 3 and 5 boxes be checked

*SERVER SIDE NOTE:* You must properly name your checkboxes with the array 
notation ([]) at the end of the name, eg:

	<input type="checkbox" name="my_checkboxes[]" value="1" />
	<input type="checkbox" name="my_checkboxes[]" value="2" />

### radio

Use the `radio` rule to require one of the radio buttons in the set have been 
checked.

Example Rules:

	{"rule": "radio"} // requires one of the radios be checked.

### Custom Validators

You can create a custom validator by using the `custom` rule.  You must 
additionally include a `func` property that either contains the function, or 
the name of the function.

Example Rules:

	{"rule": "custom", "func": "my_custom_validator" }
	
Please review the language-dependent documentation for details about how to 
declare your functions.

#### Note

I am uncertain about the "containing the function" idea - you can't store a 
function in a JSON file, and since our JSON might be parsed by PHP or other 
languages, how would that function look?  No, instead I see it might be 
possible to load your JSON file, and attach the functions, perhaps like so in 
JavaScript:
	
	$.get('/my-config.json', function(config) {
		config.some_field.rules[0].func = function(field, value) { ... }
		validator.setRules(config)
	}
	
This seems like a BAD idea to me.  First, how I've done it above, it better be 
the first rule.  Of course, you could iterate over the rules array and find the 
one with the `rule` property set to `custom` and then proceed, but now things 
are getting messy. I believe it better to declare your functions in the global 
scope, and name them well: `acmeValidator_firstName` or whatever.

**Updated** - The JavaScript and Node versions both accept a customValidators
property on the main configuration object you pass to Valid8r.  This is the
preferred way of defining custom validators.  JavaScript Example:

	var validator = new Valid8r({
		customValidators: {
			myValidator: function(field, value) { ... },
			myOhterValidator: function(field, value) { ... }
		},
		...
	});


### Conditional Validation

It is possible to attach conditional validation to any of the rules in your 
validation scheme.  Here is how:

	{
		"my_field": {
			"conditions": {
				"condition_key": {
					"field": "condition_field_id",
					"is": "required value to satisfy condition"
				}
			},
			rules: [
				{"rule":"required", "when":"condition_key"}
			]
		}
	}
	

Lets take a look.  First, we've declared a `conditions` object on our main 
validation object for "my_field".  We declare a `condition_key` property, 
which describes a `field` and value (`is`) that must be satisfied for the 
condition to be true.  Next, our `rules` array defines a single rule, 
`required`, with the `when` property set to our self-defined `condition_key`.

So, in order for the `required` rule to be tested, the `condition_key` condition
must be satisfied, or more verbosely, the field with key `condition_field_id`
must have the value `required value to satisfy condition`.

How about a more real-world example.  Say you have a registration form.  On
it, you want to require someone say "Yes" or "No" to a question, let's say, 
"Do you have a website?".  If they say yes, we want to require they enter
their website into another field.  So we'll have 2 radio buttons named
`have_website`, one with value "Yes", the other value "No".  Next we'll have
a text input, with the ID of `url`.  Here's the rules:

{
	"url": {
		"conditions": {
			"have_website": {
				"field": "have_website",
				"is": "Yes"
			}
		},
		"rules": [
			{"rule": "required", "when": "have_website"},
			{"rule": "url", "when": "have_website"}
		]
	}
}

I plan to rework the libraries to allow for globally defined conditions, so you 
can easily disable validation across multiple fields without having to re-define
the conditions over and over.

## TODO

* Test the "Quick Example" code.
* Mocha Browser Tests
* AMD/Require browser support for JavaSript module.
* AMD support for the node module.
* Add ability to create "global conditionals"

