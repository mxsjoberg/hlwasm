# hlwasm

A High-Level WebAssembly programming language.

## grammar

	<program> ::= <function> | <program> <function>

	<function> ::= <function_declaration> <block>

	<function_declaration> ::= 'function' <identifier> '(' <parameters> ')' ':' <return_type>

	<parameters> ::= <parameter> | <parameter> ',' <parameters>

	<parameter> ::= <identifier> ':' <type>

	<return_type> ::= <type> | 'void'

	<block> ::= '{' <statement>* '}'

	<statement> ::= <assignment> | <expression> | <return> | <if_statement> | <while_statement> | <block>

	<assignment> ::= <identifier> '=' <expression>

	<expression> ::= <binary_operation> | <unary_operation> | <literal> | <identifier> | <function_call>

	<binary_operation> ::= <expression> <binary_operator> <expression>

	<unary_operation> ::= <unary_operator> <expression>

	<literal> ::= <integer> | <float> | <boolean> | <string>

	<if_statement> ::= 'if' <expression> <block> ('else' <block>)?

	<while_statement> ::= 'while' <expression> <block>

	<binary_operator> ::= '+' | '-' | '*' | '/' | '%' | '==' | '!=' | '<' | '>' | '<=' | '>=' | '&&' | '||'

	<unary_operator> ::= '-' | '!' 

	<function_call> ::= <identifier> '(' <arguments> ')'

	<arguments> ::= <expression> | <expression> ',' <arguments>

	<type> ::= 'int' | 'float' | 'bool' | 'string' | <identifier>

	<identifier> ::= <letter> <alphanumeric>*

	<integer> ::= <digit>+

	<float> ::= <digit>+ '.' <digit>*

	<boolean> ::= 'true' | 'false'

	<string> ::= '"' <any_character>* '"'

	<letter> ::= 'a' | 'b' | ... | 'z' | 'A' | 'B' | ... | 'Z'

	<digit> ::= '0' | '1' | ... | '9'

	<alphanumeric> ::= <letter> | <digit>

## example

	function factorial(n: int): int {
	  if (n == 0) {
	    return 1;
	  } else {
	    return n * factorial(n - 1);
	  }
	}

	function main() {
	  let input: int = 5;
	  let result: int = factorial(input);
	  print("Factorial of " + input + " is " + result);
	}
