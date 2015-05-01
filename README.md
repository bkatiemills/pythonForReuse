## Warmup

So far, we've learned how to write loops, conditionals, and functions in python. Let's warm up with a review.

### Problem 1

> Imagine having two aligned genome sequences. We want to calculate a score that describes how good the alignment is. Qualitatively,

> - AAA AAA  is a perfect match
> - AAA AAG  is an okayish match
> - AAA AA-  where - is an unknown gap, is a bit better
> - AAA GGG is obviously terrible
> - A-- A-- how good is this, really?

> Let's give an alignment +1 point for every matching, non-gap character, -1 point for every mismatched non-gap character, and +0 points if there's a gap. Write a function `matchScore` that takes two globally aligned sequences, and uses this algorithm to calculate the match's score. Make 0 points for a gap the default behavior, but let the user change this if they like.

> Soln:

```
 def matchScore(seqA, seqB, gapPenalty=0):

   score = 0

   for i in range(len(seqA)):

     if seqA[i] == '-' or seqB[i] == '-':
       score += gapPenalty
     elif seqA[i] == seqB[i]:
       score += 1
     else:
       score -= 1

   return score
 ```

(After: everyone cut and paste your answers into the etherpad - we'll come back to these later)

## Making Packages

Now that we have a function to caluclate how well we're aligning sequences, we'd like to use it in many projects.

It's annoying to have to cut and paste it every time we want to use it; that sort of defeats the purpose of making functions in the long run.

Instead, we can include functions that we've written ourselves, in the same way we've been including things like numpy, if we put them in their own file:

(Place `matchScore` into a file `pythonLesson/scoring.py`, then...)

*IPython Notebook:*
```
import scoring

scoring.matchScore('AAGT', 'AAGC')
```


In this way, we can reuse our functions in every project, by importing them. We can update, debug and modify them all in one place - and easily distribute them to our colleagues.

It'll be a big mess, though, if we have a million python files all in one directory; we'd rather move our collections of functions into a nice tidy subdirectory, say `pythonLesson/sequenceTools`

After doing this, we need to create a file `__init__.py` to let Python know the files in this subdirectory are meant for inclusion; the file can be blank, as long as it's there.

Then, we give the include command the relative path:

*IPython Notebook:*
```
import sequenceTools.scoring

sequenceTools.scoring.matchScore('AAGT', 'AAGC')
```
This is called 'making a module.'

### MCQ 1:

>You launch a notebook from a directory called `pythonLesson`. In your notebook, you want to use a DNA transform package that you just wrote, via the following two lines of code:

```
  import DNA.transforms

  DNA.transforms.reverseCompliment('AGCT')
```

>What should the `pythonLesson` directory contain in order for this to work?

>  - a) Two files, `DNA.py` and `transforms.py`.
>  - b) A folder called `DNA`, which contains `transforms.py` and `reverseComplement.py`.
>  - c) A folder called `DNA`, where `DNA` contains `transforms.py` and `__init.py__`.
>  - d) `__init.py__` and a folder called `DNA`, where `DNA` contains a file called `transforms.py`.



### MCQ 2

> In the previous problem, which file contains the definition of the function `reverseComplement`?

> - a) `__init__.py`
> - b) the ipython notebook
> - c) `transforms.py`


### Problem 2

> Make the module described in the MCQ; you may implement as many functions in `transforms.py` as you like, as long as `reverseComplement` gets the job done:

> Given a string, reverse the order of the characters, and make the replacements:

> Swap A and T

> Swap G and C

> So AATCG becomes CGATT.

> Use your new function in your notebook.


## Asserts & Testing

Wrapping our functions up in packages is awesome, because now not only can we reuse our old work, but so can our colleagues and labmates. Think of how much faster we can all move, if we were to pick just one of the implementations of our function on the etherpad, instead of everybody writing their own.

But - do you trust me? Are the functions we've written actually correct? Or worse, are the functions you got from a random person on the internet actually correct? Even if they are correct, how do we know how to use them correctly? Consider the following:

*IPython Notebook:*
```
  sequenceTools.scoring.matchScore( [True, {}, 7], [True, {}, 7] )
```

