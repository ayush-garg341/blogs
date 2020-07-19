---
title: "JSON Vs YAML"
date: 2020-07-19 23:05:00 +07:00
tags: [json, jsvascript, yml]
description: The blog simply describes the pros and cons of both json and yml format.
twitter: https://twitter.com/ayush_garg341
---

Any person working in tech industry knows what **JSON** is, but few people are aware with new **YAML** format. So today let's cover up these two in detail and know their pros and cons.

Both formats are also known for **data serialization** process. According to <a href="https://devopedia.org/data-serialization" target="_blank">Devopedia</a> data serialization is the process of converting data objects present in complex data structures into a byte stream for storage, transfer, and distribution purposes on physical devices. So JSON and YAML both are a way of storing the data objects and structures in files.

**JSON**

Stands for **Javascript Object Notation** and is a subset of Javascript programming language. JSON has a standard format for data storing. It stores data in key/value pairs. The records are separated by commas and both field names and values are enclosed within double-quotes.

```
{
	"name":"Ayush Garg",
	"designation":"Software Engineer",
	"place":"New Delhi",
	"hobbies":["gymming", "blogging"],
	"brother": {
		   "name":"XYZ",
		   "age":"21"
	}
}
```

**YAML**

Stands for **YAML Ain’t Markup Language**. YAML uses three dashes `(“---”)` to indicate the start of a document and three dots `(“...”)` to indicate the end of a document. Use indentations to show the levels in data.

```
---
first_name: Ayush
last_name: Garg
designation: "Software Engineer"
place: "New Delhi"
hobbies:
	- gymming
	- blogging
brother:
	name: XYZ
	age: 21

# Here is a comment.
```

**Pros & Cons - JSON vs YAML**

###### *1. Readablitiy & Complexity*
The design of JSON is simpler and universally usable, hence this has reduced the readability of the data up to some extent. While design goal of YAML is to provide a good human-readable format and provide support for serializing arbitrary native data structures.

It is said that YAML is a superset of JSON. What is simply meant by this is that we can parse JSON using a YAML parser but not the other way around. However, in practical scenarios, this parsing may rise problems, but it is theoretically possible.


###### *2. Serialization Performance*
Here JSON is the winner because of the ability to quickly and easily parse JSON serialized data with its simpler design. And this has made JSON more popular among devs.

###### *3. Community Support*
<a href="https://www.json.org/json-en.html" target="_blank">JSON</a> can be easily integrated with any programming language, because of its popularity. While <a href="https://yaml.org/" target="_blank">YAML</a> also has widespread support but not as much as JSON.

###### *4. Comments*
YAML supports comments while JSON does not. We can comment anywhere in the document with a simple *#* character. This has proven advantageous when writing configuration files where one developer can easily describe the configuration using the comments. Therefore YAML format is used in many technology stacks like ElasticSearch, Docker for storing configuration information.

###### *5. Ability to use complex structures*
Another feature that YAML provides is the ability to reference other data objects. With this referencing, it is possible to write recursive data in the YAML file.
For this, we can define Anchors in the YAML file using “&” and refer to them later using Aliases, “*”. This is a very important feature in YAML that JSON does not offer. This is why serialization performance is low in YAML. In JSON, it is impossible to serialize complex structures with object references. 

```
YAML can solve a little bit of the DRY problem too.

==========YAML==========
---
foo: &anchor
 K1: "One"
 K2: "Two"

bar: *anchor

=========JSON============
{
    "foo": {
        "K1": "One",
        "K2": "Two"
    },
    "bar": {
        "K1": "One",
        "K2": "Two"
    }
}

==========YAML=========
---
foo: &anchor
  K1: "One"
  K2: "Two"

bar:
  <<: *anchor
  K2: "I Changed"
  K3: "Three"


========JSON===========
{
    "foo": {
        "K1": "One",
        "K2": "Two"
    },
    "bar": {
        "K1": "One",
        "K2": "I Changed",
        "K3": "Three"
    }
}

The << marker means extend, so <<: *anchor means, basically, inject anchor into bar, then extend.

========YAML=========
---
foo:
 <<: &anchor
   K1: "One"
 K2: "Two"

bar:
 <<: *anchor
 K3: "Three"


=========JSON=========
{
    "foo": {
        "K1": "One",
        "K2": "Two"
    }, 
    "bar": {
        "K1": "One",
        "K3": "Three"
    }
}

```

**Bonus Section**

###### *Serializing Javascript Objects*
Object serialization is the process of converting an object’s state to a string from which it can later be restored. ES5 provides native functions JSON.stringify() and JSON.parse() to serialize and restore JavaScript objects.
```
o = {x:1, y:{z:[false,null,""]}}; // Define a test object
s = JSON.stringify(o);            // s is '{"x":1,"y":{"z":[false,null,""]}}'
p = JSON.parse(s);                // p is a deep copy of o
```

JSON syntax is a subset of JavaScript syntax, and it cannot represent all JavaScript values. Objects, arrays, strings, finite numbers, true, false, and null are supported and can be serialized and restored. NaN, Infinity, and -Infinity are serialized to null. Date objects are serialized to ISO-formatted date strings (see the Date.toJSON() function), but JSON.parse() leaves these in string form and does not restore the original Date object. Function, RegExp, and Error objects and the undefined value cannot be serialized or restored. JSON.stringify() serializes only the enumerable.

*This is the end of this article on YAML and JSON. I hope you got to learn something new from this article. Stay in touch. Please follow on <a href="{{page.twitter}}" target="_blank">Twitter</a>*