# grammar

目前支持的语法

```
declaration -> varDeclaration
            | statement
statement   -> printStatement
            | expressionStatement
            | block

block -> '{' declaration* '}'

printStatement -> 'print' expression ';'
expressionStatement -> expression ';'
varDeclaration -> 'var' IDENTIFIER ('=' expression)? ';'

expression -> literal
            | unary
            | binary
            | grouping
            | variable

literal -> NUMBER | STRING | 'true' | 'false' | 'nil'
unary -> ('-' | '!') expression
binary -> expression operator expression
operator -> '==' | '!=' | '<' | '<=' | '>' | '>=' | '+' | '-' | '*' | '/' | 'or' | 'and'
grouping -> '(' expression ')'
variable -> IDENTIFIER

IDENTIFIER -> ALPHA (ALPHA | DIGIT)*
NUMBER -> DIGIT+
STRING -> '"' (CHAR | ESCAPE)* '"'
```
