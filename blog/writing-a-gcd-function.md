<!-- 
.. title: Code Writeup: Writing a GCD Function
.. slug: writing-a-gcd-function
.. date: 2016-05-11 18:08:45 UTC-04:00
.. tags: code, algorithms, writeups
.. category: programming
.. link: 
.. description: A writup on various approaches I went through implementing a gcd function
.. type: text
-->

Over the last couple of months, I haven't been able to set aside as much time to practice coding as much as I would like. However, wanting to stay in practice I have tried to set aside some time on weekends in order to do one or two challenges on [checkio.org](https://checkio.org). One of the nice things about the challenges posted there is that they don't simply ask you to solve problems, but you have to solve them in a way that manages to past tests that they have set for each problem. Oftentimes, I will come up with a solution to a problem, but will need to then go back to think about how I can either make my solution more efficient or handle various edge cases the authors have included. Today, I wanted to go through and focus on one particular problem to analyze various ways of solving it.

The [problem](https://checkio.org/mission/gcd/) in question was fairly standard, asking me to figure out the greatest common divisor for a set of integers provided as arguments to a function. It is worth noting that the integers were positive (0 < x <= 2**32), and the approaches presented here may need some additional logic if we wanted to extend the solutions to include negative integers (return the negative of the gcd of the absolute values of the arguments) or 0 (either return the non-zero number or I think either undefined or zero for gcd(0,0)). 

# Initial Approach

    :::python
    def greatest_common_divisor(*args):
        """Brute force gcd function."""
        for possible_divisor in reversed(range(1, min(args) + 1)):
            if all([arg % possible_divisor == 0 for arg in args]):
                return possible_divisor

# A Slight Improvement

What seems like the most obvious way to improve this solution is to reduce the number of factors we need to check. Since we're only interested in the largest number that evenly divides all of our arguments: it doesn't really make sense to search from lower to higher possible solutions, since we can't know if we've found the gcd till the full range has been checked. But if we search for possible factors starting from higher numbers and working to lower numbers, then we can stop as soon as we hit a number that evenly divides all of our arguments. 

    :::python
    def greatest_common_divisor(*args):
        """Brute force gcd function."""
        for possible_divisor in reversed(range(1,min(args))):
            if all([arg % possible_divisor == 0 for arg in args]):
                return possible_divisor

The interesting thing to think about with this solution, is that it doesn't improve worst case performance. If the gcd of all of our arguments is 1, then we still end up searching trying `min(args)` factors. However, *it does* improve best-case performance: In the best case requiring only 1 potential divisor to be checked.


# Using prime factorization

My next approach was as follows: First, find the prime factorization for each number we are considering. For each of the prime factors, find how many times that factor occurs in prime factorization for each number. Finally, for the prime factors that occur in the prime factorization of all of the numbers we're trying to find the gcd for, take the product of those factors raised to however many times it occurs the least across all of our prime factorizations. 

    :::python
    def prime_gen():
        found = [2]
        tocheck = 2
        yield 2
        while True:
            tocheck += 1
            divisor_found = False
            for prime in found:
                if tocheck % prime == 0:
                    divisor_found = True
                    break
            if not divisor_found:
                found.append(tocheck)
                yield tocheck


    def factor(number):
        # print("factoring: {}".format(number))
        seed = number
        factors = []
        while seed > 1:
            testing_primes = True
            prime_generator = prime_gen()
            while testing_primes:
                prime_to_test = next(prime_generator)
                if seed % prime_to_test == 0:
                    factors.append(prime_to_test)
                    seed = seed // prime_to_test
                    testing_primes = False
            # print("Seed: {}, Factors: {}".format(seed,factors))
        return factors


    def greatest_common_divisor(*args):
        """
            Find the greatest common divisor
        """
        factor_dicts = []
        # calculate the prime factorization for each argument
        for arg in args:
            factorization = factor(arg)
            factor_powers = {}
            for unique_factor in set(factorization):
                factor_powers[unique_factor] = factorization.count(unique_factor)
            factor_dicts.append(factor_powers)
        # get factors that appear in all the prime factorizations
        common_factors = set(factor_dicts[0].keys())
        for factor_dict in factor_dicts:
            common_factors = common_factors.intersection(factor_dict)
        # if the set of common factors is empty 1 is the gcd
        if len(common_factors) == 0:
            return 1
        else:
            # calculate the product of the minimum power of each common factor
            minpowers = dict()
            for common_factor in common_factors:
                minpowers[common_factor] = min(
                    [factor_dict[common_factor] for factor_dict in factor_dicts])
            minpower_exponentiated = [
                key**value for key, value in minpowers.items()]
            gcd = 1
            for powered_factor in minpower_exponentiated:
                gcd = gcd * powered_factor
            return gcd

While this approach works in theory, it isn't immediately apparent whether it will be better *per se* than the second solution. In theory, it seemed like it could be better if we memoized our function for finding primes, and needed to make more than one call to the greatest_common_divisor function. 

This third solution still failed to pass the full test suite however. It handed all of the simple test cases that it was thrown at, but would hang on the first edge case. Uncommenting the print statements in the factor function produced the following results for the first two numbers in the test it tried to factor.

    factoring: 2167650657
    Seed: 722550219, Factors: [3]
    Seed: 240850073, Factors: [3, 3]
    Seed: 4544341, Factors: [3, 3, 53]
    Seed: 2543, Factors: [3, 3, 53, 1787]
    Seed: 1, Factors: [3, 3, 53, 1787, 2543]
    factoring: 1496767446
    Seed: 748383723, Factors: [2]
    Seed: 249461241, Factors: [2, 3]
    Seed: 83153747, Factors: [2, 3, 3]
    Seed: 4376513, Factors: [2, 3, 3, 19]

It appeared that it was working fine for some numbers, but was needing to calculate fairly large primes for others. It managed to find the factors [2, 3, 3, 19] for 1496767446, but then would hang. As a small digression, if we want to figure out what prime it is struggling to calculate, we can do so with sympy fairly easily. 

    >>> from sympy import primefactors
    >>> primefactors(1496767446)
    [2, 3, 19, 4376513]

So there's the problem, it got stuck trying to find prime factors of 4376513 which was itself prime.

# With Euclid's Algorithm

So with the previous approach choaking for numbers that include large prime factors, it was back to the drawing board. I had previously heard of Euclid's Algorithm, but hadn't taken the time to look into it, instead opting previously for a more number theoretic approach. (In retrospect, Donald Knuth's quote about the Euclidian algorithm on the information page for the challege probably should have pushed me in this direction in the first place, but here we are.)


    ::python
    def gcd(A,B):
        """ Recursive implementation of euclid's algorithm"""
        if A == 0:
            return B
        if B == 0:
            return A
        quotient = A//B
        remainder = A%B
        # A = B*quotient + remainder
        return gcd(B,remainder)
        
    def greatest_common_divisor(*args):
        """
            Find the greatest common divisor
        """
        greatestCommonDivisor = min(args)
        for a in args:
            greatestCommonDivisor = gcd(greatestCommonDivisor,a)
        return greatestCommonDivisor

Note here that this solution works in part because [the gcd is associative](https://en.wikipedia.org/wiki/Greatest_common_divisor#Properties). Another equally valid approach for applying the function to an arbitrary number of arguments would have been to use `reduce(gcd, args)` (In python 3.0+, you would first have to include `from functools import reduce` in your imports).

# Best approach

The best approach however, is probably to just rely upon the standard library when possible.

    ::python
    from fractions import gcd

    def greatest_common_divisor(*args):
        """
            Find the greatest common divisor
        """
        greatestCommonDivisor = min(args)
        for a in args:
            greatestCommonDivisor = gcd(greatestCommonDivisor,a)
        return greatestCommonDivisor



If we take a look at `\lib\fractions.py` , we can see that the gcd function included in the standard library uses an iterative version of Euclid's Algorithm. 

    :::python
    def gcd(a, b):
        """Calculate the Greatest Common Divisor of a and b.

        Unless b==0, the result will have the same sign as b (so that when
        b is divided by it, the result comes out positive).
        """
        while b:
            a, b = b, a%b
        return a

# Summary

This challenge ended up being rather instructive for me for a couple of reasons. First, it helped me to realize that I need to take more time to analyze and research approaches to solving a problem before diving in and trying to solve it. Second, it really reinforces the point that if you really don't *need* to calculate primes, then you may want to question whether you should. Finally, working through different possible solutions got me to start thinking about the time-complexity of the code I've written. 
