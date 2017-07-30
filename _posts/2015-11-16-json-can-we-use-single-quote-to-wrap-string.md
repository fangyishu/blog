---
layout: post
title:  "JSON: Can We Use Single Quotes to Wrap String?"
date:   2015-11-16 +8000
categories: json JavaScript
---

The answer is **NO**.

Any strict JSON parser will report an error on this: 

    > JSON.parse("{'hello': 'world'}"); 
       
    VM108:1 Uncaught SyntaxError: Unexpected token ' in JSON at position 1
        at JSON.parse (<anonymous>)
        at <anonymous>:1:6
        
    > JSON.parse('{"hello": "world"}');
    {hello: "world"}

Because the standard ([json.org][json-web], [ECMA-404][ecma404]) said so:

> A string is a sequence of Unicode code points wrapped with quotation marks (U+0022).

And... U+0022 is double quote (").

This image also comes from the standard:

![JSON string][json-string]

This rule seems to be too strict, just like JSON doesn't have comment.
Maybe because Douglas Crockford wants to keep JSON to be extremely simple. 
(Note: JSON format was first specified by him in 2007, and he is the author of the famous book: 
*[JavaScript: The Good Parts][js-good-parts]*.)

BTW, tab indentation in YAML is forbidden, you can only use spaces ([see this][yaml-space]), strange... 


[json-web]: http://www.json.org/
[ecma404]: http://www.ecma-international.org/publications/files/ECMA-ST/ECMA-404.pdf
[json-string]: http://www.json.org/string.gif
[js-good-parts]: http://shop.oreilly.com/product/9780596517748.do 
[yaml-space]: https://stackoverflow.com/questions/19975954/a-yaml-file-cannot-contain-tabs-as-indentation
