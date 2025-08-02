// C++ Program to implement a lexical analyzer

#include <iostream>
#include <string>
#include <unordered_map>
#include <vector>
using namespace std;

// Enum class to define different types of tokens
enum class TokenType {
    KEYWORD,
    IDENTIFIER,
    INTEGER_LITERAL,
    FLOAT_LITERAL,
    OPERATOR,
    PUNCTUATOR,
    UNKNOWN
};

// Struct to represent a token with its type and value
struct Token {
    TokenType type;
    string value;

    Token(TokenType t, const string& v)
        : type(t)
        , value(v)
    {
    }
};

// Class that implements the lexical analyzer
class LexicalAnalyzer {
private:
    string input;
    size_t position;
    unordered_map<string, TokenType> keywords;

    // Function to initialize the keywords map
    void initKeywords()
    {
        keywords["int"] = TokenType::KEYWORD;
        keywords["float"] = TokenType::KEYWORD;
        keywords["if"] = TokenType::KEYWORD;
        keywords["else"] = TokenType::KEYWORD;
        keywords["while"] = TokenType::KEYWORD;
        keywords["return"] = TokenType::KEYWORD;
    }

    // Function to check if a character is whitespace
    bool isWhitespace(char c)
    {
        return c == ' ' || c == '\t' || c == '\n'
               || c == '\r';
    }

    // Function to check if a character is alphabetic
    bool isAlpha(char c)
    {
        return (c >= 'a' && c <= 'z')
               || (c >= 'A' && c <= 'Z');
    }

    // Function to check if a character is a digit
    bool isDigit(char c) { return c >= '0' && c <= '9'; }

    // Function to check if a character is alphanumeric
    bool isAlphaNumeric(char c)
    {
        return isAlpha(c) || isDigit(c);
    }

    // Function to get the next word (identifier or keyword)
    // from the input
    string getNextWord()
    {
        size_t start = position;
        while (position < input.length()
               && isAlphaNumeric(input[position])) {
            position++;
        }
        return input.substr(start, position - start);
    }

    // Function to get the next number (integer or float)
    // from the input
    string getNextNumber()
    {
        size_t start = position;
        bool hasDecimal = false;
        while (position < input.length()
               && (isDigit(input[position])
                   || input[position] == '.')) {
            if (input[position] == '.') {
                if (hasDecimal)
                    break;
                hasDecimal = true;
            }
            position++;
        }
        return input.substr(start, position - start);
    }

public:
    // Constructor for LexicalAnalyzer
    LexicalAnalyzer(const string& source)
        : input(source)
        , position(0)
    {
        initKeywords();
    }

    // Function to tokenize the input string
    vector<Token> tokenize()
    {
        vector<Token> tokens;

        while (position < input.length()) {
            char currentChar = input[position];

            // Skip whitespace
            if (isWhitespace(currentChar)) {
                position++;
                continue;
            }

            // Identify keywords or identifiers
            if (isAlpha(currentChar)) {
                string word = getNextWord();
                if (keywords.find(word) != keywords.end()) {
                    tokens.emplace_back(TokenType::KEYWORD,
                                        word);
                }
                else {
                    tokens.emplace_back(
                        TokenType::IDENTIFIER, word);
                }
            }
            // Identify integer or float literals
            else if (isDigit(currentChar)) {
                string number = getNextNumber();
                if (number.find('.') != string::npos) {
                    tokens.emplace_back(
                        TokenType::FLOAT_LITERAL, number);
                }
                else {
                    tokens.emplace_back(
                        TokenType::INTEGER_LITERAL, number);
                }
            }
            // Identify operators
            else if (currentChar == '+'
                     || currentChar == '-'
                     || currentChar == '*'
                     || currentChar == '/') {
                tokens.emplace_back(TokenType::OPERATOR,
                                    string(1, currentChar));
                position++;
            }
            // Identify punctuators
            else if (currentChar == '('
                     || currentChar == ')'
                     || currentChar == '{'
                     || currentChar == '}'
                     || currentChar == ';') {
                tokens.emplace_back(TokenType::PUNCTUATOR,
                                    string(1, currentChar));
                position++;
            }
            // Handle unknown characters
            else {
                tokens.emplace_back(TokenType::UNKNOWN,
                                    string(1, currentChar));
                position++;
            }
        }

        return tokens;
    }
};

// Function to convert TokenType to string for printing
string getTokenTypeName(TokenType type)
{
    switch (type) {
    case TokenType::KEYWORD:
        return "KEYWORD";
    case TokenType::IDENTIFIER:
        return "IDENTIFIER";
    case TokenType::INTEGER_LITERAL:
        return "INTEGER_LITERAL";
    case TokenType::FLOAT_LITERAL:
        return "FLOAT_LITERAL";
    case TokenType::OPERATOR:
        return "OPERATOR";
    case TokenType::PUNCTUATOR:
        return "PUNCTUATOR";
    case TokenType::UNKNOWN:
        return "UNKNOWN";
    default:
        return "UNDEFINED";
    }
}

// Function to print all tokens
void printTokens(const vector<Token>& tokens)
{
    for (const auto& token : tokens) {
        cout << "Type: " << getTokenTypeName(token.type)
             << ", Value: " << token.value << endl;
    }
}

// Driver Code
int main()
{
    // Sample source code to be analyzed
    string sourceCode
        = "int main() { float x = 3.14; float y=3.15; "
          "float z=x+y; return 0; }";

    // Create a LexicalAnalyzer object
    LexicalAnalyzer lexer(sourceCode);

    // Tokenize the source code
    vector<Token> tokens = lexer.tokenize();

    // Print the original source code
    cout << "Source code: " << sourceCode << endl << endl;

    // Print all identified tokens
    cout << "Tokens Generate by Lexical Analyzer:" << endl;
    printTokens(tokens);

    return 0;
}
