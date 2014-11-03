###[Enumerating k-mers Lexicographically](http://rosalind.info/problems/lexf/)

####The idea

A solution to this problem can be to perform n products of the alphabet and the concatanate the resulting string.

For example for the given example it would be:

```haskell
['T', 'A', 'G', 'C'] x ['T', 'A', 'G', 'C']
```

The algorithm is all about slowly making the product of the alphabet and the currently build strings, so that if we have the strings `["AA", "AB", "BA", "BB"]` (clearly generated by the cross product of the alphabet twice) and the alphabet `['A', 'B']`, then the next step is to perform the product of those two:

```haskell
["AA", "AB", "BA", "BB"] x ['A', 'B']
```

which results in:

```haskell
["AAA", "AAB", "ABA", "ABB", ...]
```

The algorithm will resemble something along the lines of:

```haskell
getAlphabetStrings :: Int -> [Char] -> [String]
getAlphabetStrings n a = foldr (< our product >) [""] (replicate n (a :: [Char]))
```

that is: we replicate the alphabet `n` times and then, starting from a single empty string, we build the product of the empty string with the alphabet, then the product of the resulting 4 strings with the alphabet again, and so on.

####The product function

We would like to define a function to feed to out `foldr` function above. This function should take a list of `Char`s (the alphabet) and a list of `String`s (the strings we have currently built) and then glue them together with a simple `(:)` function.

A naive implementation would be:

```haskell
product :: [Char] -> [String] -> [String]
product as bs = do
    x <- as
    y <- bs
    return $ x:y
```

which can be generalized to:

```haskell
productWith :: (a -> b -> c) -> [a] -> [b] -> [c]
productWith fn as bs = do
    x <- as
    y <- bs
    return $ fn x y
```

At this point I simply couldn't believe something like this wouldn't already exist in the standard library, so I went to [Hoogle](https://www.haskell.org/hoogle/) and searched for the above function type. What I got, amongst other functions, was `liftM2` which generalizes this even further by using monads instead of lists.

####The resulting implementation

Our final implementation is therefore:

```haskell
getAlphabetStrings :: Int -> [Char] -> [String]
getAlphabetStrings n a = foldr (liftM2 (:)) [""] (replicate n (a :: [Char]))
```