# AutoBracket

Given a custom binary operator and a sequence of numbers, AutoBracket finds where to put the parentheses so the expression evaluates to a target value. If several bracketings work, it returns the left-most one.

Built in C++ for the Algorithm Analysis and Synthesis course at Instituto Superior Técnico, with Miguel Raposo.

## The Problem

The operator ⊕ is defined by a table instead of arithmetic. Row is the left operand, column the right:

<pre>
⊕ | 1 2 3
--|------
1 | 2 2 1
2 | 3 2 1
3 | 1 3 3
</pre>

Because ⊕ isn't associative, the result of a sequence depends on how it's bracketed. Given the sequence and a target result:

<pre>
2 ⊕ 2 ⊕ 2 ⊕ 2 ⊕ 1 ⊕ 3 = 1
</pre>

the program has to find a bracketing that produces it:

<pre>
((((2 ⊕ 2) ⊕ 2) ⊕ (2 ⊕ 1)) ⊕ 3) = 1
</pre>

The number of possible bracketings grows with the Catalan numbers, so trying them all is out of the question for sequences of hundreds of terms.

## How It Works

It's interval dynamic programming, the same shape as matrix-chain multiplication.

- **State.** For every subsequence `i..j`, `dp[i][j]` holds each result that subsequence can produce, plus how to produce it: the split point `k` and the left and right results that combine into it.
- **Base case.** A single number can only produce itself.
- **Transition.** For each split `i..k | k+1..j`, every left result is combined with every right result through the operator table. A result is only stored the first time it appears, so each cell holds at most `n` entries.
- **Left-most answer.** Split points are tried from right to left, so the first way found to reach a result is the one that groups as far left as possible. That's the bracketing the problem asks for.
- **Early exits.** A cell stops searching once all `n` possible results are found, and the whole search stops as soon as the full sequence reaches the target.
- **Reconstruction.** The bracketing is rebuilt recursively from the stored split points.

**Complexity.** For a sequence of length `m` and an `n × n` table: O(m³ · n²) time in the worst case and O(m² · n) memory. The early exits make typical inputs much faster than that bound.

## Input and Output

Input on stdin:

```
n m                  table size and sequence length
n lines of n values  the operator table (row ⊕ column)
m values             the sequence
1 value              the target result
```

Output: `1` followed by the bracketing, or `0` if no bracketing reaches the target.

```console
$ printf '3 6\n2 2 1\n3 2 1\n1 3 3\n2 2 2 2 1 3\n1\n' | ./main
1
((((2 2) 2) (2 1)) 3)
```

## Building and Testing

Requires `g++` and `make`.

```sh
cd src
make            # build ./main
make test       # run every test in tests/ and diff against the expected output
make time       # time each test
make new_test   # generate random tests, from 2×1 up to 1000×1000
```

## License

[MIT](LICENSE) © 2024 Inês Costa and Miguel Raposo