A perfect match! Even though we're putting garbage in, we happen to get a completely unremarkable answer out - so in a bigger program, things would just keep bumping along, and we might never realize that nonsense was happening in the middle.

In order to avoid absurd circumstances like these (which happen more often than you'd think), python has things called asserts; they check that a certain condition is true, and if it isn't cause the program to halt immediately and report what went wrong. Example:

*`scoring.py`:*
```
  def matchScore(seqA, seqB):

    assert type(seqA) == str, 'First sequence is not a string.'
    assert type(seqB) == str, 'Second sequence is not a string.'

    ...
```

The asserts check that the sequences are actually strings, like we'd expect, before allowing execution to continue; if the condition is false, execution halts and the sentence at the end of the assert is reported.

In general, asserts are tools to ensure our assumptions have been respected; every time you can make an assumption about the state of a function, put in an assert to make sure things are as you expect.

### Problem 3

> Add two asserts to the function(s) you wrote in `transforms.py`. Remember to do 'kernel->restart' after every time you change your package in order for the changes to affect your Notebook.


Finally, we need a way to easily check if the functions actually work properly. To do this, we need to write tests. Let's start by writing a test for our scoring function:

*`sequenceTools/tests.py`*
```
import scoring

def test_matchScore_type():
  '''
  matchScore should return an int.
  '''

  score = scoring.matchScore('AAA', 'AAA')

  assert type(score) == int
```

then we run the test by going into the `sequenceTools` directory and doing `nosetests`. Our test makes sure that the `matchScore` function does at least part of what it's supposed to: it ensures that it's returning a valid number.

Also note that in future, even if we change `matchScore` to another implementation, we can still run the tests to make sure we haven't broken anything - this is another key feature of testing, it allows us to quickly and automatically make sure we haven't broken stuff as we work.

It's impossible to 100% guarantee that nothing could possibly be wrong with a function - tests just make it more likely that nothing is wrong. Writing good tests is a bit of an art that comes with practice; but, one thing you can always do is write a few spot-checks that make sure a function does what it's supposed to in familiar circumstances. For example:

*`sequenceTools/tests.py`*
```
def test_matchScore_spotCheck():
  '''
  Check an example of matchScore.
  '''

  score = scoring.matchScore('AAA', 'AAA')

  assert score == 3
```

Another thing to think about when writing tests, is checking for 'edge cases' - situations that might come up that are a bit special. For example:

*`sequenceTools/tests.py`*
```
def test_matchScore_emptyString():
  '''
  matchScore should return 0 if string arguments are empty.
  '''

  score = scoring.matchScore('', '')

  assert score == 0
```

By making sure our code passes a battery of tests of its performance, we can be more confident that our code is actually working correctly; also, we can more easily double check as we work that nothing has broken in our code.

### Problem 4

> Write two tests for the `reverseComplement` function you wrote in problem 2.




## Scripting


So far, we've been using the IPython Notebook to run all our python code. This looks nice, but there are drawbacks:

 - it's an extra moving part you have to install correctly and hope doesn't break
 - it's hard to interact with it from the shell, like we learned about yesterday
 - we have to fool around with the kernel every time we change an underlying package

At the cost of pretty rendering, we can greatly simplify the things. In your `pythonLesson` directory, make a file called `analysis.py`:

*`pythonLesson/analysis.py:`*
```
import sequenceTools.scoring

print sequenceTools.scoring.matchScore('AAGT', 'AAGC')
```

Notice this is exactly the same as what you did in the notebook, except we have to explicitly ask for things to be printed. This is called a script, and you can run it by:

```
python analysis.py
```

Exactly the same thing is happening in the script as was happening in the Notebook - but we don't have to have the notebook installed and working to run things this way.

We can also interact with a script from the bash shell, by passing in variables. In `analysis.py`:

*`pythonLesson/analysis.py:`*
```
import sequenceTools.scoring
import sys

print sequenceTools.scoring.matchScore(sys.argv[1], sys.argv[2])
```

then at the bash prompt

```
python analysis.py AAA AAA
```

In this way, we can use all our techniques from the bash shell to execute our program with different inputs - like running over different input files in a loop. If we have a bunch of files `data-n.dat` that contain inputs for our script, we could loop over them via something like:

```
for file in data*
do
python analysis cat $file
done
```
