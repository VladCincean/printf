# printf

Some ideas...

#### man 3 __printf__
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

#### man 3 __stdarg__

Variable argument lists

```
#include <stdarg.h>

void va_start(va_list ap, last);
type va_arg(va_list ap, type);
void va_end(va_list ap);
void va_copy(va_list dest, va_list src);
```

A function may be called with a varying nr. of args of varying types.
- __va list__ (type) + 3 macros (__va start__, __va arg__, __va end__) for stepping through a list of arguments whose number and types are *not* known to the called function

The called function must declare an object of type __va list__ which is used by the 3 macros.

##### va\_start()
- initializes __ap__ for subsequent use by __va arg()__ and __va end()__
- must be called first
- the argument __last__ = the name of the last argument before the variable argument list (i.e. the last argument of which the calling function knows the type)
- it should __not__ be declared as a register variable, as a function or as an array type (its address may be used in __va start__ macro)

##### va\_arg()
- expands to an expression that has the *type* and *value* of the next argument in the call
- arg. __ap__ is the __va list ap__initialized by __va start()__
- each call to __va arg()__ modifies __ap__ so that the next call returns the next argument
- arg. __type__ is a type name specified so that the type of a pointer to an object that has the specified type can be obtained simply by adding a \* to __type__
- the first use of the __va arg()__ macro after that of the __va start()__ macro returns the argument after __last__; successive invocations return the values of the remaining arguments
- if there is no next argument, or if __type__ is not compatible with the type of the actual next argument, random errors will occur `:))`
- if __ap__ is passed to a function that uses __va arg(*ap*, *type*)__, then the value of __ap__ is undefined after the return of that function

##### va\_end()
- must match with the corresponding invocation of __va start()__
- after the call __va end(*ap*)__ the variable __ap__ is undefined
- multiple traversals of the list, each backtracked by __va start/end()__ are possible
- __va end()__ may be a macro or a function

##### va\_copy()
- copies the (previously initialized) variable argument list __src__ to __dest__
- [...] *see man*

##### example
The function __foo__ takes a string of format characters and prints out the argument associated with each format character based on the type.

```
#include <stdio.h>
#include <stdarg.h>

void foo(char	*fmt, ...)
{
	va_list	ap;
	int		d;
	char	c;
	char	*s;

	va_start(ap, fmt);
	while (*fmt) {
		switch(*fmt++) {
		case 's':		/* string */
			s = va_arg(ap, char *)
			printf("string %s\n", s);
			break;
		case 'd':		/* int */
			d = va_arg(ap, int);
			printf("int %d\n", d);
			break;
		case 'c':		/* char */
			/* need a cast here since va_arg only takes fully promoted types */
			c = (char)va_arg(ap, int);
			printf("char %c\n", c);
			break;
		}
	}
	va_end(ap);
}
```
