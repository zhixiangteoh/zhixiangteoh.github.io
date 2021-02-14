---
title: Monads for the Java developer
layout: post
show_footer: true
github_link: https://github.com/zhixiangteoh/java-monads-report
demo_link: https://github.com/zhixiangteoh/java-monads-report/blob/main/Monads%20for%20the%20Java%20developer%20-%20Report.pdf
tags:
  - Programming Languages
  - Functional Programming
  - Research
  - Haskell
  - Java
  - Monads
  - Type Theory
  - Team
---

Fourth and final research project of the National University of Singapore (NUS) Computer Science module [Programming Language Concepts](https://nusmods.com/modules/CS2104/programming-language-concepts).

Together with two other peers, we wrote a [short 10-page report](https://github.com/zhixiangteoh/java-monads-report/blob/main/Monads%20for%20the%20Java%20developer%20-%20Report.pdf) entitled "Monads[^1] for the Java Developer". I wrote and adapted the sections Monads in Java and Use Cases of Monads in Java, including the code snippets for the [Maybe](https://github.com/zhixiangteoh/java-monads-report/blob/main/Maybe.java) and [Either](https://github.com/zhixiangteoh/java-monads-report/blob/main/Either.java) classes in Java, in the flavor of [Haskell](https://hackage.haskell.org/package/base-4.14.1.0/docs/Data-Maybe.html), and the tests for [conformity of the classes with the monad laws](https://github.com/zhixiangteoh/java-monads-report/blob/main/OptionalTestAssociativity.java).

## Code

`Maybe.java`:

```java
/**
 * This is a Maybe class written in the flavor of the Maybe monad in Haskell. This Maybe class is
 * discussed in my Monads for the Java developer Term Paper for CS2104 (NUS Semester 1 2020-2021), 
 * written along with my teammates Ryan Tay and Ye Guoquan.
 */

import java.util.function.Function;

class Maybe<A> {
    private A a;

    private Maybe(A aVal) {
        if (!isNull(aVal)) a = aVal;
        else a = null;
    }
    public static <A> Maybe<A> unit(A a) {
        return new Maybe<>(a);
    }
    public <B> Maybe<B> bind(Function<A, Maybe<B>> f) {
        if (!isNull(a)) return f.apply(a);
        else return new Maybe<B>(null);
    }
    boolean isNull(A a) {
        return a == null;
    }
    public String toString() {
        if (!isNull(a)) return "Just " + a;
        else return "Nothing";
    }
    public static void main(String[] args) {
        Maybe<Integer> a = Maybe.unit(3);
        Maybe<Integer> b = Maybe.unit(null);
        
        Maybe<Integer> sum = 
            a.bind(val1 -> 
            b.bind(val2 -> 
            Maybe.unit(val1 + val2)  
        ));

        System.out.println(sum); // Nothing
    }
}
```

---

`Either.java`:

```java
/**
 * Andrés Castaño. 2016. Monads for Java developers, Part 2. (November 2016). 
 * Retrieved November 6, 2020, from 
 * https://medium.com/@afcastano/monads-for-java-developers-part-2-the-result-and-log-monads-a9ecc0f231bb
 * 
 * ResultTest.java, along with Result.java, was referenced to devise the 2-parametrized Either<E, A> class,
 * discussed in my Monads for the Java developer Term Paper for CS2104 (NUS Semester 1 2020-2021), 
 * written along with my teammates Ryan Tay and Ye Guoquan.
 */

import java.util.function.Function;
import java.util.Optional;

public class Either<E, A> {
    private Optional<E> error;
    private Optional<A> a;
    
    private Either(E error, A a) {
        this.error = Optional.ofNullable(error);
        this.a     = Optional.ofNullable(a);
    }
    public static <E, A> Either<E, A> unit(E error, A a) {
        if (error != null) return new Either<>(error, null);
        else return new Either<>(null, a);
    }
    public <B> Either<E, B> bind(Function<A, Either<E, B>> f) {
        if (!isError()) return f.apply(a.get());
        else return Either.unit(error.get(), (B) null);
    }
    boolean isError() {
        return error.isPresent();
    }
    public String toString() {
        if (!error.isPresent()) return "Right " + a.get();
        else return "Left " + error.get();
    }
}
```

Testing the `Either` class:

```java
/**
 * Andrés Castaño. 2016. Monads for Java developers, Part 2. (November 2016). 
 * Retrieved November 6, 2020, from 
 * https://medium.com/@afcastano/monads-for-java-developers-part-2-the-result-and-log-monads-a9ecc0f231bb
 * 
 * ResultTest.java, along with Result.java, was referenced to devise the 2-parametrized Either<E, A> class,
 * discussed in my Monads for the Java developer Term Paper for CS2104 (NUS Semester 1 2020-2021), 
 * written along with my teammates Ryan Tay and Ye Guoquan.
 */

public class EitherTest {
    public static void main(String[] args) {
        Either<Exception, Integer> a = either(3);
        Either<Exception, Integer> b = either(5);
        Either<Exception, Integer> c = either(null);
    
        Either<Exception, Integer> sum = 
            a.bind(e1 -> 
            b.bind(e2 -> 
            either(e1 + e2)
        ));

        Either<Exception, Integer> sum1 = 
            a.bind(e1 -> 
            c.bind(e2 -> 
            either(e1 + e2)
        ));

        System.out.println(sum);  // Right 8
        System.out.println(sum1); // Left java.lang.NumberFormatException
    }

    private static Either<Exception, Integer> either(Integer value) {
        if (value == null) return Either.unit(new NumberFormatException(), null);
        else return Either.unit(null, value);
    }
}
```

Testing conformity of the [Java 8 `Optional`](https://docs.oracle.com/javase/8/docs/api/java/util/Optional.html) class to the monad laws[^2]:

```java
// Associativity
/**
 * This test for conformity to the Monad law of Associativity is discussed in my Monads for the Java developer 
 * Term Paper for CS2104 (NUS Semester 1 2020-2021), written along with my teammates Ryan Tay and Ye Guoquan.
 */

import java.util.function.Function;
import java.util.Optional;

public class OptionalTestAssociativity {
    public static void main(String[] args) {
        Function<Integer, Optional<Integer>> f = x -> (x % 2 == 0) ? Optional.ofNullable(null) : Optional.ofNullable(x);
        Function<Integer, Optional<Integer>> g = y -> y == null    ? Optional.ofNullable(null) : Optional.ofNullable(y);

        Optional<Integer> m = Optional.of(2);
        boolean isAssociative = m.flatMap(f).flatMap(g).equals(m.flatMap(y -> f.apply(y).flatMap(g)));
        System.out.println(isAssociative); // true
    }
}
```

[Source](https://github.com/zhixiangteoh/java-monads-report/blob/main/OptionalTestAssociativity.java)

```java
// Left Identity
/**
 * Marcello La Rocca. 2016. How Optional Breaks the Monad Laws and Why It Matters. (September 2016). 
 * Retrieved November 6, 2020 from https://www.sitepoint.com/how-optional-breaks-the-monad-laws-and-why-it-matters/
 * 
 * This code is referenced from Marcello La Rocca's sitepoint article.
 * 
 * This test for conformity to the Monad law of Left Identity is discussed in my Monads for the Java developer 
 * Term Paper for CS2104 (NUS Semester 1 2020-2021), written along with my teammates Ryan Tay and Ye Guoquan.
 */

import java.util.function.Function;
import java.util.Optional;

public class OptionalTestLeftIdentity {
    public static void main(String[] args) {
        Function<Integer, Optional<Integer>> f = (x -> {
            if (x == null) return Optional.ofNullable(-1);
            else return Optional.ofNullable(null);
        });
        System.out.println(Optional.ofNullable((Integer) null).flatMap(f).equals(f.apply(null))); // false
    }    
}
```

[Source](https://github.com/zhixiangteoh/java-monads-report/blob/main/OptionalTestLeftIdentity.java)

```java
// Right Identity
/**
 * Marcello La Rocca. 2016. How Optional Breaks the Monad Laws and Why It Matters. (September 2016). 
 * Retrieved November 6, 2020 from https://www.sitepoint.com/how-optional-breaks-the-monad-laws-and-why-it-matters/
 * 
 * This code is referenced from Marcello La Rocca's sitepoint article.
 * 
 * This test for conformity to the Monad law of Right Identity is discussed in my Monads for the Java developer 
 * Term Paper for CS2104 (NUS Semester 1 2020-2021), written along with my teammates Ryan Tay and Ye Guoquan.
 */

import java.util.Optional;

public class OptionalTestRightIdentity {
    public static void main(String[] args) {
        Optional<Integer> m = Optional.ofNullable(null);
        System.out.println(m.flatMap(Optional::ofNullable).equals(m)); // false
    }    
}
```

[Source](https://github.com/zhixiangteoh/java-monads-report/blob/main/OptionalTestRightIdentity.java)

---
{: data-content="footnotes"}
[^1]: [Monads](https://en.wikipedia.org/wiki/Monad_(functional_programming)) are abstractions that allow programs to be structured generically. In the case of Haskell and many programming languages, monads are centered around their `bind` operator which provides allows function-chaining and provides a lot of the benefits of [functional programming](https://en.wikipedia.org/wiki/Functional_programming). See also: my report on [Functional Programming](/functional-programming-post)
[^2]: All monads should obey the three monad laws as elaborated [here](https://wiki.haskell.org/Monad_laws). In a nutshell, this ensures the integrity of the operations in which monads are defined upon.
