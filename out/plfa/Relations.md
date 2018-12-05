---
src       : src/plfa/Relations.lagda
title     : "Relations: Inductive definition of relations"
layout    : page
prev      : /Induction/
permalink : /Relations/
next      : /Equality/
---

<pre class="Agda">{% raw %}<a id="170" class="Keyword">module</a> <a id="177" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}" class="Module">plfa.Relations</a> <a id="192" class="Keyword">where</a>{% endraw %}</pre>

After having defined operations such as addition and multiplication,
the next step is to define relations, such as _less than or equal_.

## Imports

<pre class="Agda">{% raw %}<a id="373" class="Keyword">import</a> <a id="380" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html" class="Module">Relation.Binary.PropositionalEquality</a> <a id="418" class="Symbol">as</a> <a id="421" class="Module">Eq</a>
<a id="424" class="Keyword">open</a> <a id="429" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html" class="Module">Eq</a> <a id="432" class="Keyword">using</a> <a id="438" class="Symbol">(</a><a id="439" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Equality.html#83" class="Datatype Operator">_≡_</a><a id="442" class="Symbol">;</a> <a id="444" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Equality.html#140" class="InductiveConstructor">refl</a><a id="448" class="Symbol">;</a> <a id="450" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#1056" class="Function">cong</a><a id="454" class="Symbol">;</a> <a id="456" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.Core.html#838" class="Function">sym</a><a id="459" class="Symbol">)</a>
<a id="461" class="Keyword">open</a> <a id="466" class="Keyword">import</a> <a id="473" href="https://agda.github.io/agda-stdlib/Data.Nat.html" class="Module">Data.Nat</a> <a id="482" class="Keyword">using</a> <a id="488" class="Symbol">(</a><a id="489" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a><a id="490" class="Symbol">;</a> <a id="492" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#115" class="InductiveConstructor">zero</a><a id="496" class="Symbol">;</a> <a id="498" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a><a id="501" class="Symbol">;</a> <a id="503" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">_+_</a><a id="506" class="Symbol">;</a> <a id="508" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#433" class="Primitive Operator">_*_</a><a id="511" class="Symbol">;</a> <a id="513" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#320" class="Primitive Operator">_∸_</a><a id="516" class="Symbol">)</a>
<a id="518" class="Keyword">open</a> <a id="523" class="Keyword">import</a> <a id="530" href="https://agda.github.io/agda-stdlib/Data.Nat.Properties.html" class="Module">Data.Nat.Properties</a> <a id="550" class="Keyword">using</a> <a id="556" class="Symbol">(</a><a id="557" href="https://agda.github.io/agda-stdlib/Data.Nat.Properties.html#8011" class="Function">+-comm</a><a id="563" class="Symbol">;</a> <a id="565" href="https://agda.github.io/agda-stdlib/Data.Nat.Properties.html#7575" class="Function">+-suc</a><a id="570" class="Symbol">)</a>
<a id="572" class="Keyword">open</a> <a id="577" class="Keyword">import</a> <a id="584" href="https://agda.github.io/agda-stdlib/Data.List.html" class="Module">Data.List</a> <a id="594" class="Keyword">using</a> <a id="600" class="Symbol">(</a><a id="601" href="https://agda.github.io/agda-stdlib/Agda.Builtin.List.html#80" class="Datatype">List</a><a id="605" class="Symbol">;</a> <a id="607" href="https://agda.github.io/agda-stdlib/Data.List.Base.html#8019" class="InductiveConstructor">[]</a><a id="609" class="Symbol">;</a> <a id="611" href="https://agda.github.io/agda-stdlib/Agda.Builtin.List.html#132" class="InductiveConstructor Operator">_∷_</a><a id="614" class="Symbol">)</a>
<a id="616" class="Keyword">open</a> <a id="621" class="Keyword">import</a> <a id="628" href="https://agda.github.io/agda-stdlib/Function.html" class="Module">Function</a> <a id="637" class="Keyword">using</a> <a id="643" class="Symbol">(</a><a id="644" href="https://agda.github.io/agda-stdlib/Function.html#1068" class="Function">id</a><a id="646" class="Symbol">;</a> <a id="648" href="https://agda.github.io/agda-stdlib/Function.html#769" class="Function Operator">_∘_</a><a id="651" class="Symbol">)</a>
<a id="653" class="Keyword">open</a> <a id="658" class="Keyword">import</a> <a id="665" href="https://agda.github.io/agda-stdlib/Relation.Nullary.html" class="Module">Relation.Nullary</a> <a id="682" class="Keyword">using</a> <a id="688" class="Symbol">(</a><a id="689" href="https://agda.github.io/agda-stdlib/Relation.Nullary.html#464" class="Function Operator">¬_</a><a id="691" class="Symbol">)</a>
<a id="693" class="Keyword">open</a> <a id="698" class="Keyword">import</a> <a id="705" href="https://agda.github.io/agda-stdlib/Data.Empty.html" class="Module">Data.Empty</a> <a id="716" class="Keyword">using</a> <a id="722" class="Symbol">(</a><a id="723" href="https://agda.github.io/agda-stdlib/Data.Empty.html#243" class="Datatype">⊥</a><a id="724" class="Symbol">;</a> <a id="726" href="https://agda.github.io/agda-stdlib/Data.Empty.html#360" class="Function">⊥-elim</a><a id="732" class="Symbol">)</a>{% endraw %}</pre>


## Defining relations

The relation _less than or equal_ has an infinite number of
instances.  Here are a few of them:

    0 ≤ 0     0 ≤ 1     0 ≤ 2     0 ≤ 3     ...
              1 ≤ 1     1 ≤ 2     1 ≤ 3     ...
                        2 ≤ 2     2 ≤ 3     ...
                                  3 ≤ 3     ...
                                            ...

And yet, we can write a finite definition that encompasses
all of these instances in just a few lines.  Here is the
definition as a pair of inference rules:

    z≤n --------
        zero ≤ n

        m ≤ n
    s≤s -------------
        suc m ≤ suc n

And here is the definition in Agda:
<pre class="Agda">{% raw %}<a id="1409" class="Keyword">data</a> <a id="_≤_"></a><a id="1414" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1414" class="Datatype Operator">_≤_</a> <a id="1418" class="Symbol">:</a> <a id="1420" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a> <a id="1422" class="Symbol">→</a> <a id="1424" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a> <a id="1426" class="Symbol">→</a> <a id="1428" class="PrimitiveType">Set</a> <a id="1432" class="Keyword">where</a>

  <a id="_≤_.z≤n"></a><a id="1441" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1441" class="InductiveConstructor">z≤n</a> <a id="1445" class="Symbol">:</a> <a id="1447" class="Symbol">∀</a> <a id="1449" class="Symbol">{</a><a id="1450" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1450" class="Bound">n</a> <a id="1452" class="Symbol">:</a> <a id="1454" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a><a id="1455" class="Symbol">}</a>
      <a id="1463" class="Comment">--------</a>
    <a id="1476" class="Symbol">→</a> <a id="1478" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#115" class="InductiveConstructor">zero</a> <a id="1483" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1414" class="Datatype Operator">≤</a> <a id="1485" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1450" class="Bound">n</a>

  <a id="_≤_.s≤s"></a><a id="1490" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1490" class="InductiveConstructor">s≤s</a> <a id="1494" class="Symbol">:</a> <a id="1496" class="Symbol">∀</a> <a id="1498" class="Symbol">{</a><a id="1499" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1499" class="Bound">m</a> <a id="1501" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1501" class="Bound">n</a> <a id="1503" class="Symbol">:</a> <a id="1505" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a><a id="1506" class="Symbol">}</a>
    <a id="1512" class="Symbol">→</a> <a id="1514" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1499" class="Bound">m</a> <a id="1516" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1414" class="Datatype Operator">≤</a> <a id="1518" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1501" class="Bound">n</a>
      <a id="1526" class="Comment">-------------</a>
    <a id="1544" class="Symbol">→</a> <a id="1546" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="1550" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1499" class="Bound">m</a> <a id="1552" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1414" class="Datatype Operator">≤</a> <a id="1554" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="1558" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1501" class="Bound">n</a>{% endraw %}</pre>
Here `z≤n` and `s≤s` (with no spaces) are constructor names, while
`zero ≤ n`, and `m ≤ n` and `suc m ≤ suc n` (with spaces) are types.
This is our first use of an _indexed_ datatype, where the type `m ≤ n`
is indexed by two naturals, `m` and `n`.  In Agda any line beginning
with two or more dashes is a comment, and here we have exploited that
convention to write our Agda code in a form that resembles the
corresponding inference rules, a trick we will use often from now on.

Both definitions above tell us the same two things:

* _Base case_: for all naturals `n`, the proposition `zero ≤ n` holds
* _Inductive case_: for all naturals `m` and `n`, if the proposition
  `m ≤ n` holds, then the proposition `suc m ≤ suc n` holds.

In fact, they each give us a bit more detail:

* _Base case_: for all naturals `n`, the constructor `z≤n`
  produces evidence that `zero ≤ n` holds.
* _Inductive case_: for all naturals `m` and `n`, the constructor
  `s≤s` takes evidence that `m ≤ n` holds into evidence that
  `suc m ≤ suc n` holds.

For example, here in inference rule notation is the proof that
`2 ≤ 4`:

      z≤n -----
          0 ≤ 2
     s≤s -------
          1 ≤ 3
    s≤s ---------
          2 ≤ 4

