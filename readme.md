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

## example hlwasm program

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

## example wat (to be translated from hlwasm program)

	(module
	  (func $factorial (param $n i32) (result i32)
	    (if (result i32)
	      (i32.eqz (get_local $n))
	      (then (i32.const 1))
	      (else
	        (i32.mul (get_local $n)
	                 (call $factorial (i32.sub (get_local $n) (i32.const 1))))
	      )
	    )
	  )
	  (func $main
	    (local $input i32)
	    (local $result i32)
	    (set_local $input (i32.const 5))
	    (set_local $result (call $factorial (get_local $input)))
	    (call $print
	      (call $concat
	        (call $concat
	          (call $concat (i32.const 12) (i32.const 70))
	          (i32.const 97)
	        )
	        (call $concat (i32.const 99) (call $concat (i32.const 116) (i32.const 111)))
	      )
	    )
	  )
	  (func $print (param $strptr i32))
	  (func $concat (param $strptr1 i32) (param $strptr2 i32) (result i32))
	  (export "factorial" (func $factorial))
	  (export "main" (func $main))
	  (export "print" (func $print))
	  (export "concat" (func $concat))
	)

## utility functions: `print` and `concat`

	(func $print (param $strptr i32)
	  (call $puts (get_local $strptr))
	)

	(func $concat (param $strptr1 i32) (param $strptr2 i32) (result i32)
	  (local $len1 i32)
	  (local $len2 i32)
	  (local $result i32)
	  (local $i i32)

	  ;; Get length of first string
	  (set_local $len1 (call $strlen (get_local $strptr1)))

	  ;; Get length of second string
	  (set_local $len2 (call $strlen (get_local $strptr2)))

	  ;; Allocate memory for result string
	  (set_local $result (call $malloc (i32.add (get_local $len1) (get_local $len2))))

	  ;; Copy first string into result string
	  (block
	    (set_local $i (i32.const 0))
	    (loop
	      (br_if $concat_end (i32.ge_u (get_local $i) (get_local $len1)))
	      (i32.store8
	        (i32.add (get_local $result) (get_local $i))
	        (i32.load8_u (i32.add (get_local $strptr1) (get_local $i)))
	      )
	      (set_local $i (i32.add (get_local $i) (i32.const 1)))
	      (br $concat_loop)
	    )
	  )

	  ;; Copy second string into result string
	  (block $concat_loop
	    (set_local $i (i32.const 0))
	    (loop
	      (br_if $concat_end (i32.ge_u (get_local $i) (get_local $len2)))
	      (i32.store8
	        (i32.add (get_local $result) (i32.add (get_local $len1) (get_local $i)))
	        (i32.load8_u (i32.add (get_local $strptr2) (get_local $i)))
	      )
	      (set_local $i (i32.add (get_local $i) (i32.const 1)))
	      (br $concat_loop)
	    )
	  )

	  ;; Null-terminate the result string
	  (i32.store8 (i32.add (get_local $result) (i32.add (get_local $len1) (get_local $len2))) (i32.const 0))

	  ;; Return pointer to result string
	  (get_local $result)

	  (block $concat_end)
	)
