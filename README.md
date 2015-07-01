## Asserts & Testing

Wrapping our functions up in packages is awesome, because now not only can we reuse our old work, but so can our colleagues and labmates. Think of how much faster we can all move, if we were to pick just one of the implementations of our function on the etherpad, instead of everybody writing their own.

But - do you trust me? Are the functions we've written actually correct? Or worse, are the functions you got from a random person on the internet actually correct? Even if they are correct, how do we know how to use them correctly? Consider the following:

*`myScript.py`:*
```
import genomics.matching as matching

print matching.matchScore([True, {}, 7], [True, {}, 7])
```

A perfect match! Even though we're putting garbage in, we happen to get a completely unremarkable answer out - so in a bigger program, things would just keep bumping along, and we might never realize that nonsense was happening in the middle.

In order to avoid absurd circumstances like these (which happen more often than you'd think), python has things called asserts; they check that a certain condition is true, and if it isn't cause the program to halt immediately and report what went wrong. Example:

*`matching.py`:*
```
  def matchScore(seqA, seqB):

    assert type(seqA) == str, 'First sequence is not a string.'
    assert type(seqB) == str, 'Second sequence is not a string.'

    ...
```

The asserts check that the sequences are actually strings, like we'd expect, before allowing execution to continue; if the condition is false, execution halts and the sentence at the end of the assert is reported.

In general, asserts are tools to ensure our assumptions have been respected; every time you can make an assumption about the state of a function, put in an assert to make sure things are as you expect.

### Problem 1

> Add another `assert` to `matchScore` to check that the inputs make sense. 


Finally, we need a way to easily check if the functions actually work properly. To do this, we need to write tests. Let's start by writing a test for our scoring function:

*`tests/tests.py`*
```
import genomics.matching as matching

class TestClass:

    def test_matchScore_type(self):
        '''
        matchScore should return an int.
        '''

        score = matching.matchScore('AAA', 'AAA')

        assert type(score) == int, 'matchScore should have returned an int.'
```

then we run the test by going into the root directory of the project and doing `nosetests`. Our test makes sure that the `matchScore` function does at least part of what it's supposed to: it ensures that it's returning a valid number.

Also note that in future, even if we change `matchScore` to another implementation, we can still run the tests to make sure we haven't broken anything - this is another key feature of testing, it allows us to quickly and automatically make sure we haven't broken stuff as we work.

It's impossible to 100% guarantee that nothing could possibly be wrong with a function - tests just make it more likely that nothing is wrong. Writing good tests is a bit of an art that comes with practice; but, one thing you can always do is write a few spot-checks that make sure a function does what it's supposed to in familiar circumstances. For example:

*`tests/tests.py`*
```
...

def test_matchScore_spotCheck(self):
    '''
    Check an example of matchScore.
    '''

    score = matching.matchScore('AAA', 'AAA')

    assert score == 3, '"AAA" and "AAA" should have had a match score of 3, instead had %i' % score
```

### Problem 2

> Write a test that checks that the correct answer is found when comparing 'AG-C' and 'AG-G'.

The last problem is an example of another reason why writing tests is so crucial - they help us spot our mistakes! More immediately than worrying about future bugs or reusing code, testing lets us validate our code in a concrete, reproducible way, and ensure that it actually does what we think it does.

### Problem 3

> In genomics, we often want the 'reverse complement' of a genome; to construct this, take a genome and do the following:
> - reverse the order of the characters
> - swap A for T, and G for C.
>
> So for example, ACCG becomes CGGT.
> Imagine the `matching.py` module contained a function called `reverseComplement`. This function should always return a string, and should at least be able to get the above example right. Write two tests in `tests.py` that check for this behavior; don't bother actually writing the `reverseComplement` function.

What you just did is an example of **test driven design** - writing down what we want our function to do by defining the tests that check that that's the case. TDD has the great advantage of helping us fight confirmation bias when coding; if you write a function first, there's a great temptation to run it once, see that it superficially looks okay, and call it done. Having the tests in place forces you to check that all the expected behavior actually works.

```
for file in data*
do
python analysis cat $file
done
```
