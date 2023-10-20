Causal graphs as (abstract) R scripts
================

# The importance of managing your anxiety and taking your time

This is an introduction to Pearl’s Structural Causal Model (SCM) for
psychologists (<https://en.wikipedia.org/wiki/Causal_model>). What
follows takes some time to get used to. You will gradually learn a whole
*new language*, with technical terms such as “sequence,” “function,”
“graph,” “node,” “edge,” “path,” “Markovian,” “recursive,”
“d-separation,” “probability distribution,” “conditional probability,”
“statistical (in)dependence,” “conditional (or marginal)
(in)dependence,” “linear regression,” etc., and you will learn to use
this language to talk and reason about causal properties, study design,
and statistical analysis.

Since this is a mathematical theory we are talking about, its language
has strict definitions and strict rules that may seem weird or
unnecessarily complicated at first. For instance, only with some
practice will you be able to pay enough attention to the difference
between a graph and a path when both terms are used simultaneously in a
short passage of text. My advice is: If you feel confused, take a break,
maybe even take a full day off, and later get back to the earlier parts
of the text, even if you think you already understood them.

# What is a causal graph

A *graph* consists of a bunch of *nodes* connected by *edges*. The nodes
are its “points,” and the edges are the connections between the nodes.
If you are curious about the general definition of a graph, then you may
want to have a look at the Wikipedia page
<https://en.wikipedia.org/wiki/Graph_(discrete_mathematics)>, but this
may not be the best idea at this point, as the graphs that we will use
will be rather restricted.

We will almost exclusively use *Directed Acyclic Graphs* (DAGs). A graph
is *directed* if there are no non-directional edges on it, i.e., there
are no connections without any arrowheads; it is *acyclic* if there are
no (directional) loops. For instance, if, on graph $G$, there is an
arrow: from $X$ to $Y$, from $Y$ to $Z$, and from $Z$ to $X$, then $G$
is a cyclic graph, but if, e.g., there are only arrows: from $X$ to $Y$,
from $Z$ to $Y$, and from $X$ to $Z$, then there are no cycles and such
a graph is acyclic.

**Exercise 0**: Draw the two graphs. Note that the drawing of the first
graph should be a sketch, not an actual graph because we did not say
that the listed arrows completely define the structure of the first
graph. Maybe even use the terms “node,” “edge,” and “arrow” to describe
the structure of one of the graphs.

Graphs can be used to represent many different kinds of relations
between many different kinds of elements or objects. We will use graphs
to represent *causal* relations between *variables*. This interpretation
will make them causal graphs. A node in a causal graph may be any
variable (we take the notion of a variable to be self-evident). We will
even sometimes draw nodes that correspond to theoretical latent
constructs (we take “latent” to mean unobserved), which are variables
that may not exist, like, for example, a personality trait interpreted
as a latent common cause of the responses to test items (I, for one, am
not sure if it makes sense to say that a personality trait is a common
cause of the responses to the items on a personality test but I do not
expect that you will agree).

This is important and may seem weird for quite a while: Unless we
explicitly state otherwise, an *arrow* from $X$ to $Y$ is just the
*theoretical possibility* of a *direct* (= not mediated by other nodes
on the same causal graph) causal effect of $X$ on $Y$. The presence of
an arrow from $X$ to $Y$ means that if $X$ was intervened on (the
distribution of) $Y$ *might* change even when every other (modeled)
variable was held constant (the modeled-unmodeled distinction will be
explained later). In particular, *just by drawing an arrow or an arc, we
do not express any causal assumption*; the *actual* causal assumptions
are represented by the *missing* arrows and the *missing* arcs. Do not
worry if it seems you are getting used to this aspect of the theory
rather slowly; it takes time.

A *bidirectional* arc is a shorthand notation for the possible existence
of an unmodeled common cause:
$X \leftrightarrow Y := X \leftarrow ? \rightarrow Y$ (the question mark
is nonstandard).

**Exercise 1**: Copy the $X \rightarrow Y$ graph, and write the question
mark above the only arrow this graph contains to highlight that an arrow
is just the theoretical possibility of a direct causal effect. Assuming
that it is a causal graph, draw, as crossed directed edges, all the
actual assumptions that this graph represents.

# Instatiating causal graphs by writing scripts that generate observations.

The graph $X \rightarrow Y \rightarrow Z$ can be instantiated by the
following process (of generating the observations in the modeled
variables):

This is just the sample size that we give a name ($n$) to:

``` r
n = 10000
```

This guarantees that the simulation results are repeatable. The number
that I use here as the seed of the (pseudo)random number generator
($1234$) is arbitrary. If you use the same number and execute the same
commands in the same order, you will obtain the same results even though
(pseudo)random samples are used.

``` r
set.seed(1234) 
```

In our graph, $X$ has no modeled aka endogenous causes; it comes
entirely from an unspecified source. We will model $X$ as normally
distributed just because:

``` r
X = rnorm(n) 
```

The rnorm function generates (pseudo)random samples from a normal
distribution. When no additional arguments are given except for the
sample size the rnorm function generates (essentially) independent
samples from the standard normal distribution (i.e., one with mean = $0$
and sd = $1$).

In our graph, $Y$ has one modeled direct cause, $X$, and a unique
exogenous or unmodeled or unspecified source. We say that those two
variables directly cause $Y$. The way the values of $Y$ are generated
using the values of $X$ and the *new and independent* (pseudo)random
samples could be different; here, it is a linear process, with intercept
= $0$, slope = $1$, and normally distributed unmodeled source only
because this is a simple and convenient choice.

``` r
Y = X + rnorm(n) 
```

Finally, $Z$ is directly caused by its own unique unmodeled source and
by $Y$.

``` r
Z = Y + rnorm(n) 
```

Notice that we did create an actual causal process: The = sign does not
represent equality here; it is an *assignment operator*. When R
evaluates the assignment statement, the value created by evaluating the
expression on the right-hand side becomes the value of the variable on
the left-hand side. This means that (a function of) the right-hand side
(expression) is the cause of (a function of) the left-hand side
(expression).

The graph $X \rightarrow Y \rightarrow Z$ is a true description of the
process that we have just created, or it is valid for this process (this
terminology is nonstandard) in the following sense: There are no causal
relations between $X$, $Y$, and $Z$ that are not represented by an arrow
or an arc on this graph, i.e., no real causal relation is missing from
the graph. You may feel that something is off because the same
expression, rnorm(n), appears on the right-hand side of every
instruction that generates a modeled variable, and so it looks like
rnorm(n) is a common cause of all three modeled variables. In all
honesty, it kind of is, but the rnorm function works in such a way that
we can assume that it isn’t a common cause, as the generated samples are
*independent*.

The fact that no real arrows or arcs are missing is the only thing that
matters as long as we only want to know if the causal graph is true,
which would make the conclusions correctly derived from this graph true
as well. The causal graph may have *too many* arrows or arcs, and it may
still be true precisely because an arrow or an arc is just the
theoretical possibility of the corresponding causal relation, but it
cannot have too few arrows or arcs, or it will be false because a
missing arrow or a missing arc represents a definite lack of the
corresponding causal relation.

**Exercise 2**: Draw a different causal graph, with nodes $X$, $Y$, and
$Z$, that is true as a qualitative causal model of the process that we
have created. While you are at it, draw at least one causal graph that
is false as a description of this process.

The price we pay, if any, for the unnecessary arrows or arcs, i.e., the
ones that do not correspond to any real causal relations, is that the
resulting graph may have fewer testable consequences and it may be that
fewer causal effects or causal properties are estimable from data given
the graph. For instance, if we want to model three variables, $X$, $Y$,
and $Z$, and we want to be sure that our causal graph is true, we can
just draw every possible arrow and arc, and we will obtain a true causal
graph. The graph will be pretty much useless, but it will be true. In
other words, the fewer edges there are, the stronger or more powerful
the causal graph, but with power comes responsibility: In general, it is
better to draw an edge if you are not sure if it can be safely omitted,
or you will gain inferential power and perhaps draw strong and
interesting conclusions *only* because you hope or wish something was
true.

As we can see below, all the three variables are correlated:

``` r
cor.test(X, Y)
```


        Pearson's product-moment correlation

    data:  X and Y
    t = 99.236, df = 9998, p-value < 2.2e-16
    alternative hypothesis: true correlation is not equal to 0
    95 percent confidence interval:
     0.6944146 0.7141668
    sample estimates:
          cor 
    0.7044271 

``` r
cor.test(X, Z)
```


        Pearson's product-moment correlation

    data:  X and Z
    t = 69.407, df = 9998, p-value < 2.2e-16
    alternative hypothesis: true correlation is not equal to 0
    95 percent confidence interval:
     0.5568515 0.5833086
    sample estimates:
          cor 
    0.5702279 

``` r
cor.test(Y, Z)
```


        Pearson's product-moment correlation

    data:  Y and Z
    t = 140.73, df = 9998, p-value < 2.2e-16
    alternative hypothesis: true correlation is not equal to 0
    95 percent confidence interval:
     0.8085096 0.8216631
    sample estimates:
          cor 
    0.8151914 

This is the consequence of the properties of our particular
instantiation of the graph $X \rightarrow Y \rightarrow Z$; it is *not*
the consequence of this graph *alone*. The graph alone only represents
the *qualitative theoretical (im)possibilities of direct causal
effects*. In particular, even if there were no correlations between the
variables, this graph could still be true. For instance, this causal
graph is also valid for the following process (do not evaluate this
code, but make sure you understand why this process can be described by
the same graph):

``` r
X = rnorm(n)
Y = rnorm(n)
Z = rnorm(n)
```

The tests above cannot possibly provide evidence for or against our
causal graph as such, but there is one that can. We will now test the
only testable (or statistical or observable) consequence of the graph
$X \rightarrow Y \rightarrow Z$. According to this graph, $X$ and $Z$
*must* be independent when we condition on / stratify by / (correctly)
statistically control for $Y$. In this case, “to statistically control
for $Y$” boils down to including $Y$ as an additional predictor in a
linear regression model, but this simple method of statistically
controlling for may not work in more complicated situations (we will
talk some more about this issue later).

``` r
summary(lm(Z ~ X + Y))
```


    Call:
    lm(formula = Z ~ X + Y)

    Residuals:
        Min      1Q  Median      3Q     Max 
    -4.0680 -0.6771 -0.0081  0.6643  3.8304 

    Coefficients:
                 Estimate Std. Error t value Pr(>|t|)    
    (Intercept)  0.004170   0.009992   0.417    0.676    
    X           -0.013921   0.014255  -0.977    0.329    
    Y            0.999006   0.009933 100.576   <2e-16 ***
    ---
    Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

    Residual standard error: 0.9991 on 9997 degrees of freedom
    Multiple R-squared:  0.6646,    Adjusted R-squared:  0.6645 
    F-statistic:  9903 on 2 and 9997 DF,  p-value: < 2.2e-16

As you can see, the *statistical* effect of $X$ on $Z$ is not
significant when we include $Y$ as an additional predictor in a linear
regression model.

It is customary to denote the exogenous or unmodeled variables using the
letter “U” with a subscript that denotes the directly affected variable,
e.g., the direct exogenous source of variability in $X$ is $U_{X}$ (we
call it U_X in R because using subscripts in R is not the best idea),
the exogenous source of $Y$ is $U_{Y}$, etc.

If $X \rightarrow Y$ is a causal graph, then it is actually shorthand
for $U_{X} \rightarrow X \rightarrow Y \leftarrow U_{Y}$, where $U_{X}$
and $U_{Y}$ are assumed to be independent because there is no arc
between $X$ and $Y$. Using this notation, we can re-create the process
like this:

``` r
n = 10000
set.seed(1234)

U_X = rnorm(n)
U_Y = rnorm(n)
U_Z = rnorm(n)
```

Each exogenous or unmodeled variable is filled with a fresh batch of n
independent (here, they just happen to be normally distributed) random
samples. This exogenous or unmodeled part of the model corresponds to
the so-called *background conditions* of the study or the sampling
process:

This is the endogenous or modeled part:

``` r
X = U_X
Y = X + U_Y
Z = Y + U_Z
```

The notation is now slightly different (there were no $U_{i}$ variables
before), but the process is the same. Notice that the two parts really
are different: The unmodeled or exogenous part is (pseudo)random,
whereas there is nothing random about the modeled or endogenous part; it
is *deterministic*.

This is the test of the only testable property of our graph (the same as
before):

``` r
summary(lm(Z ~ X + Y))
```


    Call:
    lm(formula = Z ~ X + Y)

    Residuals:
        Min      1Q  Median      3Q     Max 
    -4.0680 -0.6771 -0.0081  0.6643  3.8304 

    Coefficients:
                 Estimate Std. Error t value Pr(>|t|)    
    (Intercept)  0.004170   0.009992   0.417    0.676    
    X           -0.013921   0.014255  -0.977    0.329    
    Y            0.999006   0.009933 100.576   <2e-16 ***
    ---
    Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

    Residual standard error: 0.9991 on 9997 degrees of freedom
    Multiple R-squared:  0.6646,    Adjusted R-squared:  0.6645 
    F-statistic:  9903 on 2 and 9997 DF,  p-value: < 2.2e-16

Can you see that nothing interesting is going on in the unmodeled part?
That is why we usually do not draw the unmodeled variables unless two
modeled variables may have some unmodeled common cause, in which case we
must draw a bidirectional arc between the nodes that represent such
modeled variables.

Here is another important technical term: A *path* on a causal graph is
a finite, nonempty sequence of adjacent arrows, where adjacent means
something like “touching each other” (in a good way), such that the
direction may change along the way, but the sequence does not contain
any repetitions. For instance, if this:
$X \rightarrow Y \rightarrow Z \leftarrow V \rightarrow W$ is a causal
graph (with unmodeled variables omitted), then $(X \rightarrow Y)$ is a
path on this graph, $(Y \leftarrow X)$ is *another* path, as the order
matters in sequences, $(X \rightarrow Y \rightarrow Z)$ is a path on
this graph, and $(Z \leftarrow V)$ is a path on this graph, and
$(Z \leftarrow V \rightarrow W)$ is one, and
$(X \rightarrow Y \rightarrow Z \leftarrow V \rightarrow W)$ as well. To
my knowledge, the notational convention to surround the paths with
parentheses is nonstandard, by the way.

So, here is all you need to know about the three most important simple
causal structures:

# The Holy Trinity: the Chain, the Fork, and the Weird One aka the Collider

Consider the path $(X \rightarrow Y \rightarrow Z)$ as a causal graph
$X \rightarrow Y \rightarrow Z$. Can you already see how every causal
path can be thought of as a causal graph, but not every causal graph can
be thought of as a causal path? When we say it is a graph, it follows
that it is not just a part of some possibly unspecified, perhaps larger
causal graph, as all paths are by definition, but it is the whole graph.
Why am I describing it in such a convoluted way? To make you practice
using the terms “path” and “graph” correctly.

So, $X \rightarrow Y \rightarrow Z$ is a *chain* (graph). Here are *all*
the 7 *actual* causal assumptions, in no particular order, that this
causal graph represents:

1.  $Y$ does not directly cause $X$.

2.  $Z$ does not directly cause $Y$.

3.  $Z$ does not directly cause $X$.

4.  $X$ does not directly cause $Z$.

5.  $X$ and $Y$ do not have unmodeled common causes.

6.  $Y$ and $Z$ do not have unmodeled common causes.

7.  $X$ and $Z$ do not have unmodeled common causes.

Recall that “directly” means “in a way that is not mediated by some
modeled variable”; it does not mean “immediately”: The process
represented by all the arrows entering the same node/variable may be
arbitrarily complex, it may be parallel, and it may have multiple
stages - the arrows are abstractions over such details.

As you already know, this is the *only* testable consequence of the
chain graph $X \rightarrow Y \rightarrow Z$: $X$ and $Z$ are independent
(in particular, they are not correlated) in every stratum of $Y$, or
conditioning on $Y$, or given $Y$. In short, $X$ ind $Z | Y$. There is a
proper mathematical symbol for statistical independence (see here:
<https://www.fileformat.info/info/unicode/char/2aeb/index.htm>), but it
is a rare animal, and so here, I simply write “ind” instead. Anyway, we
have already performed this test, and, as a result, we *did not reject*
the chain graph because *we did not find statistical dependence where*,
according to this causal graph, *there was not supposed to be any*. We
did not really obtain any evidence that this graph is true either. If
you are confused by all this, recall the inherent asymmetry of null
hypothesis significance testing. If you cannot recall what this is
about, please, be better than the great majority of the researchers in
the field, i.e., be better than the so-called researchers, and do learn
what a null hypothesis significance test is.

The *fork*, as a graph, looks like this: $X \leftarrow Y \rightarrow Z$,
and it has the same set of testable properties, i.e., if this graph is
true, then $X$ ind $Z|Y$. That’s it; *that is all that a fork or a chain
can tell you about the distribution of its modeled variables*.

**Exercise 3**: Using the $U_{i}$ notation for every unmodeled variable,
write the code of a simulation of a fork-like process just like we did
for the chain graph. To be consistent, write the R code so that all the
unmodeled variables, i.e., $U_{X}$, $U_{Y}$, and $U_{Z}$, are generated
first. Perform the statistical test of the only testable property of
this graph, and interpret the result of this test in terms of evidence
for or against the causal assumptions represented by the graph.

**Exercise 4**: Draw a different causal graph that is also valid for (=
true as a description of) the fork process that you have just
implemented. Is there a chain graph that is valid for this process?
Justify your answer.

This may also take some time to get used to: A chain graph and a fork
graph *cannot be distinguished by observing the modeled variables*! Such
graphs or models are called *equivalent*, or statistically equivalent,
or we may say that they belong to the same equivalence class. We *can*
distinguish the two causal structures empirically if we *intervene* on
(some of) the modeled variables, instead of just passively observing
them, but that is another matter.

**Exercise 5**: Instantiate the *collider* graph
$X \rightarrow Y \leftarrow Z$.

The term “collider” is ambiguous; it is used to denote paths and graphs
of this shape, as well as variables that are directly caused by two or
more variables.

An *active* path is any path that does not contain any colliders. If a
path has a collider somewhere, then it is *inactive*. You can say that a
collider breaks the flow of information, if any, induced by the path
that it is a part of. If $X$ and $Y$ are nodes on the causal graph $G$,
then $X$ and $Y$ *may* be statistically dependent according to $G$
*only* if there is at least one active path between $X$ and $Y$; if
there are no active paths on $G$ between $X$ and $Y$ then $X$ and $Y$
*are* independent according to $G$ (they may be *conditionally*
dependent though, as we will see later).

The only testable consequence of the collider graph
$X \rightarrow Y \leftarrow Z$ is that $X$ and $Z$ are independent
(unconditionally, or in the population, or marginally): $X$ ind $Z$. You
should now know that $X$ and $Z$ are independent according to this graph
because there are no active paths between $X$ and $Z$ on this graph. If
it turns out that $X$ and $Z$ are in fact, dependent, for instance, if
$X$ and $Z$ turn out to be correlated, then such a result will mean that
the collider graph is probably false. In general, we can only say
“probably false” because the statistical effect may be significant due
to sampling error.

The reason why colliders are weird is that they may induce *spurious*
conditional dependencies. Here, “spurious” means something like fake,
but not exactly, because the statistical dependence induced by
conditioning on a collider is real, it is just that it is
*systematically misleading about its causal source*. This weird
phenomenon can be illustrated by evaluating this (this time, the
observations in $X$, $Y$, and $Z$ were generated by a process of shape
$X \rightarrow Y \leftarrow Z$, but I did not include the code for this
process since you were supposed to write it yourself as a solution to
exercise 5):

``` r
cor.test(X, Z)
```


        Pearson's product-moment correlation

    data:  X and Z
    t = 1.0752, df = 9998, p-value = 0.2823
    alternative hypothesis: true correlation is not equal to 0
    95 percent confidence interval:
     -0.008849123  0.030346487
    sample estimates:
           cor 
    0.01075281 

``` r
summary(lm(Z ~ X + Y))
```


    Call:
    lm(formula = Z ~ X + Y)

    Residuals:
         Min       1Q   Median       3Q      Max 
    -2.58850 -0.47118  0.00207  0.46563  3.07255 

    Coefficients:
                 Estimate Std. Error t value Pr(>|t|)    
    (Intercept) -0.005983   0.007093  -0.844    0.399    
    X           -0.490495   0.008743 -56.099   <2e-16 ***
    Y            0.503448   0.005006 100.576   <2e-16 ***
    ---
    Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

    Residual standard error: 0.7093 on 9997 degrees of freedom
    Multiple R-squared:  0.503, Adjusted R-squared:  0.5029 
    F-statistic:  5059 on 2 and 9997 DF,  p-value: < 2.2e-16

In our collider-like process, $X$ and $Z$ are independent (a fact that
*we have not obtained evidence against*, as the correlation between $X$
and $Z$ was not significant), but the statistical effect of $X$ on $Z$
is (highly) significant when we also include $Y$ as a predictor in a
linear regression model. This is a general phenomenon: if $Y$ *really
is* caused by $X$ and $Z$ then conditioning on $Y$ will *most likely*
induce a spurious statistical dependence between $X$ and $Z$. That is
why: women may easily *seem* more (or less) intelligent than men if we
have too many (or too few) psychology students in our sample, it may
*seem* that achievements in sports tend to be accompanied by poor
academic performance if we have too many or too few college students in
our sample, and it may *seem* that attractive people are less nice if we
study people on dates.

Here is how I sometimes remember how the three causal structures just
described work: Only the presence of a collider prevents the natural
flow of information, if any, through a path. Conditioning on a collider
may turn on and distort the flow of information, and conditioning on the
middle node in a fork or a chain prevents the flow.

**Exercise 6**: Prove that if $X$ is randomly assigned, $Y$ is observed,
and $X$ and $Y$ are statistically dependent (i.e., there is some kind of
statistical effect of $X$ on $Y$), then this statistical effect can only
be due to some *directional* path(s) going *from* $X$ *to* $Y$. We say
that a path is directional if it does not change direction along the
way. Notice that we do *not* assume here that $X$ and $Y$ are the *only*
modeled variables on the graph, so your proof may include a sketch of a
partially specified causal graph. You may state your proof like this:
For there to be a statistical effect of $X$ on $Y$ there has to be an
active path between X and Y. Since $X$ is randomly assigned, this path
has to be such that …, and if this path is … and it is active then …

**Exercise 7**: Assuming that:

$R$ is randomly assigned.

$C$(ause) and $E$(ffect) are two *latent* variables such that $E$ cannot
cause $C$.

$Y$ is observed, and its value is determined only *after* the values of
$C$ and $E$ are determined.

7.a Draw the causal graph that represents all the arrows and arcs that
are not excluded by this set of assumptions but make it so that $R$ is
the first node on the left, $C$ is to the right of $R$, $E$ is to the
right of $C$, and $Y$ is to the right of $E$.

7.b Remove all the arrows that belong *only* to the *inactive* paths
between the observed variables $R$ and $Y$. Such paths cannot explain
the observed statistical effect of $R$ on $Y$. Since we are removing
some possibly “real” arrows, the graph may not be true, but its purpose
here is to serve as a representation of all the possible qualitative
causal explanations of the statistical effect, if any, of $R$ on $Y$.
Reflect on how this final graph is related to the result that you
obtained in the previous exercise.