And here is the corresponding Agda proof:
<pre class="Agda">{% raw %}<a id="2835" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#2835" class="Function">_</a> <a id="2837" class="Symbol">:</a> <a id="2839" class="Number">2</a> <a id="2841" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1414" class="Datatype Operator">≤</a> <a id="2843" class="Number">4</a>
<a id="2845" class="Symbol">_</a> <a id="2847" class="Symbol">=</a> <a id="2849" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1490" class="InductiveConstructor">s≤s</a> <a id="2853" class="Symbol">(</a><a id="2854" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1490" class="InductiveConstructor">s≤s</a> <a id="2858" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1441" class="InductiveConstructor">z≤n</a><a id="2861" class="Symbol">)</a>{% endraw %}</pre>




## Implicit arguments

This is our first use of implicit arguments.  In the definition of
inequality, the two lines defining the constructors use `∀`, very
similar to our use of `∀` in propositions such as:

    +-comm : ∀ (m n : ℕ) → m + n ≡ n + m

However, here the declarations are surrounded by curly braces `{ }`
rather than parentheses `( )`.  This means that the arguments are
_implicit_ and need not be written explicitly; instead, they are
_inferred_ by Agda's typechecker. Thus, we write `+-comm m n` for the
proof that `m + n ≡ n + m`, but `z≤n` for the proof that `zero ≤ n`,
leaving `n` implicit.  Similarly, if `m≤n` is evidence that `m ≤ n`,
we write `s≤s m≤n` for evidence that `suc m ≤ suc n`, leaving both `m`
and `n` implicit.

