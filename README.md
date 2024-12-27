# my-clox

code from https://craftinginterpreters.com/

17

Parsers for tokens We map each token type to a different kind of expression. We
define a function for each expression that outputs the appropriate bytecode.
Then we build an array of function pointers. The indexes in the array correspond
to the TokenType enum values, and the function at each index is the code to
compile an expression of that token type.

17 . 6A Pratt Parser

The Pratt parser is a top-down operator precedence parser. It’s a simple and
efficient way to parse expressions. It’s also very flexible and can handle a
wide variety of grammars. The parser is named after Vaughan Pratt, who first
described it in 1973. It’s also known as a precedence climbing parser. The
parser is recursive and uses a table of functions to handle different precedence
levels. Each function is responsible for parsing expressions at a specific
precedence level. The parser is called a top-down parser because it starts at
the top of the expression and works its way down. It’s called an operator
precedence parser because it uses the precedence of operators to guide the
parsing process. The parser is also called a Pratt parser because it was
popularized by Pratt in his paper “Top Down Operator Precedence”.

实现一个简单的算术表达式解析器，支持加减乘除和括号。

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

typedef enum {
    TOKEN_NUMBER,
    TOKEN_PLUS,
    TOKEN_MINUS,
    TOKEN_MULTIPLY,
    TOKEN_DIVIDE,
    TOKEN_LPAREN,
    TOKEN_RPAREN,
    TOKEN_EOF,
} TokenType;

typedef struct {
    TokenType type;
    double value;
} Token;

typedef struct {
    const char* start;
    const char* current;
} Scanner;

typedef struct {
    Token current;
    Scanner scanner;
} Parser;

void advance(Scanner* scanner) {
    scanner->current++;
}

char peek(Scanner* scanner) {
    return *scanner->current;
}

int is_at_end(Scanner* scanner) {
    return *scanner->current == '\0';
}

void init_scanner(Scanner* scanner, const char* source) {
    scanner->start = source;
    scanner->current = source;
}

void init_parser(Parser* parser, const char* source) {
    init_scanner(&parser->scanner, source);
    parser->current.type = TOKEN_EOF;
    parser->current.value = 0;
}

void init_token(Token* token, TokenType type, double value) {
    token->type = type;
    token->value = value;
}

Token make_token(TokenType type, const char* start, const char* current) {
    Token token;
    token.type = type;
    token.value = strtod(start, NULL);
    return token;
}

Token scan_token(Scanner* scanner) {
    while (!is_at_end(scanner)) {
        scanner->start = scanner->current;
        switch (peek(scanner)) {
            case '+': advance(scanner); return make_token(TOKEN_PLUS, scanner->start, scanner->current);
            case '-': advance(scanner); return make_token(TOKEN_MINUS, scanner->start, scanner->current);
            case '*': advance(scanner); return make_token(TOKEN_MULTIPLY, scanner->start, scanner->current);
            case '/': advance(scanner); return make_token(TOKEN_DIVIDE, scanner->start, scanner->current);
            case '(': advance(scanner); return make_token(TOKEN_LPAREN, scanner->start, scanner->current);
            case ')': advance(scanner); return make_token(TOKEN_RPAREN, scanner->start, scanner->current);
            case ' ': advance(scanner); break;
            default:
                if (peek(scanner) >= '0' && peek(scanner) <= '9') {
                    while (peek(scanner) >= '0' && peek(scanner) <= '9') {
                        advance(scanner);
                    }
                    return make_token(TOKEN_NUMBER, scanner->start, scanner->current);
                } else {
                    fprintf(stderr, "Unexpected character: %c\n", peek(scanner));
                    exit(1);
                }
        }
    }
    return make_token(TOKEN_EOF, scanner->start, scanner->current);
}

void expression(Parser* parser);

void factor(Parser* parser) {
    if (parser->current.type == TOKEN_NUMBER) {
        advance(&parser->scanner);
    } else if (parser->current.type == TOKEN_LPAREN) {
        advance(&parser->scanner);
        expression(parser);
        if (parser->current.type != TOKEN_RPAREN) {
            fprintf(stderr, "Expected ')'\n");
            exit(1);
        }
        advance(&parser->scanner);
    } else {
        fprintf(stderr, "Expected number or '('\n");
        exit(1);
    }
}

void term(Parser* parser) {
    factor(parser);
    while (parser->current.type == TOKEN_MULTIPLY || parser->current.type == TOKEN_DIVIDE) {
        advance(&parser->scanner);
        factor(parser);
    }
}

void expression(Parser* parser) {
    term(parser);
    while (parser->current.type == TOKEN_PLUS || parser->current.type == TOKEN_MINUS) {
        advance(&parser->scanner);
        term(parser);
    }
}

double evaluate(const char* source) {
    Parser parser;
    init_parser(&parser, source);
    parser.current = scan_token(&parser.scanner);
    expression(&parser);
    if (parser.current.type != TOKEN_EOF) {
        fprintf(stderr, "Expected EOF\n");
        exit(1);
    }
    return parser.current.value;
}

int main() {
    printf("%f\n", evaluate("1 + 2 * 3"));
    printf("%f\n", evaluate("1 + (2 * 3)"));
    printf("%f\n", evaluate("(1 + 2) * 3"));
    printf("%f\n", evaluate("1 + 2 * 3 + 4 / 2"));
    return 0;
}
```

```sh
$ gcc -o main main.c
$ ./main
7.000000
7.000000
9.000000
9.000000
```
