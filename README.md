# printf

Some ideas...

#### man 3 printf
##### Format of the format string
The format string is a character string, beginning and ending in its initial shift state, if any. The format string is composed of zero or more directives:
* ordinary characters (not %) -> they are copied unchanged to the output stream
* conversion specifications, each of which results in fetching zero or more subsequent arguments
	* introduced by "%"
	* ended with a conversion specifier
	* in between there may be (in this order)
		- (0 or more) flags
		- (optional) minimum field width
		- (optional) precision
		- (optional) length modifier

##### Flag characters
The character '%' is followed by 0 or more flags:
* #
* 0
* -
* +
* space
* (bonus) *
* (bonus) $
* (bonus) L
* (bonus) '
* (bonus+) %b to print in binary
* (bonus+) %r to print a string of non-printable characters
* (bonus+) %k to print a date in any ordinary ISO format, etc.

##### Field width (optional)
- is a decimal digit string (first digit != 0)
- specifies a minimum field width
- if the converted value has fewer characters than FW, it will be padded with spaces on the left (or right, if the left-adjustment flag has been given)
- instead of a decimal digit string one may write * to specify that the FW is given in the next argument
- a negative field width is taken as a '-' flag followed by a positive field width
- if the result of a conversion is wider than the field width, the field is expanded (so, no truncation of the field)

##### Precision (optional)
- '.' followed by an optional (default = 0) decimal digit string
- instead of a decimal digit string one may write * to specifi that the precision is given in the next argument
- negative precision = taken as if the precision was omitted
- this gives:
	- the minimum nr. of digits to appear for __d__, __i__, __o__, __u__, __x__. __X__ conversions
	- the nr. of digits that appear after a radix character for __a__, __A__, __e__, __E__, __f__, __F__ conversions
	- the nr. of significant digits for __g__ and __G__ conversions
	- the maximum nr. of characters to be printed from a string for __s__ and __S__ conversions

##### Length modifier
* hh
* h
* l
* ll
* j
* z

##### Conversion specifiers
- a character that specifies the type of conversion to be applied
* sSpdDioOuUxXcC
* (bonus) eE, fF, gG, aA, n

##### Return value
- success -> the nr. of characters printed (excluding '\0')
- error -> a negative value is returned
