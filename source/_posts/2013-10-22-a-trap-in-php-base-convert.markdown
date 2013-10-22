---
layout: post
title: "A trap in php base_convert"
date: 2013-10-22 01:37
comments: true
categories: PHP
---

Today I find an interesting thing: a php function [base_convert](http://us1.php.net/manual/en/function.base-convert.php)
{% codeblock convert.php %}
$a = 0x80;
var_dump(base_convert($a, 16, 2));
{% endcodeblock %}
No surprises:
    string(9) "100101000"
Let's go on!
{% codeblock convert.php %}
$a = 0x80;
var_dump(base_convert($a, 16, 2));
$b = ~$a;
var_dump(base_convert($b, 16, 2), $b);
{% endcodeblock %}
output:
    string(9) "100101000"
    string(9) "100101001"
    int(-129)

Hmm,it seems strange that binary *__100101001__* not equas decimal *__-129__*, but the result: -129 is the right answer,what happened?
<!--more-->
Then I reading the source code of this function [base_convert](https://github.com/php/php-src/blob/master/ext/standard/math.c#L1065) The Unscientific-real function is [_php_math_basetozval](https://github.com/php/php-src/blob/master/ext/standard/math.c#L839)


{% codeblock math.c %}
	for (i = Z_STRLEN_P(arg); i > 0; i--) {
		c = *s++;

		/* might not work for EBCDIC */
		if (c >= '0' && c <= '9') 
			c -= '0';
		else if (c >= 'A' && c <= 'Z') 
			c -= 'A' - 10;
		else if (c >= 'a' && c <= 'z') 
			c -= 'a' - 10;
		else
			continue; //Evil continue...

		if (c >= base)
			continue;
		
		switch (mode) {
		case 0: /* Integer */
			if (num < cutoff || (num == cutoff && c <= cutlim)) {
				num = num * base + c;
				break;
			} else {
				fnum = num;
				mode = 1;
			}
			/* fall-through */
		case 1: /* Float */
			fnum = fnum * base + c;
		}	
	}
{% endcodeblock %}

Read here do not complain: God is Fair.
Another PHP functions can help us solve this problem:printf or sprintf.

if you realy want to see the binary of *_-129_*, please use these functions like this:

{% codeblock convert.php %}
$a = 0x80;
var_dump(base_convert($a, 16, 2));
$b = ~$a;
var_dump(base_convert($b, 16, 2), $b);
printf("%b", $b);
echo "\n";
{% endcodeblock %}
Please verify the conclusion by yourself :)
