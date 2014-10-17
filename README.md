# expandparams

`expandparams` is a (La)TeX package that controls the expansion of token list with ease.
By using the commands `\expandparams` and `\prexp` in this package
you can save the cost of writing thousands of `\expandafter`. 
For example, let `\csone` and `\cstwo` be defined by below:

    \makeatletter
    \def\csone{GF}%
    \def\cstwo{\csone N}%
    \def\separatetokens#1#2#3{%
      \@tfor\ch:=#1\do{[\ch]}%
      \@tfor\ch:=#2\do{(\ch)}%
      \@tfor\ch:=#3\do{\{\ch\}%
    }%

Here, ordinary spec of `\separatetokens` is like

    \separatetokens{AB}{PQRS}{XYZ}\par
      % => [A][B](P)(Q)(R)(S){X}{Y}{Z}
    \separatetokens{AB}{\cstwo}{\csone}\par
      % => [A][B](GFN){GF}

However, sometimes we need to expand parameters before the main control sequence is expanded.
When you want to expand `\csone` once before `\separatetokens` is expanded,
you can write

    \expandparams{\separatetokens{AB}{\cstwo}\prexp{\cstwo}}\par
      % => [A][B](GFN){G}{F}

instead of writing

    \expandafter\separatetokens
      \expandafter{\expandafter A\expandafter B\expandafter}%
      \expandafter{\expandafter\cstwo\expandafter}%
      \expandafter{\csone}\par

by using enormous `\expandafter`s. Similarly, you can write

    \expandparams{\separatetokens{AB}\prexp{\cstwo}\prexp{\csone}}\par
      % => [A][B](GF)(N){G}{F}

instead of

    \expandafter\expandafter\expandafter\separatetokens
      \expandafter\expandafter\expandafter{%
        \expandafter\expandafter\expandafter A%
        \expandafter\expandafter\expandafter B%
      \expandafter\expandafter\expandafter}%
      \expandafter\expandafter\expandafter
      {\expandafter\cstwo\expandafter}\expandafter{\csone}\par
 
for expanding both `\csone` and `\cstwo` once. And you can also write

    \expandparams{\separatetokens{AB}\prexp\prexp{\cstwo}{\csone}}\par
      % => [A][B](G)(F)(N){GF}

instead of

    \expandafter\expandafter\expandafter\separatetokens
      \expandafter\expandafter\expandafter{%
      	\expandafter\expandafter\expandafter A%
      	\expandafter\expandafter\expandafter B%
      \expandafter\expandafter\expandafter}%
      \expandafter\expandafter\expandafter{\cstwo}{\csone}\par

for expanding `\cstwo` *twice*.