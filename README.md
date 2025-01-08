# grammar

目前支持的语法

```bnf
declaration -> varDeclaration
            | statement

varDeclaration -> 'var' IDENTIFIER ('=' expression)? ';'
statement   -> printStatement
            | forStatement
            | ifStatement
            | whileStatement
            | expressionStatement
            | block

printStatement -> 'print' expression ';'
forStatement -> 'for' '(' (varDeclaration | expressionStatement | ';') expression? ';' expression? ')' statement
ifStatement -> 'if' '(' expression ')' statement ('else' statement)?
whileStatement -> 'while' '(' expression ')' statement
expressionStatement -> expression ';'
block -> '{' declaration* '}'

expression -> literal
            | unary
            | binary
            | grouping
            | variable

literal -> NUMBER 
          | STRING 
          | 'true' 
          | 'false' 
          | 'nil'

unary -> ('-' | '!') expression

binary -> expression operator expression

operator -> '==' 
          | '!=' 
          | '<' 
          | '<=' 
          | '>' 
          | '>=' 
          | '+' 
          | '-' 
          | '*' 
          | '/' 
          | 'or' 
          | 'and'
grouping -> '(' expression ')'
variable -> IDENTIFIER
IDENTIFIER -> ALPHA (ALPHA | DIGIT)*
NUMBER -> DIGIT+
STRING -> '"' (CHAR | ESCAPE)* '"'
```
