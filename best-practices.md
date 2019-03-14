### let expressions

You _should_ put `let` before each variable inside a `do` block.
But beware of name shadowing (though compiler can help with it).

```haskell
foo = do
  let x   = 10
  let f 1 = 5
      f _ = 0  -- possible shadowing here with let
  return $ x + f 2
```

_WARNING_: try to avoid aggressive autoderiving because
[it can slow down compilation](http://www.stephendiehl.com/posts/production.html).

> Deriving instances of Read/Show/Data/Generic for largely recursive ADTs can
> sometimes lead to quadratic memory behavior when the nesting gets deep.

You _should not_ use `() <$` to ignore the result of function, because it less legible
than `_ <-` or `void $`.

```haskell
foo = do
  _ <- sendCert ourVssCertificate  -- cannot be used as last statement
  void $ sendTransactionAndReport 1 "tx42"
```

### ApplicativeDo

Use `-XApplicativeDo` only when necessary.

You _should_ use `-XApplicativeDo` when you are writing a parser of CLI arguments with
`optparse-applicative` because it is very easy to mess up the order of the arguments.
However, be aware of
[some non-obvious behavior](https://www.reddit.com/r/haskell/comments/7679g8/ghc_821_applicativedo_possible_bug/)
when you use pattern-matching inside `do`-blocks.

## Dealing with laziness

By default, use strict data types and lazy functions.

### Data types

You _should_ enable [`StrictData`](https://downloads.haskell.org/~ghc/latest/docs/html/users_guide/glasgow_exts.html#extension-StrictData)
globally for your project, marking fields explicitly with `~` when you need laziness.

```haskell
{-# LANGUAGE StrictData #-}

-- Good
data Point = Point
  { pointX :: Double  -- ^ X coordinate
  , pointY :: Double  -- ^ Y coordinate
  }

-- OK, but needs justification
data Point = Point
  { pointX :: ~Double  -- ^ X coordinate
  , pointY :: ~Double  -- ^ Y coordinate
  }
```

Additionally, unpacking simple fields often improves performance and
reduces memory usage:

```haskell
data Point = Point
  { pointX :: {-# UNPACK #-} Double  -- ^ X coordinate
  , pointY :: {-# UNPACK #-} Double  -- ^ Y coordinate
  }
```

As an alternative to the `UNPACK` pragma, you can put

```haskell
{-# OPTIONS_GHC -funbox-strict-fields #-}
```

at the top of the file.  Including this flag in the file itself instead
of e.g. in the Cabal file is preferable as the optimization will be
applied even if someone compiles the file using other means (i.e. the
optimization is attached to the source code it belongs to).

Note that `-funbox-strict-fields` applies to all strict fields, not
just small fields (e.g. `Double` or `Int`). If you are using GHC 7.4 or
later you can use `NOUNPACK` to selectively opt-out for the unpacking
enabled by `-funbox-strict-fields`.

For backwards compatibility, you _may_ choose to keep `StrictData` off globally, but you are
strongly advised not to. If you choose not to enable it, you _should_ mark all record
fields as strict, unless there is an explicit reason to make them lazy.

```haskell
-- Good
data Point = Point
  { pointX :: !Double  -- ^ X coordinate
  , pointY :: !Double  -- ^ Y coordinate
  }

-- Needs justification for implicit laziness
data Point = Point
  { pointX :: Double  -- ^ X coordinate
  , pointY :: Double  -- ^ Y coordinate
  }
```

### Functions

Have function arguments be lazy unless you explicitly need them to be
strict.

The most common case when you need strict function arguments is in
recursion with an accumulator:

```haskell
mysum :: [Int] -> Int
mysum = go 0
  where
    go !acc []    = acc
    go acc (x:xs) = go (acc + x) xs
```

### Warnings

 The following GHC options _should_ be enabled globally for your project    
([source](https://medium.com/mercury-bank/enable-all-the-warnings-a0517bc081c3)):   

 ```    
-Weverything    
-Wno-missing-exported-signatures    
-Wno-missing-import-lists   
-Wno-missed-specialisations 
-Wno-all-missed-specialisations 
-Wno-unsafe 
-Wno-safe   
-Wno-missing-local-signatures   
-Wno-monomorphism-restriction   
-Wno-implicit-prelude   
-Werror 
``` 

 A successful build _must_ not trigger any of the enabled warnings. 

 You _may_ use `{-# OPTIONS_GHC -fno-warn-orphans #-}` on a per-module basis.