If we wish, it is possible to provide implicit arguments explicitly by
writing the arguments inside curly braces.  For instance, here is the
Agda proof that `2 ≤ 4` repeated, with the implicit arguments made
explicit:
<pre class="Agda">{% raw %}<a id="3856" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#3856" class="Function">_</a> <a id="3858" class="Symbol">:</a> <a id="3860" class="Number">2</a> <a id="3862" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1414" class="Datatype Operator">≤</a> <a id="3864" class="Number">4</a>
<a id="3866" class="Symbol">_</a> <a id="3868" class="Symbol">=</a> <a id="3870" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1490" class="InductiveConstructor">s≤s</a> <a id="3874" class="Symbol">{</a><a id="3875" class="Number">1</a><a id="3876" class="Symbol">}</a> <a id="3878" class="Symbol">{</a><a id="3879" class="Number">3</a><a id="3880" class="Symbol">}</a> <a id="3882" class="Symbol">(</a><a id="3883" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1490" class="InductiveConstructor">s≤s</a> <a id="3887" class="Symbol">{</a><a id="3888" class="Number">0</a><a id="3889" class="Symbol">}</a> <a id="3891" class="Symbol">{</a><a id="3892" class="Number">2</a><a id="3893" class="Symbol">}</a> <a id="3895" class="Symbol">(</a><a id="3896" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1441" class="InductiveConstructor">z≤n</a> <a id="3900" class="Symbol">{</a><a id="3901" class="Number">2</a><a id="3902" class="Symbol">}))</a>{% endraw %}</pre>
One may also identify implicit arguments by name:
<pre class="Agda">{% raw %}<a id="3980" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#3980" class="Function">_</a> <a id="3982" class="Symbol">:</a> <a id="3984" class="Number">2</a> <a id="3986" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1414" class="Datatype Operator">≤</a> <a id="3988" class="Number">4</a>
<a id="3990" class="Symbol">_</a> <a id="3992" class="Symbol">=</a> <a id="3994" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1490" class="InductiveConstructor">s≤s</a> <a id="3998" class="Symbol">{</a><a id="3999" class="Argument">m</a> <a id="4001" class="Symbol">=</a> <a id="4003" class="Number">1</a><a id="4004" class="Symbol">}</a> <a id="4006" class="Symbol">{</a><a id="4007" class="Argument">n</a> <a id="4009" class="Symbol">=</a> <a id="4011" class="Number">3</a><a id="4012" class="Symbol">}</a> <a id="4014" class="Symbol">(</a><a id="4015" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1490" class="InductiveConstructor">s≤s</a> <a id="4019" class="Symbol">{</a><a id="4020" class="Argument">m</a> <a id="4022" class="Symbol">=</a> <a id="4024" class="Number">0</a><a id="4025" class="Symbol">}</a> <a id="4027" class="Symbol">{</a><a id="4028" class="Argument">n</a> <a id="4030" class="Symbol">=</a> <a id="4032" class="Number">2</a><a id="4033" class="Symbol">}</a> <a id="4035" class="Symbol">(</a><a id="4036" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1441" class="InductiveConstructor">z≤n</a> <a id="4040" class="Symbol">{</a><a id="4041" class="Argument">n</a> <a id="4043" class="Symbol">=</a> <a id="4045" class="Number">2</a><a id="4046" class="Symbol">}))</a>{% endraw %}</pre>
In the latter format, you may only supply some implicit arguments:
<pre class="Agda">{% raw %}<a id="4141" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#4141" class="Function">_</a> <a id="4143" class="Symbol">:</a> <a id="4145" class="Number">2</a> <a id="4147" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1414" class="Datatype Operator">≤</a> <a id="4149" class="Number">4</a>
<a id="4151" class="Symbol">_</a> <a id="4153" class="Symbol">=</a> <a id="4155" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1490" class="InductiveConstructor">s≤s</a> <a id="4159" class="Symbol">{</a><a id="4160" class="Argument">n</a> <a id="4162" class="Symbol">=</a> <a id="4164" class="Number">3</a><a id="4165" class="Symbol">}</a> <a id="4167" class="Symbol">(</a><a id="4168" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1490" class="InductiveConstructor">s≤s</a> <a id="4172" class="Symbol">{</a><a id="4173" class="Argument">n</a> <a id="4175" class="Symbol">=</a> <a id="4177" class="Number">2</a><a id="4178" class="Symbol">}</a> <a id="4180" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1441" class="InductiveConstructor">z≤n</a><a id="4183" class="Symbol">)</a>{% endraw %}</pre>
It is not permitted to swap implicit arguments, even when named.


## Precedence

We declare the precedence for comparison as follows:
<pre class="Agda">{% raw %}<a id="4344" class="Keyword">infix</a> <a id="4350" class="Number">4</a> <a id="4352" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1414" class="Datatype Operator">_≤_</a>{% endraw %}</pre>
We set the precedence of `_≤_` at level 4, so it binds less tightly
that `_+_` at level 6 and hence `1 + 2 ≤ 3` parses as `(1 + 2) ≤ 3`.
We write `infix` to indicate that the operator does not associate to
either the left or right, as it makes no sense to parse `1 ≤ 2 ≤ 3` as
either `(1 ≤ 2) ≤ 3` or `1 ≤ (2 ≤ 3)`.


## Decidability

Given two numbers, it is straightforward to compute whether or not the
first is less than or equal to the second.  We don't give the code for
doing so here, but will return to this point in
Chapter [Decidable]({{ site.baseurl }}{% link out/plfa/Decidable.md%}).


## Properties of ordering relations

Relations pop up all the time, and mathematicians have agreed
on names for some of the most common properties.

* _Reflexive_. For all `n`, the relation `n ≤ n` holds.
* _Transitive_. For all `m`, `n`, and `p`, if `m ≤ n` and
`n ≤ p` hold, then `m ≤ p` holds.
* _Anti-symmetric_. For all `m` and `n`, if both `m ≤ n` and
`n ≤ m` hold, then `m ≡ n` holds.
* _Total_. For all `m` and `n`, either `m ≤ n` or `n ≤ m`
holds.

The relation `_≤_` satisfies all four of these properties.

There are also names for some combinations of these properties.

* _Preorder_. Any relation that is reflexive and transitive.
* _Partial order_. Any preorder that is also anti-symmetric.
* _Total order_. Any partial order that is also total.

If you ever bump into a relation at a party, you now know how
to make small talk, by asking it whether it is reflexive, transitive,
anti-symmetric, and total. Or instead you might ask whether it is a
preorder, partial order, or total order.

Less frivolously, if you ever bump into a relation while reading a
technical paper, this gives you a way to orient yourself, by checking
whether or not it is a preorder, partial order, or total order.  A
careful author will often call out these properties---or their
lack---for instance by saying that a newly introduced relation is a
a partial order but not a total order.


#### Exercise `orderings` {#orderings}

Give an example of a preorder that is not a partial order.

Give an example of a partial order that is not a total order.


## Reflexivity

The first property to prove about comparison is that it is reflexive:
for any natural `n`, the relation `n ≤ n` holds.  We follow the
convention in the standard library and make the argument implicit,
as that will make it easier to invoke reflexivity:
<pre class="Agda">{% raw %}<a id="≤-refl"></a><a id="6755" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#6755" class="Function">≤-refl</a> <a id="6762" class="Symbol">:</a> <a id="6764" class="Symbol">∀</a> <a id="6766" class="Symbol">{</a><a id="6767" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#6767" class="Bound">n</a> <a id="6769" class="Symbol">:</a> <a id="6771" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a><a id="6772" class="Symbol">}</a>
    <a id="6778" class="Comment">-----</a>
  <a id="6786" class="Symbol">→</a> <a id="6788" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#6767" class="Bound">n</a> <a id="6790" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1414" class="Datatype Operator">≤</a> <a id="6792" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#6767" class="Bound">n</a>
<a id="6794" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#6755" class="Function">≤-refl</a> <a id="6801" class="Symbol">{</a><a id="6802" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#115" class="InductiveConstructor">zero</a><a id="6806" class="Symbol">}</a>   <a id="6810" class="Symbol">=</a>  <a id="6813" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1441" class="InductiveConstructor">z≤n</a>
<a id="6817" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#6755" class="Function">≤-refl</a> <a id="6824" class="Symbol">{</a><a id="6825" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="6829" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#6829" class="Bound">n</a><a id="6830" class="Symbol">}</a>  <a id="6833" class="Symbol">=</a>  <a id="6836" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1490" class="InductiveConstructor">s≤s</a> <a id="6840" class="Symbol">(</a><a id="6841" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#6755" class="Function">≤-refl</a> <a id="6848" class="Symbol">{</a><a id="6849" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#6829" class="Bound">n</a><a id="6850" class="Symbol">})</a>{% endraw %}</pre>
The proof is a straightforward induction on the implicit argument `n`.
In the base case, `zero ≤ zero` holds by `z≤n`.  In the inductive
case, the inductive hypothesis `≤-refl {n}` gives us a proof of `n ≤
n`, and applying `s≤s` to that yields a proof of `suc n ≤ suc n`.

It is a good exercise to prove reflexivity interactively in Emacs,
using holes and the `C-c C-c`, `C-c C-,`, and `C-c C-r` commands.


## Transitivity

The second property to prove about comparison is that it is
transitive: for any naturals `m`, `n`, and `p`, if `m ≤ n` and `n ≤ p`
hold, then `m ≤ p` holds.  Again, `m`, `n`, and `p` are implicit:
<pre class="Agda">{% raw %}<a id="≤-trans"></a><a id="7499" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#7499" class="Function">≤-trans</a> <a id="7507" class="Symbol">:</a> <a id="7509" class="Symbol">∀</a> <a id="7511" class="Symbol">{</a><a id="7512" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#7512" class="Bound">m</a> <a id="7514" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#7514" class="Bound">n</a> <a id="7516" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#7516" class="Bound">p</a> <a id="7518" class="Symbol">:</a> <a id="7520" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a><a id="7521" class="Symbol">}</a>
  <a id="7525" class="Symbol">→</a> <a id="7527" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#7512" class="Bound">m</a> <a id="7529" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1414" class="Datatype Operator">≤</a> <a id="7531" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#7514" class="Bound">n</a>
  <a id="7535" class="Symbol">→</a> <a id="7537" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#7514" class="Bound">n</a> <a id="7539" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1414" class="Datatype Operator">≤</a> <a id="7541" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#7516" class="Bound">p</a>
    <a id="7547" class="Comment">-----</a>
  <a id="7555" class="Symbol">→</a> <a id="7557" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#7512" class="Bound">m</a> <a id="7559" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1414" class="Datatype Operator">≤</a> <a id="7561" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#7516" class="Bound">p</a>
<a id="7563" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#7499" class="Function">≤-trans</a> <a id="7571" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1441" class="InductiveConstructor">z≤n</a>       <a id="7581" class="Symbol">_</a>          <a id="7592" class="Symbol">=</a>  <a id="7595" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1441" class="InductiveConstructor">z≤n</a>
<a id="7599" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#7499" class="Function">≤-trans</a> <a id="7607" class="Symbol">(</a><a id="7608" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1490" class="InductiveConstructor">s≤s</a> <a id="7612" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#7612" class="Bound">m≤n</a><a id="7615" class="Symbol">)</a> <a id="7617" class="Symbol">(</a><a id="7618" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1490" class="InductiveConstructor">s≤s</a> <a id="7622" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#7622" class="Bound">n≤p</a><a id="7625" class="Symbol">)</a>  <a id="7628" class="Symbol">=</a>  <a id="7631" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1490" class="InductiveConstructor">s≤s</a> <a id="7635" class="Symbol">(</a><a id="7636" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#7499" class="Function">≤-trans</a> <a id="7644" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#7612" class="Bound">m≤n</a> <a id="7648" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#7622" class="Bound">n≤p</a><a id="7651" class="Symbol">)</a>{% endraw %}</pre>
Here the proof is by induction on the _evidence_ that `m ≤ n`.  In the
base case, the first inequality holds by `z≤n` and must show `zero ≤
p`, which follows immediately by `z≤n`.  In this case, the fact that
`n ≤ p` is irrelevant, and we write `_` as the pattern to indicate
that the corresponding evidence is unused.

In the inductive case, the first inequality holds by `s≤s m≤n`
and the second inequality by `s≤s n≤p`, and so we are given
`suc m ≤ suc n` and `suc n ≤ suc p`, and must show `suc m ≤ suc p`.
The inductive hypothesis `≤-trans m≤n n≤p` establishes
that `m ≤ p`, and our goal follows by applying `s≤s`.

The case `≤-trans (s≤s m≤n) z≤n` cannot arise, since the first
inequality implies the middle value is `suc n` while the second
inequality implies that it is `zero`.  Agda can determine that such a
case cannot arise, and does not require (or permit) it to be listed.

Alternatively, we could make the implicit parameters explicit:
<pre class="Agda">{% raw %}<a id="≤-trans′"></a><a id="8628" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#8628" class="Function">≤-trans′</a> <a id="8637" class="Symbol">:</a> <a id="8639" class="Symbol">∀</a> <a id="8641" class="Symbol">(</a><a id="8642" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#8642" class="Bound">m</a> <a id="8644" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#8644" class="Bound">n</a> <a id="8646" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#8646" class="Bound">p</a> <a id="8648" class="Symbol">:</a> <a id="8650" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a><a id="8651" class="Symbol">)</a>
  <a id="8655" class="Symbol">→</a> <a id="8657" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#8642" class="Bound">m</a> <a id="8659" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1414" class="Datatype Operator">≤</a> <a id="8661" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#8644" class="Bound">n</a>
  <a id="8665" class="Symbol">→</a> <a id="8667" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#8644" class="Bound">n</a> <a id="8669" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1414" class="Datatype Operator">≤</a> <a id="8671" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#8646" class="Bound">p</a>
    <a id="8677" class="Comment">-----</a>
  <a id="8685" class="Symbol">→</a> <a id="8687" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#8642" class="Bound">m</a> <a id="8689" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1414" class="Datatype Operator">≤</a> <a id="8691" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#8646" class="Bound">p</a>
<a id="8693" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#8628" class="Function">≤-trans′</a> <a id="8702" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#115" class="InductiveConstructor">zero</a>    <a id="8710" class="Symbol">_</a>       <a id="8718" class="Symbol">_</a>       <a id="8726" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1441" class="InductiveConstructor">z≤n</a>       <a id="8736" class="Symbol">_</a>          <a id="8747" class="Symbol">=</a>  <a id="8750" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1441" class="InductiveConstructor">z≤n</a>
<a id="8754" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#8628" class="Function">≤-trans′</a> <a id="8763" class="Symbol">(</a><a id="8764" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="8768" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#8768" class="Bound">m</a><a id="8769" class="Symbol">)</a> <a id="8771" class="Symbol">(</a><a id="8772" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="8776" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#8776" class="Bound">n</a><a id="8777" class="Symbol">)</a> <a id="8779" class="Symbol">(</a><a id="8780" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="8784" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#8784" class="Bound">p</a><a id="8785" class="Symbol">)</a> <a id="8787" class="Symbol">(</a><a id="8788" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1490" class="InductiveConstructor">s≤s</a> <a id="8792" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#8792" class="Bound">m≤n</a><a id="8795" class="Symbol">)</a> <a id="8797" class="Symbol">(</a><a id="8798" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1490" class="InductiveConstructor">s≤s</a> <a id="8802" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#8802" class="Bound">n≤p</a><a id="8805" class="Symbol">)</a>  <a id="8808" class="Symbol">=</a>  <a id="8811" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1490" class="InductiveConstructor">s≤s</a> <a id="8815" class="Symbol">(</a><a id="8816" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#8628" class="Function">≤-trans′</a> <a id="8825" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#8768" class="Bound">m</a> <a id="8827" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#8776" class="Bound">n</a> <a id="8829" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#8784" class="Bound">p</a> <a id="8831" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#8792" class="Bound">m≤n</a> <a id="8835" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#8802" class="Bound">n≤p</a><a id="8838" class="Symbol">)</a>{% endraw %}</pre>
One might argue that this is clearer or one might argue that the extra
length obscures the essence of the proof.  We will usually opt for
shorter proofs.

The technique of induction on evidence that a property holds (e.g.,
inducting on evidence that `m ≤ n`)---rather than induction on 
values of which the property holds (e.g., inducting on `m`)---will turn
out to be immensely valuable, and one that we use often.

Again, it is a good exercise to prove transitivity interactively in Emacs,
using holes and the `C-c C-c`, `C-c C-,`, and `C-c C-r` commands.


## Anti-symmetry

The third property to prove about comparison is that it is
antisymmetric: for all naturals `m` and `n`, if both `m ≤ n` and `n ≤
m` hold, then `m ≡ n` holds:
<pre class="Agda">{% raw %}<a id="≤-antisym"></a><a id="9600" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#9600" class="Function">≤-antisym</a> <a id="9610" class="Symbol">:</a> <a id="9612" class="Symbol">∀</a> <a id="9614" class="Symbol">{</a><a id="9615" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#9615" class="Bound">m</a> <a id="9617" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#9617" class="Bound">n</a> <a id="9619" class="Symbol">:</a> <a id="9621" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a><a id="9622" class="Symbol">}</a>
  <a id="9626" class="Symbol">→</a> <a id="9628" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#9615" class="Bound">m</a> <a id="9630" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1414" class="Datatype Operator">≤</a> <a id="9632" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#9617" class="Bound">n</a>
  <a id="9636" class="Symbol">→</a> <a id="9638" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#9617" class="Bound">n</a> <a id="9640" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1414" class="Datatype Operator">≤</a> <a id="9642" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#9615" class="Bound">m</a>
    <a id="9648" class="Comment">-----</a>
  <a id="9656" class="Symbol">→</a> <a id="9658" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#9615" class="Bound">m</a> <a id="9660" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Equality.html#83" class="Datatype Operator">≡</a> <a id="9662" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#9617" class="Bound">n</a>
<a id="9664" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#9600" class="Function">≤-antisym</a> <a id="9674" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1441" class="InductiveConstructor">z≤n</a>       <a id="9684" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1441" class="InductiveConstructor">z≤n</a>        <a id="9695" class="Symbol">=</a>  <a id="9698" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Equality.html#140" class="InductiveConstructor">refl</a>
<a id="9703" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#9600" class="Function">≤-antisym</a> <a id="9713" class="Symbol">(</a><a id="9714" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1490" class="InductiveConstructor">s≤s</a> <a id="9718" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#9718" class="Bound">m≤n</a><a id="9721" class="Symbol">)</a> <a id="9723" class="Symbol">(</a><a id="9724" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1490" class="InductiveConstructor">s≤s</a> <a id="9728" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#9728" class="Bound">n≤m</a><a id="9731" class="Symbol">)</a>  <a id="9734" class="Symbol">=</a>  <a id="9737" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#1056" class="Function">cong</a> <a id="9742" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="9746" class="Symbol">(</a><a id="9747" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#9600" class="Function">≤-antisym</a> <a id="9757" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#9718" class="Bound">m≤n</a> <a id="9761" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#9728" class="Bound">n≤m</a><a id="9764" class="Symbol">)</a>{% endraw %}</pre>
Again, the proof is by induction over the evidence that `m ≤ n`
and `n ≤ m` hold.

In the base case, both inequalities hold by `z≤n`, and so we are given
`zero ≤ zero` and `zero ≤ zero` and must show `zero ≡ zero`, which
follows by reflexivity.  (Reflexivity of equality, that is, not
reflexivity of inequality.)

In the inductive case, the first inequality holds by `s≤s m≤n` and the
second inequality holds by `s≤s n≤m`, and so we are given `suc m ≤ suc n`
and `suc n ≤ suc m` and must show `suc m ≡ suc n`.  The inductive
hypothesis `≤-antisym m≤n n≤m` establishes that `m ≡ n`, and our goal
follows by congruence.

#### Exercise `≤-antisym-cases` {#leq-antisym-cases} 

The above proof omits cases where one argument is `z≤n` and one
argument is `s≤s`.  Why is it ok to omit them?


## Total

The fourth property to prove about comparison is that it is total:
for any naturals `m` and `n` either `m ≤ n` or `n ≤ m`, or both if
`m` and `n` are equal.

We specify what it means for inequality to be total:
<pre class="Agda">{% raw %}<a id="10798" class="Keyword">data</a> <a id="Total"></a><a id="10803" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#10803" class="Datatype">Total</a> <a id="10809" class="Symbol">(</a><a id="10810" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#10810" class="Bound">m</a> <a id="10812" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#10812" class="Bound">n</a> <a id="10814" class="Symbol">:</a> <a id="10816" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a><a id="10817" class="Symbol">)</a> <a id="10819" class="Symbol">:</a> <a id="10821" class="PrimitiveType">Set</a> <a id="10825" class="Keyword">where</a>

  <a id="Total.forward"></a><a id="10834" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#10834" class="InductiveConstructor">forward</a> <a id="10842" class="Symbol">:</a>
      <a id="10850" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#10810" class="Bound">m</a> <a id="10852" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1414" class="Datatype Operator">≤</a> <a id="10854" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#10812" class="Bound">n</a>
      <a id="10862" class="Comment">---------</a>
    <a id="10876" class="Symbol">→</a> <a id="10878" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#10803" class="Datatype">Total</a> <a id="10884" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#10810" class="Bound">m</a> <a id="10886" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#10812" class="Bound">n</a>

  <a id="Total.flipped"></a><a id="10891" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#10891" class="InductiveConstructor">flipped</a> <a id="10899" class="Symbol">:</a>
      <a id="10907" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#10812" class="Bound">n</a> <a id="10909" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1414" class="Datatype Operator">≤</a> <a id="10911" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#10810" class="Bound">m</a>
      <a id="10919" class="Comment">---------</a>
    <a id="10933" class="Symbol">→</a> <a id="10935" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#10803" class="Datatype">Total</a> <a id="10941" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#10810" class="Bound">m</a> <a id="10943" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#10812" class="Bound">n</a>{% endraw %}</pre>
Evidence that `Total m n` holds is either of the form
`forward m≤n` or `flipped n≤m`, where `m≤n` and `n≤m` are
evidence of `m ≤ n` and `n ≤ m` respectively.

(For those familiar with logic, the above definition
could also be written as a disjunction. Disjunctions will
be introduced in Chapter [Connectives]({{ site.baseurl }}{% link out/plfa/Connectives.md%}).)

This is our first use of a datatype with _parameters_,
in this case `m` and `n`.  It is equivalent to the following
indexed datatype:
<pre class="Agda">{% raw %}<a id="11433" class="Keyword">data</a> <a id="Total′"></a><a id="11438" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#11438" class="Datatype">Total′</a> <a id="11445" class="Symbol">:</a> <a id="11447" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a> <a id="11449" class="Symbol">→</a> <a id="11451" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a> <a id="11453" class="Symbol">→</a> <a id="11455" class="PrimitiveType">Set</a> <a id="11459" class="Keyword">where</a>

  <a id="Total′.forward′"></a><a id="11468" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#11468" class="InductiveConstructor">forward′</a> <a id="11477" class="Symbol">:</a> <a id="11479" class="Symbol">∀</a> <a id="11481" class="Symbol">{</a><a id="11482" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#11482" class="Bound">m</a> <a id="11484" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#11484" class="Bound">n</a> <a id="11486" class="Symbol">:</a> <a id="11488" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a><a id="11489" class="Symbol">}</a>
    <a id="11495" class="Symbol">→</a> <a id="11497" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#11482" class="Bound">m</a> <a id="11499" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1414" class="Datatype Operator">≤</a> <a id="11501" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#11484" class="Bound">n</a>
      <a id="11509" class="Comment">----------</a>
    <a id="11524" class="Symbol">→</a> <a id="11526" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#11438" class="Datatype">Total′</a> <a id="11533" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#11482" class="Bound">m</a> <a id="11535" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#11484" class="Bound">n</a>

  <a id="Total′.flipped′"></a><a id="11540" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#11540" class="InductiveConstructor">flipped′</a> <a id="11549" class="Symbol">:</a> <a id="11551" class="Symbol">∀</a> <a id="11553" class="Symbol">{</a><a id="11554" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#11554" class="Bound">m</a> <a id="11556" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#11556" class="Bound">n</a> <a id="11558" class="Symbol">:</a> <a id="11560" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a><a id="11561" class="Symbol">}</a>
    <a id="11567" class="Symbol">→</a> <a id="11569" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#11556" class="Bound">n</a> <a id="11571" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1414" class="Datatype Operator">≤</a> <a id="11573" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#11554" class="Bound">m</a>
      <a id="11581" class="Comment">----------</a>
    <a id="11596" class="Symbol">→</a> <a id="11598" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#11438" class="Datatype">Total′</a> <a id="11605" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#11554" class="Bound">m</a> <a id="11607" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#11556" class="Bound">n</a>{% endraw %}</pre>
Each parameter of the type translates as an implicit parameter of each
constructor.  Unlike an indexed datatype, where the indexes can vary
(as in `zero ≤ n` and `suc m ≤ suc n`), in a parameterised datatype
the parameters must always be the same (as in `Total m n`).
Parameterised declarations are shorter, easier to read, and
occcasionally aid Agda's termination checker, so we will use them in
preference to indexed types when possible.

With that preliminary out of the way, we specify and prove totality:
<pre class="Agda">{% raw %}<a id="≤-total"></a><a id="12143" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#12143" class="Function">≤-total</a> <a id="12151" class="Symbol">:</a> <a id="12153" class="Symbol">∀</a> <a id="12155" class="Symbol">(</a><a id="12156" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#12156" class="Bound">m</a> <a id="12158" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#12158" class="Bound">n</a> <a id="12160" class="Symbol">:</a> <a id="12162" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a><a id="12163" class="Symbol">)</a> <a id="12165" class="Symbol">→</a> <a id="12167" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#10803" class="Datatype">Total</a> <a id="12173" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#12156" class="Bound">m</a> <a id="12175" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#12158" class="Bound">n</a>
<a id="12177" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#12143" class="Function">≤-total</a> <a id="12185" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#115" class="InductiveConstructor">zero</a>    <a id="12193" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#12193" class="Bound">n</a>                         <a id="12219" class="Symbol">=</a>  <a id="12222" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#10834" class="InductiveConstructor">forward</a> <a id="12230" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1441" class="InductiveConstructor">z≤n</a>
<a id="12234" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#12143" class="Function">≤-total</a> <a id="12242" class="Symbol">(</a><a id="12243" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="12247" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#12247" class="Bound">m</a><a id="12248" class="Symbol">)</a> <a id="12250" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#115" class="InductiveConstructor">zero</a>                      <a id="12276" class="Symbol">=</a>  <a id="12279" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#10891" class="InductiveConstructor">flipped</a> <a id="12287" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1441" class="InductiveConstructor">z≤n</a>
<a id="12291" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#12143" class="Function">≤-total</a> <a id="12299" class="Symbol">(</a><a id="12300" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="12304" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#12304" class="Bound">m</a><a id="12305" class="Symbol">)</a> <a id="12307" class="Symbol">(</a><a id="12308" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="12312" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#12312" class="Bound">n</a><a id="12313" class="Symbol">)</a> <a id="12315" class="Keyword">with</a> <a id="12320" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#12143" class="Function">≤-total</a> <a id="12328" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#12304" class="Bound">m</a> <a id="12330" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#12312" class="Bound">n</a>
<a id="12332" class="Symbol">...</a>                        <a id="12359" class="Symbol">|</a> <a id="12361" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#10834" class="InductiveConstructor">forward</a> <a id="12369" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#12369" class="Bound">m≤n</a>  <a id="12374" class="Symbol">=</a>  <a id="12377" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#10834" class="InductiveConstructor">forward</a> <a id="12385" class="Symbol">(</a><a id="12386" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1490" class="InductiveConstructor">s≤s</a> <a id="12390" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#12369" class="Bound">m≤n</a><a id="12393" class="Symbol">)</a>
<a id="12395" class="Symbol">...</a>                        <a id="12422" class="Symbol">|</a> <a id="12424" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#10891" class="InductiveConstructor">flipped</a> <a id="12432" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#12432" class="Bound">n≤m</a>  <a id="12437" class="Symbol">=</a>  <a id="12440" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#10891" class="InductiveConstructor">flipped</a> <a id="12448" class="Symbol">(</a><a id="12449" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1490" class="InductiveConstructor">s≤s</a> <a id="12453" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#12432" class="Bound">n≤m</a><a id="12456" class="Symbol">)</a>{% endraw %}</pre>
In this case the proof is by induction over both the first
and second arguments.  We perform a case analysis:

* _First base case_: If the first argument is `zero` and the
  second argument is `n` then the forward case holds,
  with `z≤n` as evidence that `zero ≤ n`.

* _Second base case_: If the first argument is `suc m` and the
  second argument is `zero` then the flipped case holds, with
  `z≤n` as evidence that `zero ≤ suc m`.

* _Inductive case_: If the first argument is `suc m` and the
  second argument is `suc n`, then the inductive hypothesis
  `≤-total m n` establishes one of the following:

  + The forward case of the inductive hypothesis holds with `m≤n` as
    evidence that `m ≤ n`, from which it follows that the forward case of the
    proposition holds with `s≤s m≤n` as evidence that `suc m ≤ suc n`.

  + The flipped case of the inductive hypothesis holds with `n≤m` as
    evidence that `n ≤ m`, from which it follows that the flipped case of the
    proposition holds with `s≤s n≤m` as evidence that `suc n ≤ suc m`.

This is our first use of the `with` clause in Agda.  The keyword
`with` is followed by an expression and one or more subsequent lines.
Each line begins with an ellipsis (`...`) and a vertical bar (`|`),
followed by a pattern to be matched against the expression
and the right-hand side of the equation.

Every use of `with` is equivalent to defining a helper function.  For
example, the definition above is equivalent to the following:
<pre class="Agda">{% raw %}<a id="≤-total′"></a><a id="13964" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#13964" class="Function">≤-total′</a> <a id="13973" class="Symbol">:</a> <a id="13975" class="Symbol">∀</a> <a id="13977" class="Symbol">(</a><a id="13978" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#13978" class="Bound">m</a> <a id="13980" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#13980" class="Bound">n</a> <a id="13982" class="Symbol">:</a> <a id="13984" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a><a id="13985" class="Symbol">)</a> <a id="13987" class="Symbol">→</a> <a id="13989" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#10803" class="Datatype">Total</a> <a id="13995" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#13978" class="Bound">m</a> <a id="13997" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#13980" class="Bound">n</a>
<a id="13999" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#13964" class="Function">≤-total′</a> <a id="14008" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#115" class="InductiveConstructor">zero</a>    <a id="14016" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#14016" class="Bound">n</a>        <a id="14025" class="Symbol">=</a>  <a id="14028" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#10834" class="InductiveConstructor">forward</a> <a id="14036" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1441" class="InductiveConstructor">z≤n</a>
<a id="14040" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#13964" class="Function">≤-total′</a> <a id="14049" class="Symbol">(</a><a id="14050" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="14054" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#14054" class="Bound">m</a><a id="14055" class="Symbol">)</a> <a id="14057" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#115" class="InductiveConstructor">zero</a>     <a id="14066" class="Symbol">=</a>  <a id="14069" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#10891" class="InductiveConstructor">flipped</a> <a id="14077" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1441" class="InductiveConstructor">z≤n</a>
<a id="14081" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#13964" class="Function">≤-total′</a> <a id="14090" class="Symbol">(</a><a id="14091" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="14095" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#14095" class="Bound">m</a><a id="14096" class="Symbol">)</a> <a id="14098" class="Symbol">(</a><a id="14099" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="14103" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#14103" class="Bound">n</a><a id="14104" class="Symbol">)</a>  <a id="14107" class="Symbol">=</a>  <a id="14110" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#14142" class="Function">helper</a> <a id="14117" class="Symbol">(</a><a id="14118" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#13964" class="Function">≤-total′</a> <a id="14127" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#14095" class="Bound">m</a> <a id="14129" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#14103" class="Bound">n</a><a id="14130" class="Symbol">)</a>
  <a id="14134" class="Keyword">where</a>
  <a id="14142" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#14142" class="Function">helper</a> <a id="14149" class="Symbol">:</a> <a id="14151" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#10803" class="Datatype">Total</a> <a id="14157" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#14095" class="Bound">m</a> <a id="14159" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#14103" class="Bound">n</a> <a id="14161" class="Symbol">→</a> <a id="14163" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#10803" class="Datatype">Total</a> <a id="14169" class="Symbol">(</a><a id="14170" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="14174" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#14095" class="Bound">m</a><a id="14175" class="Symbol">)</a> <a id="14177" class="Symbol">(</a><a id="14178" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="14182" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#14103" class="Bound">n</a><a id="14183" class="Symbol">)</a>
  <a id="14187" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#14142" class="Function">helper</a> <a id="14194" class="Symbol">(</a><a id="14195" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#10834" class="InductiveConstructor">forward</a> <a id="14203" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#14203" class="Bound">m≤n</a><a id="14206" class="Symbol">)</a>  <a id="14209" class="Symbol">=</a>  <a id="14212" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#10834" class="InductiveConstructor">forward</a> <a id="14220" class="Symbol">(</a><a id="14221" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1490" class="InductiveConstructor">s≤s</a> <a id="14225" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#14203" class="Bound">m≤n</a><a id="14228" class="Symbol">)</a>
  <a id="14232" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#14142" class="Function">helper</a> <a id="14239" class="Symbol">(</a><a id="14240" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#10891" class="InductiveConstructor">flipped</a> <a id="14248" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#14248" class="Bound">n≤m</a><a id="14251" class="Symbol">)</a>  <a id="14254" class="Symbol">=</a>  <a id="14257" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#10891" class="InductiveConstructor">flipped</a> <a id="14265" class="Symbol">(</a><a id="14266" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1490" class="InductiveConstructor">s≤s</a> <a id="14270" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#14248" class="Bound">n≤m</a><a id="14273" class="Symbol">)</a>{% endraw %}</pre>
This is also our first use of a `where` clause in Agda.  The keyword `where` is
followed by one or more definitions, which must be indented.  Any variables
bound of the left-hand side of the preceding equation (in this case, `m` and
`n`) are in scope within the nested definition, and any identifiers bound in the
nested definition (in this case, `helper`) are in scope in the right-hand side
of the preceding equation.

If both arguments are equal, then both cases hold and we could return evidence
of either.  In the code above we return the forward case, but there is a
variant that returns the flipped case:
<pre class="Agda">{% raw %}<a id="≤-total″"></a><a id="14911" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#14911" class="Function">≤-total″</a> <a id="14920" class="Symbol">:</a> <a id="14922" class="Symbol">∀</a> <a id="14924" class="Symbol">(</a><a id="14925" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#14925" class="Bound">m</a> <a id="14927" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#14927" class="Bound">n</a> <a id="14929" class="Symbol">:</a> <a id="14931" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a><a id="14932" class="Symbol">)</a> <a id="14934" class="Symbol">→</a> <a id="14936" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#10803" class="Datatype">Total</a> <a id="14942" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#14925" class="Bound">m</a> <a id="14944" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#14927" class="Bound">n</a>
<a id="14946" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#14911" class="Function">≤-total″</a> <a id="14955" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#14955" class="Bound">m</a>       <a id="14963" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#115" class="InductiveConstructor">zero</a>                      <a id="14989" class="Symbol">=</a>  <a id="14992" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#10891" class="InductiveConstructor">flipped</a> <a id="15000" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1441" class="InductiveConstructor">z≤n</a>
<a id="15004" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#14911" class="Function">≤-total″</a> <a id="15013" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#115" class="InductiveConstructor">zero</a>    <a id="15021" class="Symbol">(</a><a id="15022" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="15026" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#15026" class="Bound">n</a><a id="15027" class="Symbol">)</a>                   <a id="15047" class="Symbol">=</a>  <a id="15050" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#10834" class="InductiveConstructor">forward</a> <a id="15058" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1441" class="InductiveConstructor">z≤n</a>
<a id="15062" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#14911" class="Function">≤-total″</a> <a id="15071" class="Symbol">(</a><a id="15072" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="15076" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#15076" class="Bound">m</a><a id="15077" class="Symbol">)</a> <a id="15079" class="Symbol">(</a><a id="15080" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="15084" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#15084" class="Bound">n</a><a id="15085" class="Symbol">)</a> <a id="15087" class="Keyword">with</a> <a id="15092" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#14911" class="Function">≤-total″</a> <a id="15101" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#15076" class="Bound">m</a> <a id="15103" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#15084" class="Bound">n</a>
<a id="15105" class="Symbol">...</a>                        <a id="15132" class="Symbol">|</a> <a id="15134" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#10834" class="InductiveConstructor">forward</a> <a id="15142" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#15142" class="Bound">m≤n</a>   <a id="15148" class="Symbol">=</a>  <a id="15151" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#10834" class="InductiveConstructor">forward</a> <a id="15159" class="Symbol">(</a><a id="15160" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1490" class="InductiveConstructor">s≤s</a> <a id="15164" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#15142" class="Bound">m≤n</a><a id="15167" class="Symbol">)</a>
<a id="15169" class="Symbol">...</a>                        <a id="15196" class="Symbol">|</a> <a id="15198" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#10891" class="InductiveConstructor">flipped</a> <a id="15206" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#15206" class="Bound">n≤m</a>   <a id="15212" class="Symbol">=</a>  <a id="15215" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#10891" class="InductiveConstructor">flipped</a> <a id="15223" class="Symbol">(</a><a id="15224" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1490" class="InductiveConstructor">s≤s</a> <a id="15228" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#15206" class="Bound">n≤m</a><a id="15231" class="Symbol">)</a>{% endraw %}</pre>
It differs from the original code in that it pattern
matches on the second argument before the first argument.


## Monotonicity

If one bumps into both an operator and an ordering at a party, one may ask if
the operator is _monotonic_ with regard to the ordering.  For example, addition
is monotonic with regard to inequality, meaning:

    ∀ {m n p q : ℕ} → m ≤ n → p ≤ q → m + p ≤ n + q

The proof is straightforward using the techniques we have learned, and is best
broken into three parts. First, we deal with the special case of showing
addition is monotonic on the right:
<pre class="Agda">{% raw %}<a id="+-monoʳ-≤"></a><a id="15836" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#15836" class="Function">+-monoʳ-≤</a> <a id="15846" class="Symbol">:</a> <a id="15848" class="Symbol">∀</a> <a id="15850" class="Symbol">(</a><a id="15851" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#15851" class="Bound">m</a> <a id="15853" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#15853" class="Bound">p</a> <a id="15855" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#15855" class="Bound">q</a> <a id="15857" class="Symbol">:</a> <a id="15859" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a><a id="15860" class="Symbol">)</a>
  <a id="15864" class="Symbol">→</a> <a id="15866" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#15853" class="Bound">p</a> <a id="15868" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1414" class="Datatype Operator">≤</a> <a id="15870" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#15855" class="Bound">q</a>
    <a id="15876" class="Comment">-------------</a>
  <a id="15892" class="Symbol">→</a> <a id="15894" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#15851" class="Bound">m</a> <a id="15896" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="15898" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#15853" class="Bound">p</a> <a id="15900" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1414" class="Datatype Operator">≤</a> <a id="15902" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#15851" class="Bound">m</a> <a id="15904" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="15906" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#15855" class="Bound">q</a>
<a id="15908" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#15836" class="Function">+-monoʳ-≤</a> <a id="15918" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#115" class="InductiveConstructor">zero</a>    <a id="15926" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#15926" class="Bound">p</a> <a id="15928" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#15928" class="Bound">q</a> <a id="15930" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#15930" class="Bound">p≤q</a>  <a id="15935" class="Symbol">=</a>  <a id="15938" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#15930" class="Bound">p≤q</a>
<a id="15942" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#15836" class="Function">+-monoʳ-≤</a> <a id="15952" class="Symbol">(</a><a id="15953" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="15957" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#15957" class="Bound">m</a><a id="15958" class="Symbol">)</a> <a id="15960" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#15960" class="Bound">p</a> <a id="15962" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#15962" class="Bound">q</a> <a id="15964" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#15964" class="Bound">p≤q</a>  <a id="15969" class="Symbol">=</a>  <a id="15972" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1490" class="InductiveConstructor">s≤s</a> <a id="15976" class="Symbol">(</a><a id="15977" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#15836" class="Function">+-monoʳ-≤</a> <a id="15987" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#15957" class="Bound">m</a> <a id="15989" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#15960" class="Bound">p</a> <a id="15991" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#15962" class="Bound">q</a> <a id="15993" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#15964" class="Bound">p≤q</a><a id="15996" class="Symbol">)</a>{% endraw %}</pre>
The proof is by induction on the first argument.

* _Base case_: The first argument is `zero` in which case
  `zero + p ≤ zero + q` simplifies to `p ≤ q`, the evidence
  for which is given by the argument `p≤q`.

* _Inductive case_: The first argument is `suc m`, in which case
  `suc m + p ≤ suc m + q` simplifies to `suc (m + p) ≤ suc (m + q)`.
  The inductive hypothesis `+-monoʳ-≤ m p q p≤q` establishes that
  `m + p ≤ m + q`, and our goal follows by applying `s≤s`.

Second, we deal with the special case of showing addition is
monotonic on the left. This follows from the previous
result and the commutativity of addition:
<pre class="Agda">{% raw %}<a id="+-monoˡ-≤"></a><a id="16652" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#16652" class="Function">+-monoˡ-≤</a> <a id="16662" class="Symbol">:</a> <a id="16664" class="Symbol">∀</a> <a id="16666" class="Symbol">(</a><a id="16667" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#16667" class="Bound">m</a> <a id="16669" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#16669" class="Bound">n</a> <a id="16671" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#16671" class="Bound">p</a> <a id="16673" class="Symbol">:</a> <a id="16675" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a><a id="16676" class="Symbol">)</a>
  <a id="16680" class="Symbol">→</a> <a id="16682" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#16667" class="Bound">m</a> <a id="16684" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1414" class="Datatype Operator">≤</a> <a id="16686" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#16669" class="Bound">n</a>
    <a id="16692" class="Comment">-------------</a>
  <a id="16708" class="Symbol">→</a> <a id="16710" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#16667" class="Bound">m</a> <a id="16712" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="16714" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#16671" class="Bound">p</a> <a id="16716" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1414" class="Datatype Operator">≤</a> <a id="16718" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#16669" class="Bound">n</a> <a id="16720" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="16722" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#16671" class="Bound">p</a>
<a id="16724" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#16652" class="Function">+-monoˡ-≤</a> <a id="16734" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#16734" class="Bound">m</a> <a id="16736" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#16736" class="Bound">n</a> <a id="16738" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#16738" class="Bound">p</a> <a id="16740" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#16740" class="Bound">m≤n</a>  <a id="16745" class="Keyword">rewrite</a> <a id="16753" href="https://agda.github.io/agda-stdlib/Data.Nat.Properties.html#8011" class="Function">+-comm</a> <a id="16760" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#16734" class="Bound">m</a> <a id="16762" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#16738" class="Bound">p</a> <a id="16764" class="Symbol">|</a> <a id="16766" href="https://agda.github.io/agda-stdlib/Data.Nat.Properties.html#8011" class="Function">+-comm</a> <a id="16773" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#16736" class="Bound">n</a> <a id="16775" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#16738" class="Bound">p</a>  <a id="16778" class="Symbol">=</a> <a id="16780" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#15836" class="Function">+-monoʳ-≤</a> <a id="16790" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#16738" class="Bound">p</a> <a id="16792" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#16734" class="Bound">m</a> <a id="16794" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#16736" class="Bound">n</a> <a id="16796" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#16740" class="Bound">m≤n</a>{% endraw %}</pre>
Rewriting by `+-comm m p` and `+-comm n p` converts `m + p ≤ n + p` into
`p + m ≤ p + n`, which is proved by invoking `+-monoʳ-≤ p m n m≤n`.

Third, we combine the two previous results:
<pre class="Agda">{% raw %}<a id="+-mono-≤"></a><a id="17010" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#17010" class="Function">+-mono-≤</a> <a id="17019" class="Symbol">:</a> <a id="17021" class="Symbol">∀</a> <a id="17023" class="Symbol">(</a><a id="17024" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#17024" class="Bound">m</a> <a id="17026" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#17026" class="Bound">n</a> <a id="17028" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#17028" class="Bound">p</a> <a id="17030" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#17030" class="Bound">q</a> <a id="17032" class="Symbol">:</a> <a id="17034" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a><a id="17035" class="Symbol">)</a>
  <a id="17039" class="Symbol">→</a> <a id="17041" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#17024" class="Bound">m</a> <a id="17043" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1414" class="Datatype Operator">≤</a> <a id="17045" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#17026" class="Bound">n</a>
  <a id="17049" class="Symbol">→</a> <a id="17051" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#17028" class="Bound">p</a> <a id="17053" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1414" class="Datatype Operator">≤</a> <a id="17055" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#17030" class="Bound">q</a>
    <a id="17061" class="Comment">-------------</a>
  <a id="17077" class="Symbol">→</a> <a id="17079" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#17024" class="Bound">m</a> <a id="17081" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="17083" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#17028" class="Bound">p</a> <a id="17085" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1414" class="Datatype Operator">≤</a> <a id="17087" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#17026" class="Bound">n</a> <a id="17089" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="17091" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#17030" class="Bound">q</a>
<a id="17093" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#17010" class="Function">+-mono-≤</a> <a id="17102" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#17102" class="Bound">m</a> <a id="17104" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#17104" class="Bound">n</a> <a id="17106" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#17106" class="Bound">p</a> <a id="17108" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#17108" class="Bound">q</a> <a id="17110" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#17110" class="Bound">m≤n</a> <a id="17114" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#17114" class="Bound">p≤q</a>  <a id="17119" class="Symbol">=</a>  <a id="17122" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#7499" class="Function">≤-trans</a> <a id="17130" class="Symbol">(</a><a id="17131" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#16652" class="Function">+-monoˡ-≤</a> <a id="17141" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#17102" class="Bound">m</a> <a id="17143" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#17104" class="Bound">n</a> <a id="17145" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#17106" class="Bound">p</a> <a id="17147" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#17110" class="Bound">m≤n</a><a id="17150" class="Symbol">)</a> <a id="17152" class="Symbol">(</a><a id="17153" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#15836" class="Function">+-monoʳ-≤</a> <a id="17163" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#17104" class="Bound">n</a> <a id="17165" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#17106" class="Bound">p</a> <a id="17167" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#17108" class="Bound">q</a> <a id="17169" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#17114" class="Bound">p≤q</a><a id="17172" class="Symbol">)</a>{% endraw %}</pre>
Invoking `+-monoˡ-≤ m n p m≤n` proves `m + p ≤ n + p` and invoking
`+-monoʳ-≤ n p q p≤q` proves `n + p ≤ n + q`, and combining these with
transitivity proves `m + p ≤ n + q`, as was to be shown.


#### Exercise `*-mono-≤` (stretch)

Show that multiplication is monotonic with regard to inequality.


## Strict inequality {#strict-inequality}

We can define strict inequality similarly to inequality:
<pre class="Agda">{% raw %}<a id="17598" class="Keyword">infix</a> <a id="17604" class="Number">4</a> <a id="17606" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#17616" class="Datatype Operator">_&lt;_</a>

<a id="17611" class="Keyword">data</a> <a id="_&lt;_"></a><a id="17616" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#17616" class="Datatype Operator">_&lt;_</a> <a id="17620" class="Symbol">:</a> <a id="17622" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a> <a id="17624" class="Symbol">→</a> <a id="17626" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a> <a id="17628" class="Symbol">→</a> <a id="17630" class="PrimitiveType">Set</a> <a id="17634" class="Keyword">where</a>

  <a id="_&lt;_.z&lt;s"></a><a id="17643" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#17643" class="InductiveConstructor">z&lt;s</a> <a id="17647" class="Symbol">:</a> <a id="17649" class="Symbol">∀</a> <a id="17651" class="Symbol">{</a><a id="17652" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#17652" class="Bound">n</a> <a id="17654" class="Symbol">:</a> <a id="17656" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a><a id="17657" class="Symbol">}</a>
      <a id="17665" class="Comment">------------</a>
    <a id="17682" class="Symbol">→</a> <a id="17684" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#115" class="InductiveConstructor">zero</a> <a id="17689" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#17616" class="Datatype Operator">&lt;</a> <a id="17691" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="17695" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#17652" class="Bound">n</a>

  <a id="_&lt;_.s&lt;s"></a><a id="17700" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#17700" class="InductiveConstructor">s&lt;s</a> <a id="17704" class="Symbol">:</a> <a id="17706" class="Symbol">∀</a> <a id="17708" class="Symbol">{</a><a id="17709" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#17709" class="Bound">m</a> <a id="17711" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#17711" class="Bound">n</a> <a id="17713" class="Symbol">:</a> <a id="17715" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a><a id="17716" class="Symbol">}</a>
    <a id="17722" class="Symbol">→</a> <a id="17724" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#17709" class="Bound">m</a> <a id="17726" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#17616" class="Datatype Operator">&lt;</a> <a id="17728" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#17711" class="Bound">n</a>
      <a id="17736" class="Comment">-------------</a>
    <a id="17754" class="Symbol">→</a> <a id="17756" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="17760" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#17709" class="Bound">m</a> <a id="17762" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#17616" class="Datatype Operator">&lt;</a> <a id="17764" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="17768" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#17711" class="Bound">n</a>{% endraw %}</pre>
The key difference is that zero is less than the successor of an
arbitrary number, but is not less than zero.

Clearly, strict inequality is not reflexive. However it is
_irreflexive_ in that `n < n` never holds for any value of `n`.
Like inequality, strict inequality is transitive.
Strict inequality is not total, but satisfies the closely related property of
_trichotomy_: for any `m` and `n`, exactly one of `m < n`, `m ≡ n`, or `m > n`
holds (where we define `m > n` to hold exactly when `n < m`).
It is also monotonic with regards to addition and multiplication.

Most of the above are considered in exercises below.  Irreflexivity
requires negation, as does the fact that the three cases in
trichotomy are mutually exclusive, so those points are deferred to
Chapter [Negation]({{ site.baseurl }}{% link out/plfa/Negation.md%}).

It is straightforward to show that `suc m ≤ n` implies `m < n`,
and conversely.  One can then give an alternative derivation of the
properties of strict inequality, such as transitivity, by directly
exploiting the corresponding properties of inequality.

#### Exercise `<-trans` (recommended) {#less-trans}

Show that strict inequality is transitive.

#### Exercise `trichotomy` {#trichotomy}

Show that strict inequality satisfies a weak version of trichotomy, in
the sense that for any `m` and `n` that one of the following holds:
  * `m < n`,
  * `m ≡ n`, or
  * `m > n`
Define `m > n` to be the same as `n < m`.
You will need a suitable data declaration,
similar to that used for totality.
(We will show that the three cases are exclusive after we introduce
[negation]({{ site.baseurl }}{% link out/plfa/Negation.md%}).)

#### Exercise `+-mono-<` {#plus-mono-less}

Show that addition is monotonic with respect to strict inequality.
As with inequality, some additional definitions may be required.

#### Exercise `≤-iff-<` (recommended) {#leq-iff-less}

Show that `suc m ≤ n` implies `m < n`, and conversely.

#### Exercise `<-trans-revisited` {#less-trans-revisited}

Give an alternative proof that strict inequality is transitive,
using the relating between strict inequality and inequality and
the fact that inequality is transitive.


## Even and odd

As a further example, let's specify even and odd numbers.  Inequality
and strict inequality are _binary relations_, while even and odd are
_unary relations_, sometimes called _predicates_:
<pre class="Agda">{% raw %}<a id="20109" class="Keyword">data</a> <a id="even"></a><a id="20114" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#20114" class="Datatype">even</a> <a id="20119" class="Symbol">:</a> <a id="20121" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a> <a id="20123" class="Symbol">→</a> <a id="20125" class="PrimitiveType">Set</a>
<a id="20129" class="Keyword">data</a> <a id="odd"></a><a id="20134" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#20134" class="Datatype">odd</a>  <a id="20139" class="Symbol">:</a> <a id="20141" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a> <a id="20143" class="Symbol">→</a> <a id="20145" class="PrimitiveType">Set</a>

<a id="20150" class="Keyword">data</a> <a id="20155" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#20114" class="Datatype">even</a> <a id="20160" class="Keyword">where</a>

  <a id="even.zero"></a><a id="20169" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#20169" class="InductiveConstructor">zero</a> <a id="20174" class="Symbol">:</a>
      <a id="20182" class="Comment">---------</a>
      <a id="20198" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#20114" class="Datatype">even</a> <a id="20203" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#115" class="InductiveConstructor">zero</a>

  <a id="even.suc"></a><a id="20211" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#20211" class="InductiveConstructor">suc</a>  <a id="20216" class="Symbol">:</a> <a id="20218" class="Symbol">∀</a> <a id="20220" class="Symbol">{</a><a id="20221" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#20221" class="Bound">n</a> <a id="20223" class="Symbol">:</a> <a id="20225" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a><a id="20226" class="Symbol">}</a>
    <a id="20232" class="Symbol">→</a> <a id="20234" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#20134" class="Datatype">odd</a> <a id="20238" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#20221" class="Bound">n</a>
      <a id="20246" class="Comment">------------</a>
    <a id="20263" class="Symbol">→</a> <a id="20265" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#20114" class="Datatype">even</a> <a id="20270" class="Symbol">(</a><a id="20271" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="20275" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#20221" class="Bound">n</a><a id="20276" class="Symbol">)</a>

<a id="20279" class="Keyword">data</a> <a id="20284" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#20134" class="Datatype">odd</a> <a id="20288" class="Keyword">where</a>

  <a id="odd.suc"></a><a id="20297" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#20297" class="InductiveConstructor">suc</a>   <a id="20303" class="Symbol">:</a> <a id="20305" class="Symbol">∀</a> <a id="20307" class="Symbol">{</a><a id="20308" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#20308" class="Bound">n</a> <a id="20310" class="Symbol">:</a> <a id="20312" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a><a id="20313" class="Symbol">}</a>
    <a id="20319" class="Symbol">→</a> <a id="20321" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#20114" class="Datatype">even</a> <a id="20326" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#20308" class="Bound">n</a>
      <a id="20334" class="Comment">-----------</a>
    <a id="20350" class="Symbol">→</a> <a id="20352" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#20134" class="Datatype">odd</a> <a id="20356" class="Symbol">(</a><a id="20357" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="20361" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#20308" class="Bound">n</a><a id="20362" class="Symbol">)</a>{% endraw %}</pre>
A number is even if it is zero or the successor of an odd number,
and odd if it is the successor of an even number.

This is our first use of a mutually recursive datatype declaration.
Since each identifier must be defined before it is used, we first
declare the indexed types `even` and `odd` (omitting the `where`
keyword and the declarations of the constructors) and then
declare the constructors (omitting the signatures `ℕ → Set`
which were given earlier).

This is also our first use of _overloaded_ constructors,
that is, using the same name for constructors of different types.
Here `suc` means one of three constructors:

    suc : ℕ → ℕ

    suc : ∀ {n : ℕ}
      → odd n
        ------------
      → even (suc n)

    suc : ∀ {n : ℕ}
      → even n
        -----------
      → odd (suc n)

Similarly, `zero` refers to one of two constructors. Due to how it
does type inference, Agda does not allow overloading of defined names,
but does allow overloading of constructors.  It is recommended that
one restrict overloading to related meanings, as we have done here,
but it is not required.

We show that the sum of two even numbers is even:
<pre class="Agda">{% raw %}<a id="e+e≡e"></a><a id="21538" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#21538" class="Function">e+e≡e</a> <a id="21544" class="Symbol">:</a> <a id="21546" class="Symbol">∀</a> <a id="21548" class="Symbol">{</a><a id="21549" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#21549" class="Bound">m</a> <a id="21551" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#21551" class="Bound">n</a> <a id="21553" class="Symbol">:</a> <a id="21555" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a><a id="21556" class="Symbol">}</a>
  <a id="21560" class="Symbol">→</a> <a id="21562" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#20114" class="Datatype">even</a> <a id="21567" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#21549" class="Bound">m</a>
  <a id="21571" class="Symbol">→</a> <a id="21573" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#20114" class="Datatype">even</a> <a id="21578" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#21551" class="Bound">n</a>
    <a id="21584" class="Comment">------------</a>
  <a id="21599" class="Symbol">→</a> <a id="21601" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#20114" class="Datatype">even</a> <a id="21606" class="Symbol">(</a><a id="21607" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#21549" class="Bound">m</a> <a id="21609" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="21611" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#21551" class="Bound">n</a><a id="21612" class="Symbol">)</a>

<a id="o+e≡o"></a><a id="21615" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#21615" class="Function">o+e≡o</a> <a id="21621" class="Symbol">:</a> <a id="21623" class="Symbol">∀</a> <a id="21625" class="Symbol">{</a><a id="21626" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#21626" class="Bound">m</a> <a id="21628" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#21628" class="Bound">n</a> <a id="21630" class="Symbol">:</a> <a id="21632" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a><a id="21633" class="Symbol">}</a>
  <a id="21637" class="Symbol">→</a> <a id="21639" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#20134" class="Datatype">odd</a> <a id="21643" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#21626" class="Bound">m</a>
  <a id="21647" class="Symbol">→</a> <a id="21649" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#20114" class="Datatype">even</a> <a id="21654" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#21628" class="Bound">n</a>
    <a id="21660" class="Comment">-----------</a>
  <a id="21674" class="Symbol">→</a> <a id="21676" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#20134" class="Datatype">odd</a> <a id="21680" class="Symbol">(</a><a id="21681" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#21626" class="Bound">m</a> <a id="21683" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="21685" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#21628" class="Bound">n</a><a id="21686" class="Symbol">)</a>

<a id="21689" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#21538" class="Function">e+e≡e</a> <a id="21695" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#20169" class="InductiveConstructor">zero</a>     <a id="21704" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#21704" class="Bound">en</a>  <a id="21708" class="Symbol">=</a>  <a id="21711" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#21704" class="Bound">en</a>
<a id="21714" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#21538" class="Function">e+e≡e</a> <a id="21720" class="Symbol">(</a><a id="21721" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#20211" class="InductiveConstructor">suc</a> <a id="21725" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#21725" class="Bound">om</a><a id="21727" class="Symbol">)</a> <a id="21729" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#21729" class="Bound">en</a>  <a id="21733" class="Symbol">=</a>  <a id="21736" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#20211" class="InductiveConstructor">suc</a> <a id="21740" class="Symbol">(</a><a id="21741" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#21615" class="Function">o+e≡o</a> <a id="21747" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#21725" class="Bound">om</a> <a id="21750" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#21729" class="Bound">en</a><a id="21752" class="Symbol">)</a>

<a id="21755" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#21615" class="Function">o+e≡o</a> <a id="21761" class="Symbol">(</a><a id="21762" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#20297" class="InductiveConstructor">suc</a> <a id="21766" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#21766" class="Bound">em</a><a id="21768" class="Symbol">)</a> <a id="21770" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#21770" class="Bound">en</a>  <a id="21774" class="Symbol">=</a>  <a id="21777" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#20297" class="InductiveConstructor">suc</a> <a id="21781" class="Symbol">(</a><a id="21782" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#21538" class="Function">e+e≡e</a> <a id="21788" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#21766" class="Bound">em</a> <a id="21791" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#21770" class="Bound">en</a><a id="21793" class="Symbol">)</a>{% endraw %}</pre>
Corresponding to the mutually recursive types, we use two mutually recursive
functions, one to show that the sum of two even numbers is even, and the other
to show that the sum of an odd and an even number is odd.

This is our first use of mutually recursive functions.  Since each identifier
must be defined before it is used, we first give the signatures for both
functions and then the equations that define them.

To show that the sum of two even numbers is even, consider the
evidence that the first number is even. If it is because it is zero,
then the sum is even because the second number is even.  If it is
because it is the successor of an odd number, then the result is even
because it is the successor of the sum of an odd and an even number,
which is odd.

To show that the sum of an odd and even number is odd, consider the
evidence that the first number is odd. If it is because it is the
successor of an even number, then the result is odd because it is the
successor of the sum of two even numbers, which is even.

#### Exercise `o+o≡e` (stretch) {#odd-plus-odd}

Show that the sum of two odd numbers is even.

#### Exercise `Bin-predicates` (stretch) {#Bin-predicates}

Recall that 
Exercise [Bin]({{ site.baseurl }}{% link out/plfa/Naturals.md%}#Bin)
defines a datatype `Bin` of bitstrings representing natural numbers.
Representations are not unique due to leading zeros.
Hence, eleven may be represented by both of the following:

    x1 x1 x0 x1 nil
    x1 x1 x0 x1 x0 x0 nil

Define a predicate

    Can : Bin → Set

over all bitstrings that holds if the bitstring is canonical, meaning
it has no leading zeros; the first representation of eleven above is
canonical, and the second is not.  To define it, you will need an
auxiliary predicate

    One : Bin → Set

that holds only if the bistring has a leading one.  A bitstring is
canonical if it has a leading one (representing a positive number) or
if it consists of a single zero (representing zero).

Show that increment preserves canonical bitstrings:

    Can x
    ------------
    Can (inc x)

Show that converting a natural to a bitstring always yields a
canonical bitstring:

    ----------
    Can (to n)

Show that converting a canonical bitstring to a natural
and back is the identity:

    Can x
    ---------------
    to (from x) ≡ x

(Hint: For each of these, you may first need to prove related
properties of `One`.)

## Standard prelude

Definitions similar to those in this chapter can be found in the standard library:
<pre class="Agda">{% raw %}<a id="24297" class="Keyword">import</a> <a id="24304" href="https://agda.github.io/agda-stdlib/Data.Nat.html" class="Module">Data.Nat</a> <a id="24313" class="Keyword">using</a> <a id="24319" class="Symbol">(</a><a id="24320" href="https://agda.github.io/agda-stdlib/Data.Nat.Base.html#742" class="Datatype Operator">_≤_</a><a id="24323" class="Symbol">;</a> <a id="24325" href="https://agda.github.io/agda-stdlib/Data.Nat.Base.html#765" class="InductiveConstructor">z≤n</a><a id="24328" class="Symbol">;</a> <a id="24330" href="https://agda.github.io/agda-stdlib/Data.Nat.Base.html#807" class="InductiveConstructor">s≤s</a><a id="24333" class="Symbol">)</a>
<a id="24335" class="Keyword">import</a> <a id="24342" href="https://agda.github.io/agda-stdlib/Data.Nat.Properties.html" class="Module">Data.Nat.Properties</a> <a id="24362" class="Keyword">using</a> <a id="24368" class="Symbol">(</a><a id="24369" href="https://agda.github.io/agda-stdlib/Data.Nat.Properties.html#1804" class="Function">≤-refl</a><a id="24375" class="Symbol">;</a> <a id="24377" href="https://agda.github.io/agda-stdlib/Data.Nat.Properties.html#1997" class="Function">≤-trans</a><a id="24384" class="Symbol">;</a> <a id="24386" href="https://agda.github.io/agda-stdlib/Data.Nat.Properties.html#1854" class="Function">≤-antisym</a><a id="24395" class="Symbol">;</a> <a id="24397" href="https://agda.github.io/agda-stdlib/Data.Nat.Properties.html#2109" class="Function">≤-total</a><a id="24404" class="Symbol">;</a>
                                  <a id="24440" href="https://agda.github.io/agda-stdlib/Data.Nat.Properties.html#10952" class="Function">+-monoʳ-≤</a><a id="24449" class="Symbol">;</a> <a id="24451" href="https://agda.github.io/agda-stdlib/Data.Nat.Properties.html#10862" class="Function">+-monoˡ-≤</a><a id="24460" class="Symbol">;</a> <a id="24462" href="https://agda.github.io/agda-stdlib/Data.Nat.Properties.html#10706" class="Function">+-mono-≤</a><a id="24470" class="Symbol">)</a>{% endraw %}</pre>
In the standard library, `≤-total` is formalised in terms of
disjunction (which we define in
Chapter [Connectives]({{ site.baseurl }}{% link out/plfa/Connectives.md%})),
and `+-monoʳ-≤`, `+-monoˡ-≤`, `+-mono-≤` are proved differently than here,
and more arguments are implicit.


## Unicode

This chapter uses the following unicode:

    ≤  U+2264  LESS-THAN OR EQUAL TO (\<=, \le)
    ≥  U+2265  GREATER-THAN OR EQUAL TO (\>=, \ge)
    ˡ  U+02E1  MODIFIER LETTER SMALL L (\^l)
    ʳ  U+02B3  MODIFIER LETTER SMALL R (\^r)

The commands `\^l` and `\^r` give access to a variety of superscript
leftward and rightward arrows in addition to superscript letters `l` and `r`.
