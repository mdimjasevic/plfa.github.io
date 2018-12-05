---
src       : src/plfa/Induction.lagda
title     : "Induction: Proof by Induction"
layout    : page
prev      : /Naturals/
permalink : /Induction/
next      : /Relations/
---

<pre class="Agda">{% raw %}<a id="155" class="Keyword">module</a> <a id="162" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}" class="Module">plfa.Induction</a> <a id="177" class="Keyword">where</a>{% endraw %}</pre>

> Induction makes you feel guilty for getting something out of nothing
> ... but it is one of the greatest ideas of civilization.
> -- Herbert Wilf

Now that we've defined the naturals and operations upon them, our next
step is to learn how to prove properties that they satisfy.  As hinted
by their name, properties of _inductive datatypes_ are proved by
_induction_.


## Imports

We require equality as in the previous chapter, plus the naturals
and some operations upon them.  We also import a couple of new operations,
`cong`, `sym`, and `_≡⟨_⟩_`, which are explained below.
<pre class="Agda">{% raw %}<a id="788" class="Keyword">import</a> <a id="795" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html" class="Module">Relation.Binary.PropositionalEquality</a> <a id="833" class="Symbol">as</a> <a id="836" class="Module">Eq</a>
<a id="839" class="Keyword">open</a> <a id="844" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html" class="Module">Eq</a> <a id="847" class="Keyword">using</a> <a id="853" class="Symbol">(</a><a id="854" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Equality.html#83" class="Datatype Operator">_≡_</a><a id="857" class="Symbol">;</a> <a id="859" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Equality.html#140" class="InductiveConstructor">refl</a><a id="863" class="Symbol">;</a> <a id="865" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#1056" class="Function">cong</a><a id="869" class="Symbol">;</a> <a id="871" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.Core.html#838" class="Function">sym</a><a id="874" class="Symbol">)</a>
<a id="876" class="Keyword">open</a> <a id="881" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#3840" class="Module">Eq.≡-Reasoning</a> <a id="896" class="Keyword">using</a> <a id="902" class="Symbol">(</a><a id="903" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#3941" class="Function Operator">begin_</a><a id="909" class="Symbol">;</a> <a id="911" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#3999" class="Function Operator">_≡⟨⟩_</a><a id="916" class="Symbol">;</a> <a id="918" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#4058" class="Function Operator">_≡⟨_⟩_</a><a id="924" class="Symbol">;</a> <a id="926" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#4239" class="Function Operator">_∎</a><a id="928" class="Symbol">)</a>
<a id="930" class="Keyword">open</a> <a id="935" class="Keyword">import</a> <a id="942" href="https://agda.github.io/agda-stdlib/Data.Nat.html" class="Module">Data.Nat</a> <a id="951" class="Keyword">using</a> <a id="957" class="Symbol">(</a><a id="958" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a><a id="959" class="Symbol">;</a> <a id="961" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#115" class="InductiveConstructor">zero</a><a id="965" class="Symbol">;</a> <a id="967" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a><a id="970" class="Symbol">;</a> <a id="972" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">_+_</a><a id="975" class="Symbol">;</a> <a id="977" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#433" class="Primitive Operator">_*_</a><a id="980" class="Symbol">;</a> <a id="982" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#320" class="Primitive Operator">_∸_</a><a id="985" class="Symbol">)</a>{% endraw %}</pre>


## Properties of operators

Operators pop up all the time, and mathematicians have agreed
on names for some of the most common properties.

* _Identity_.   Operator `+` has left identity `0` if `0 + n ≡ n`, and
  right identity `0` if `n + 0 ≡ n`, for all `n`. A value that is both
  a left and right identity is just called an identity. Identity is also
  sometimes called _unit_.

* _Associativity_.   Operator `+` is associative if the location
  of parentheses does not matter: `(m + n) + p ≡ m + (n + p)`,
  for all `m`, `n`, and `p`.

* _Commutativity_.   Operator `+` is commutative if order of
  arguments does not matter: `m + n ≡ n + m`, for all `m` and `n`.

* _Distributivity_.   Operator `*` distributes over operator `+` from the
  left if `(m + n) * p ≡ (m * p) + (n * p)`, for all `m`, `n`, and `p`,
  and from the right if `m * (p + q) ≡ (m * p) + (m * q)`, for all `m`,
  `p`, and `q`.

Addition has identity `0` and multiplication has identity `1`;
addition and multiplication are both associative and commutative;
and multiplication distributes over addition.

If you ever bump into an operator at a party, you now know how
to make small talk, by asking whether it has a unit and is
associative or commutative.  If you bump into two operators, you
might ask them if one distributes over the other.

Less frivolously, if you ever bump into an operator while reading a
technical paper, this gives you a way to orient yourself, by checking
whether or not it has an identity, is associative or commutative, or
distributes over another operator.  A careful author will often call
out these properties---or their lack---for instance by pointing out
that a newly introduced operator is associative but not commutative.

#### Exercise `operators` {#operators}

Give another example of a pair of operators that have an identity
and are associative, commutative, and distribute over one another.

Give an example of an operator that has an identity and is
associative but is not commutative.


## Associativity

One property of addition is that it is _associative_, that is, that the
location of the parentheses does not matter:

    (m + n) + p ≡ m + (n + p)

Here `m`, `n`, and `p` are variables that range over all natural numbers.

We can test the proposition by choosing specific numbers for the three
variables.
<pre class="Agda">{% raw %}<a id="3341" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#3341" class="Function">_</a> <a id="3343" class="Symbol">:</a> <a id="3345" class="Symbol">(</a><a id="3346" class="Number">3</a> <a id="3348" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="3350" class="Number">4</a><a id="3351" class="Symbol">)</a> <a id="3353" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="3355" class="Number">5</a> <a id="3357" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Equality.html#83" class="Datatype Operator">≡</a> <a id="3359" class="Number">3</a> <a id="3361" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="3363" class="Symbol">(</a><a id="3364" class="Number">4</a> <a id="3366" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="3368" class="Number">5</a><a id="3369" class="Symbol">)</a>
<a id="3371" class="Symbol">_</a> <a id="3373" class="Symbol">=</a>
  <a id="3377" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#3941" class="Function Operator">begin</a>
    <a id="3387" class="Symbol">(</a><a id="3388" class="Number">3</a> <a id="3390" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="3392" class="Number">4</a><a id="3393" class="Symbol">)</a> <a id="3395" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="3397" class="Number">5</a>
  <a id="3401" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#3999" class="Function Operator">≡⟨⟩</a>
    <a id="3409" class="Number">7</a> <a id="3411" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="3413" class="Number">5</a>
  <a id="3417" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#3999" class="Function Operator">≡⟨⟩</a>
    <a id="3425" class="Number">12</a>
  <a id="3430" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#3999" class="Function Operator">≡⟨⟩</a>
    <a id="3438" class="Number">3</a> <a id="3440" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="3442" class="Number">9</a>
  <a id="3446" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#3999" class="Function Operator">≡⟨⟩</a>
    <a id="3454" class="Number">3</a> <a id="3456" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="3458" class="Symbol">(</a><a id="3459" class="Number">4</a> <a id="3461" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="3463" class="Number">5</a><a id="3464" class="Symbol">)</a>
  <a id="3468" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#4239" class="Function Operator">∎</a>{% endraw %}</pre>
Here we have displayed the computation as a chain of equations,
one term to a line.  It is often easiest to read such chains from the top down
until one reaches the simplest term (in this case, `12`), and
then from the bottom up until one reaches the same term.

The test reveals that associativity is perhaps not as obvious as first
it appears.  Why should `7 + 5` be the same as `3 + 9`?  We might want
to gather more evidence, testing the proposition by choosing other
numbers.  But---since there are an infinite number of
naturals---testing can never be complete.  Is there any way we can be
sure that associativity holds for _all_ the natural numbers?

The answer is yes! We can prove a property holds for all naturals using
_proof by induction_.


## Proof by induction

Recall the definition of natural numbers consists of a _base case_
which tells us that `zero` is a natural, and an _inductive case_
which tells us that if `m` is a natural then `suc m` is also a natural.

Proof by induction follows the structure of this definition.  To prove
a property of natural numbers by induction, we need to prove two cases.
First is the _base case_, where we show the property holds for `zero`.
Second is the _inductive case_, where we assume the property holds for
an arbitrary natural `m` (we call this the _inductive hypothesis_), and
then show that the property must also hold for `suc m`.

If we write `P m` for a property of `m`, then what we need to
demonstrate are the following two inference rules:

    ------
    P zero

    P m
    ---------
    P (suc m)

Let's unpack these rules.  The first rule is the base case, and
requires we show that property `P` holds for `zero`.  The second rule
is the inductive case, and requires we show that if we assume the
inductive hypothesis---namely that `P` holds for `m`---then it follows that
`P` also holds for `suc m`.

Why does this work?  Again, it can be explained by a creation story.
To start with, we know no properties.

    -- in the beginning, no properties are known

Now, we apply the two rules to all the properties we know about.  The
base case tells us that `P zero` holds, so we add it to the set of
known properties.  The inductive case tells us that if `P m` holds (on
the day before today) then `P (suc m)` also holds (today).  We didn't
know about any properties before today, so the inductive case doesn't
apply.

    -- on the first day, one property is known
    P zero

Then we repeat the process, so on the next day we know about all the
properties from the day before, plus any properties added by the rules.
The base case tells us that `P zero` holds, but we already
knew that. But now the inductive case tells us that since `P zero`
held yesterday, then `P (suc zero)` holds today.

    -- on the second day, two properties are known
    P zero
    P (suc zero)

And we repeat the process again. Now the inductive case
tells us that since `P zero` and `P (suc zero)` both hold, then
`P (suc zero)` and `P (suc (suc zero))` also hold. We already knew about
the first of these, but the second is new.

    -- on the third day, three properties are known
    P zero
    P (suc zero)
    P (suc (suc zero))

You've got the hang of it by now.

    -- on the fourth day, four properties are known
    P zero
    P (suc zero)
    P (suc (suc zero))
    P (suc (suc (suc zero)))

The process continues.  On the _n_'th day there will be _n_ distinct
properties that hold.  The property of every natural number will appear
on some given day.  In particular, the property `P n` first appears on
day _n+1_.


## Our first proof: associativity

To prove associativity, we take `P m` to be the property

    (m + n) + p ≡ m + (n + p)

Here `n` and `p` are arbitrary natural numbers, so if we can show the
equation holds for all `m` it will also hold for all `n` and `p`.
The appropriate instances of the inference rules are:

    -------------------------------
    (zero + n) + p ≡ zero + (n + p)

    (m + n) + p ≡ m + (n + p)
    ---------------------------------
    (suc m + n) + p ≡ suc m + (n + p)

If we can demonstrate both of these, then associativity of addition
follows by induction.

Here is the proposition's statement and proof.
<pre class="Agda">{% raw %}<a id="+-assoc"></a><a id="7706" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7706" class="Function">+-assoc</a> <a id="7714" class="Symbol">:</a> <a id="7716" class="Symbol">∀</a> <a id="7718" class="Symbol">(</a><a id="7719" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7719" class="Bound">m</a> <a id="7721" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7721" class="Bound">n</a> <a id="7723" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7723" class="Bound">p</a> <a id="7725" class="Symbol">:</a> <a id="7727" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a><a id="7728" class="Symbol">)</a> <a id="7730" class="Symbol">→</a> <a id="7732" class="Symbol">(</a><a id="7733" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7719" class="Bound">m</a> <a id="7735" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="7737" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7721" class="Bound">n</a><a id="7738" class="Symbol">)</a> <a id="7740" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="7742" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7723" class="Bound">p</a> <a id="7744" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Equality.html#83" class="Datatype Operator">≡</a> <a id="7746" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7719" class="Bound">m</a> <a id="7748" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="7750" class="Symbol">(</a><a id="7751" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7721" class="Bound">n</a> <a id="7753" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="7755" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7723" class="Bound">p</a><a id="7756" class="Symbol">)</a>
<a id="7758" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7706" class="Function">+-assoc</a> <a id="7766" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#115" class="InductiveConstructor">zero</a> <a id="7771" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7771" class="Bound">n</a> <a id="7773" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7773" class="Bound">p</a> <a id="7775" class="Symbol">=</a>
  <a id="7779" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#3941" class="Function Operator">begin</a>
    <a id="7789" class="Symbol">(</a><a id="7790" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#115" class="InductiveConstructor">zero</a> <a id="7795" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="7797" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7771" class="Bound">n</a><a id="7798" class="Symbol">)</a> <a id="7800" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="7802" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7773" class="Bound">p</a>
  <a id="7806" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#3999" class="Function Operator">≡⟨⟩</a>
    <a id="7814" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7771" class="Bound">n</a> <a id="7816" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="7818" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7773" class="Bound">p</a>
  <a id="7822" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#3999" class="Function Operator">≡⟨⟩</a>
   <a id="7829" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#115" class="InductiveConstructor">zero</a> <a id="7834" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="7836" class="Symbol">(</a><a id="7837" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7771" class="Bound">n</a> <a id="7839" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="7841" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7773" class="Bound">p</a><a id="7842" class="Symbol">)</a>
  <a id="7846" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#4239" class="Function Operator">∎</a>
<a id="7848" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7706" class="Function">+-assoc</a> <a id="7856" class="Symbol">(</a><a id="7857" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="7861" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7861" class="Bound">m</a><a id="7862" class="Symbol">)</a> <a id="7864" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7864" class="Bound">n</a> <a id="7866" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7866" class="Bound">p</a> <a id="7868" class="Symbol">=</a>
  <a id="7872" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#3941" class="Function Operator">begin</a>
    <a id="7882" class="Symbol">(</a><a id="7883" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="7887" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7861" class="Bound">m</a> <a id="7889" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="7891" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7864" class="Bound">n</a><a id="7892" class="Symbol">)</a> <a id="7894" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="7896" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7866" class="Bound">p</a>
  <a id="7900" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#3999" class="Function Operator">≡⟨⟩</a>
    <a id="7908" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="7912" class="Symbol">(</a><a id="7913" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7861" class="Bound">m</a> <a id="7915" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="7917" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7864" class="Bound">n</a><a id="7918" class="Symbol">)</a> <a id="7920" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="7922" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7866" class="Bound">p</a>
  <a id="7926" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#3999" class="Function Operator">≡⟨⟩</a>
    <a id="7934" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="7938" class="Symbol">((</a><a id="7940" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7861" class="Bound">m</a> <a id="7942" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="7944" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7864" class="Bound">n</a><a id="7945" class="Symbol">)</a> <a id="7947" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="7949" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7866" class="Bound">p</a><a id="7950" class="Symbol">)</a>
  <a id="7954" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#4058" class="Function Operator">≡⟨</a> <a id="7957" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#1056" class="Function">cong</a> <a id="7962" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="7966" class="Symbol">(</a><a id="7967" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7706" class="Function">+-assoc</a> <a id="7975" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7861" class="Bound">m</a> <a id="7977" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7864" class="Bound">n</a> <a id="7979" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7866" class="Bound">p</a><a id="7980" class="Symbol">)</a> <a id="7982" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#4058" class="Function Operator">⟩</a>
    <a id="7988" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="7992" class="Symbol">(</a><a id="7993" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7861" class="Bound">m</a> <a id="7995" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="7997" class="Symbol">(</a><a id="7998" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7864" class="Bound">n</a> <a id="8000" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="8002" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7866" class="Bound">p</a><a id="8003" class="Symbol">))</a>
  <a id="8008" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#3999" class="Function Operator">≡⟨⟩</a>
    <a id="8016" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="8020" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7861" class="Bound">m</a> <a id="8022" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="8024" class="Symbol">(</a><a id="8025" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7864" class="Bound">n</a> <a id="8027" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="8029" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7866" class="Bound">p</a><a id="8030" class="Symbol">)</a>
  <a id="8034" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#4239" class="Function Operator">∎</a>{% endraw %}</pre>
We have named the proof `+-assoc`.  In Agda, identifiers can consist of
any sequence of characters not including spaces or the characters `@.(){};_`.

Let's unpack this code.  The signature states that we are
defining the identifier `+-assoc` which provides evidence for the
proposition:

    ∀ (m n p : ℕ) → (m + n) + p ≡ m + (n + p)

The upside down A is pronounced "for all", and the proposition
asserts that for all natural numbers `m`, `n`, and `p`
the equation `(m + n) + p ≡ m + (n + p)` holds.  Evidence for the proposition
is a function that accepts three natural numbers, binds them to `m`, `n`, and `p`,
and returns evidence for the corresponding instance of the equation.

For the base case, we must show:

    (zero + n) + p ≡ zero + (n + p)

Simplifying both sides with the base case of addition yields the equation:

    n + p ≡ n + p

This holds trivially.  Reading the chain of equations in the base case of the proof,
the top and bottom of the chain match the two sides of the equation to
be shown, and reading down from the top and up from the bottom takes us to
`n + p` in the middle.  No justification other than simplification is required.

For the inductive case, we must show:

    (suc m + n) + p ≡ suc m + (n + p)

Simplifying both sides with the inductive case of addition yields the equation:

    suc ((m + n) + p) ≡ suc (m + (n + p))

This in turn follows by prefacing `suc` to both sides of the induction
hypothesis:

    (m + n) + p ≡ m + (n + p)

Reading the chain of equations in the inductive case of the proof, the
top and bottom of the chain match the two sides of the equation to be
shown, and reading down from the top and up from the bottom takes us
to the simplified equation above. The remaining equation, does not follow
from simplification alone, so we use an additional operator for chain
reasoning, `_≡⟨_⟩_`, where a justification for the equation appears
within angle brackets.  The justification given is:

    ⟨ cong suc (+-assoc m n p) ⟩

Here, the recursive invocation `+-assoc m n p` has as its type the
induction hypothesis, and `cong suc` prefaces `suc` to each side to
yield the needed equation.

A relation is said to be a _congruence_ for a given function if it is
preserved by applying that function.  If `e` is evidence that `x ≡ y`,
then `cong f e` is evidence that `f x ≡ f y`, for any function `f`.

Here the inductive hypothesis is not assumed, but instead proved by a
recursive invocation of the function we are defining, `+-assoc m n p`.
As with addition, this is well founded because associativity of
larger numbers is proved in terms of associativity of smaller numbers.
In this case, `assoc (suc m) n p` is proved using `assoc m n p`.
The correspondence between proof by induction and definition by
recursion is one of the most appealing aspects of Agda.


## Our second proof: commutativity

Another important property of addition is that it is _commutative_, that is,
that the order of the operands does not matter:

    m + n ≡ n + m

The proof requires that we first demonstrate two lemmas.

### The first lemma

The base case of the definition of addition states that zero
is a left-identity:

    zero + n ≡ n

Our first lemma states that zero is also a right-identity:

    m + zero ≡ m

Here is the lemma's statement and proof.
<pre class="Agda">{% raw %}<a id="+-identityʳ"></a><a id="11364" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#11364" class="Function">+-identityʳ</a> <a id="11376" class="Symbol">:</a> <a id="11378" class="Symbol">∀</a> <a id="11380" class="Symbol">(</a><a id="11381" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#11381" class="Bound">m</a> <a id="11383" class="Symbol">:</a> <a id="11385" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a><a id="11386" class="Symbol">)</a> <a id="11388" class="Symbol">→</a> <a id="11390" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#11381" class="Bound">m</a> <a id="11392" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="11394" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#115" class="InductiveConstructor">zero</a> <a id="11399" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Equality.html#83" class="Datatype Operator">≡</a> <a id="11401" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#11381" class="Bound">m</a>
<a id="11403" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#11364" class="Function">+-identityʳ</a> <a id="11415" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#115" class="InductiveConstructor">zero</a> <a id="11420" class="Symbol">=</a>
  <a id="11424" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#3941" class="Function Operator">begin</a>
    <a id="11434" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#115" class="InductiveConstructor">zero</a> <a id="11439" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="11441" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#115" class="InductiveConstructor">zero</a>
  <a id="11448" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#3999" class="Function Operator">≡⟨⟩</a>
    <a id="11456" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#115" class="InductiveConstructor">zero</a>
  <a id="11463" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#4239" class="Function Operator">∎</a>
<a id="11465" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#11364" class="Function">+-identityʳ</a> <a id="11477" class="Symbol">(</a><a id="11478" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="11482" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#11482" class="Bound">m</a><a id="11483" class="Symbol">)</a> <a id="11485" class="Symbol">=</a>
  <a id="11489" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#3941" class="Function Operator">begin</a>
    <a id="11499" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="11503" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#11482" class="Bound">m</a> <a id="11505" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="11507" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#115" class="InductiveConstructor">zero</a>
  <a id="11514" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#3999" class="Function Operator">≡⟨⟩</a>
    <a id="11522" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="11526" class="Symbol">(</a><a id="11527" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#11482" class="Bound">m</a> <a id="11529" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="11531" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#115" class="InductiveConstructor">zero</a><a id="11535" class="Symbol">)</a>
  <a id="11539" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#4058" class="Function Operator">≡⟨</a> <a id="11542" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#1056" class="Function">cong</a> <a id="11547" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="11551" class="Symbol">(</a><a id="11552" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#11364" class="Function">+-identityʳ</a> <a id="11564" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#11482" class="Bound">m</a><a id="11565" class="Symbol">)</a> <a id="11567" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#4058" class="Function Operator">⟩</a>
    <a id="11573" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="11577" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#11482" class="Bound">m</a>
  <a id="11581" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#4239" class="Function Operator">∎</a>{% endraw %}</pre>
The signature states that we are defining the identifier `+-identityʳ` which
provides evidence for the proposition:

    ∀ (m : ℕ) → m + zero ≡ m

Evidence for the proposition is a function that accepts a natural
number, binds it to `m`, and returns evidence for the corresponding
instance of the equation.  The proof is by induction on `m`.

For the base case, we must show:

    zero + zero ≡ zero

Simplifying with the base case of addition, this is straightforward.

For the inductive case, we must show:

    (suc m) + zero = suc m

Simplifying both sides with the inductive case of addition yields the equation:

    suc (m + zero) = suc m

This in turn follows by prefacing `suc` to both sides of the induction
hypothesis:

    m + zero ≡ m

Reading the chain of equations down from the top and up from the bottom
takes us to the simplified equation above.  The remaining
equation has the justification:

    ⟨ cong suc (+-identityʳ m) ⟩

Here, the recursive invocation `+-identityʳ m` has as its type the
induction hypothesis, and `cong suc` prefaces `suc` to each side to
yield the needed equation.  This completes the first lemma.

### The second lemma

The inductive case of the definition of addition pushes `suc` on the
first argument to the outside:

    suc m + n ≡ suc (m + n)

Our second lemma does the same for `suc` on the second argument:

    m + suc n ≡ suc (m + n)

Here is the lemma's statement and proof.
<pre class="Agda">{% raw %}<a id="+-suc"></a><a id="13037" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#13037" class="Function">+-suc</a> <a id="13043" class="Symbol">:</a> <a id="13045" class="Symbol">∀</a> <a id="13047" class="Symbol">(</a><a id="13048" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#13048" class="Bound">m</a> <a id="13050" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#13050" class="Bound">n</a> <a id="13052" class="Symbol">:</a> <a id="13054" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a><a id="13055" class="Symbol">)</a> <a id="13057" class="Symbol">→</a> <a id="13059" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#13048" class="Bound">m</a> <a id="13061" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="13063" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="13067" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#13050" class="Bound">n</a> <a id="13069" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Equality.html#83" class="Datatype Operator">≡</a> <a id="13071" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="13075" class="Symbol">(</a><a id="13076" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#13048" class="Bound">m</a> <a id="13078" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="13080" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#13050" class="Bound">n</a><a id="13081" class="Symbol">)</a>
<a id="13083" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#13037" class="Function">+-suc</a> <a id="13089" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#115" class="InductiveConstructor">zero</a> <a id="13094" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#13094" class="Bound">n</a> <a id="13096" class="Symbol">=</a>
  <a id="13100" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#3941" class="Function Operator">begin</a>
    <a id="13110" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#115" class="InductiveConstructor">zero</a> <a id="13115" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="13117" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="13121" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#13094" class="Bound">n</a>
  <a id="13125" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#3999" class="Function Operator">≡⟨⟩</a>
    <a id="13133" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="13137" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#13094" class="Bound">n</a>
  <a id="13141" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#3999" class="Function Operator">≡⟨⟩</a>
    <a id="13149" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="13153" class="Symbol">(</a><a id="13154" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#115" class="InductiveConstructor">zero</a> <a id="13159" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="13161" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#13094" class="Bound">n</a><a id="13162" class="Symbol">)</a>
  <a id="13166" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#4239" class="Function Operator">∎</a>
<a id="13168" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#13037" class="Function">+-suc</a> <a id="13174" class="Symbol">(</a><a id="13175" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="13179" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#13179" class="Bound">m</a><a id="13180" class="Symbol">)</a> <a id="13182" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#13182" class="Bound">n</a> <a id="13184" class="Symbol">=</a>
  <a id="13188" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#3941" class="Function Operator">begin</a>
    <a id="13198" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="13202" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#13179" class="Bound">m</a> <a id="13204" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="13206" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="13210" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#13182" class="Bound">n</a>
  <a id="13214" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#3999" class="Function Operator">≡⟨⟩</a>
    <a id="13222" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="13226" class="Symbol">(</a><a id="13227" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#13179" class="Bound">m</a> <a id="13229" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="13231" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="13235" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#13182" class="Bound">n</a><a id="13236" class="Symbol">)</a>
  <a id="13240" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#4058" class="Function Operator">≡⟨</a> <a id="13243" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#1056" class="Function">cong</a> <a id="13248" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="13252" class="Symbol">(</a><a id="13253" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#13037" class="Function">+-suc</a> <a id="13259" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#13179" class="Bound">m</a> <a id="13261" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#13182" class="Bound">n</a><a id="13262" class="Symbol">)</a> <a id="13264" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#4058" class="Function Operator">⟩</a>
    <a id="13270" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="13274" class="Symbol">(</a><a id="13275" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="13279" class="Symbol">(</a><a id="13280" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#13179" class="Bound">m</a> <a id="13282" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="13284" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#13182" class="Bound">n</a><a id="13285" class="Symbol">))</a>
  <a id="13290" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#3999" class="Function Operator">≡⟨⟩</a>
    <a id="13298" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="13302" class="Symbol">(</a><a id="13303" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="13307" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#13179" class="Bound">m</a> <a id="13309" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="13311" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#13182" class="Bound">n</a><a id="13312" class="Symbol">)</a>
  <a id="13316" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#4239" class="Function Operator">∎</a>{% endraw %}</pre>
The signature states that we are defining the identifier `+-suc` which provides
evidence for the proposition:

    ∀ (m n : ℕ) → m + suc n ≡ suc (m + n)

Evidence for the proposition is a function that accepts two natural numbers,
binds them to `m` and `n`, and returns evidence for the corresponding instance
of the equation.  The proof is by induction on `m`.

For the base case, we must show:

    zero + suc n ≡ suc (zero + n)

Simplifying with the base case of addition, this is straightforward.

For the inductive case, we must show:

    suc m + suc n ≡ suc (suc m + n)

Simplifying both sides with the inductive case of addition yields the equation:

    suc (m + suc n) ≡ suc (suc (m + n))

This in turn follows by prefacing `suc` to both sides of the induction
hypothesis:

    m + suc n ≡ suc (m + n)

Reading the chain of equations down from the top and up from the bottom
takes us to the simplified equation in the middle.  The remaining
equation has the justification:

    ⟨ cong suc (+-suc m n) ⟩

Here, the recursive invocation `+-suc m n` has as its type the
induction hypothesis, and `cong suc` prefaces `suc` to each side to
yield the needed equation.  This completes the second lemma.

### The proposition

Finally, here is our proposition's statement and proof.
<pre class="Agda">{% raw %}<a id="+-comm"></a><a id="14626" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#14626" class="Function">+-comm</a> <a id="14633" class="Symbol">:</a> <a id="14635" class="Symbol">∀</a> <a id="14637" class="Symbol">(</a><a id="14638" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#14638" class="Bound">m</a> <a id="14640" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#14640" class="Bound">n</a> <a id="14642" class="Symbol">:</a> <a id="14644" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a><a id="14645" class="Symbol">)</a> <a id="14647" class="Symbol">→</a> <a id="14649" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#14638" class="Bound">m</a> <a id="14651" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="14653" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#14640" class="Bound">n</a> <a id="14655" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Equality.html#83" class="Datatype Operator">≡</a> <a id="14657" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#14640" class="Bound">n</a> <a id="14659" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="14661" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#14638" class="Bound">m</a>
<a id="14663" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#14626" class="Function">+-comm</a> <a id="14670" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#14670" class="Bound">m</a> <a id="14672" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#115" class="InductiveConstructor">zero</a> <a id="14677" class="Symbol">=</a>
  <a id="14681" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#3941" class="Function Operator">begin</a>
    <a id="14691" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#14670" class="Bound">m</a> <a id="14693" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="14695" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#115" class="InductiveConstructor">zero</a>
  <a id="14702" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#4058" class="Function Operator">≡⟨</a> <a id="14705" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#11364" class="Function">+-identityʳ</a> <a id="14717" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#14670" class="Bound">m</a> <a id="14719" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#4058" class="Function Operator">⟩</a>
    <a id="14725" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#14670" class="Bound">m</a>
  <a id="14729" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#3999" class="Function Operator">≡⟨⟩</a>
    <a id="14737" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#115" class="InductiveConstructor">zero</a> <a id="14742" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="14744" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#14670" class="Bound">m</a>
  <a id="14748" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#4239" class="Function Operator">∎</a>
<a id="14750" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#14626" class="Function">+-comm</a> <a id="14757" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#14757" class="Bound">m</a> <a id="14759" class="Symbol">(</a><a id="14760" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="14764" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#14764" class="Bound">n</a><a id="14765" class="Symbol">)</a> <a id="14767" class="Symbol">=</a>
  <a id="14771" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#3941" class="Function Operator">begin</a>
    <a id="14781" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#14757" class="Bound">m</a> <a id="14783" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="14785" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="14789" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#14764" class="Bound">n</a>
  <a id="14793" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#4058" class="Function Operator">≡⟨</a> <a id="14796" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#13037" class="Function">+-suc</a> <a id="14802" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#14757" class="Bound">m</a> <a id="14804" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#14764" class="Bound">n</a> <a id="14806" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#4058" class="Function Operator">⟩</a>
    <a id="14812" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="14816" class="Symbol">(</a><a id="14817" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#14757" class="Bound">m</a> <a id="14819" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="14821" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#14764" class="Bound">n</a><a id="14822" class="Symbol">)</a>
  <a id="14826" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#4058" class="Function Operator">≡⟨</a> <a id="14829" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#1056" class="Function">cong</a> <a id="14834" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="14838" class="Symbol">(</a><a id="14839" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#14626" class="Function">+-comm</a> <a id="14846" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#14757" class="Bound">m</a> <a id="14848" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#14764" class="Bound">n</a><a id="14849" class="Symbol">)</a> <a id="14851" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#4058" class="Function Operator">⟩</a>
    <a id="14857" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="14861" class="Symbol">(</a><a id="14862" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#14764" class="Bound">n</a> <a id="14864" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="14866" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#14757" class="Bound">m</a><a id="14867" class="Symbol">)</a>
  <a id="14871" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#3999" class="Function Operator">≡⟨⟩</a>
    <a id="14879" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="14883" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#14764" class="Bound">n</a> <a id="14885" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="14887" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#14757" class="Bound">m</a>
  <a id="14891" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#4239" class="Function Operator">∎</a>{% endraw %}</pre>
The first line states that we are defining the identifier
`+-comm` which provides evidence for the proposition:

    ∀ (m n : ℕ) → m + n ≡ n + m

Evidence for the proposition is a function that accepts two
natural numbers, binds them to `m` and `n`, and returns evidence for the
corresponding instance of the equation.  The proof is by
induction on `n`.  (Not on `m` this time!)

For the base case, we must show:

    m + zero ≡ zero + m

Simplifying both sides with the base case of addition yields the equation:

    m + zero ≡ m

The remaining equation has the justification `⟨ +-identityʳ m ⟩`,
which invokes the first lemma.

For the inductive case, we must show:

    m + suc n ≡ suc n + m

Simplifying both sides with the inductive case of addition yields the equation:

    m + suc n ≡ suc (n + m)

We show this in two steps.  First, we have:

    m + suc n ≡ suc (m + n)

which is justified by the second lemma, `⟨ +-suc m n ⟩`.  Then we
have

    suc (m + n) ≡ suc (n + m)

which is justified by congruence and the induction hypothesis,
`⟨ cong suc (+-comm m n) ⟩`.  This completes the proof.

Agda requires that identifiers are defined before they are used,
so we must present the lemmas before the main proposition, as we
have done above.  In practice, one will often attempt to prove
the main proposition first, and the equations required to do so
will suggest what lemmas to prove.


## Our first corollary: rearranging {#sections}

We can apply associativity to rearrange parentheses however we like.
Here is an example.
<pre class="Agda">{% raw %}<a id="+-rearrange"></a><a id="16453" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16453" class="Function">+-rearrange</a> <a id="16465" class="Symbol">:</a> <a id="16467" class="Symbol">∀</a> <a id="16469" class="Symbol">(</a><a id="16470" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16470" class="Bound">m</a> <a id="16472" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16472" class="Bound">n</a> <a id="16474" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16474" class="Bound">p</a> <a id="16476" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16476" class="Bound">q</a> <a id="16478" class="Symbol">:</a> <a id="16480" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a><a id="16481" class="Symbol">)</a> <a id="16483" class="Symbol">→</a> <a id="16485" class="Symbol">(</a><a id="16486" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16470" class="Bound">m</a> <a id="16488" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="16490" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16472" class="Bound">n</a><a id="16491" class="Symbol">)</a> <a id="16493" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="16495" class="Symbol">(</a><a id="16496" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16474" class="Bound">p</a> <a id="16498" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="16500" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16476" class="Bound">q</a><a id="16501" class="Symbol">)</a> <a id="16503" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Equality.html#83" class="Datatype Operator">≡</a> <a id="16505" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16470" class="Bound">m</a> <a id="16507" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="16509" class="Symbol">(</a><a id="16510" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16472" class="Bound">n</a> <a id="16512" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="16514" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16474" class="Bound">p</a><a id="16515" class="Symbol">)</a> <a id="16517" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="16519" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16476" class="Bound">q</a>
<a id="16521" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16453" class="Function">+-rearrange</a> <a id="16533" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16533" class="Bound">m</a> <a id="16535" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16535" class="Bound">n</a> <a id="16537" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16537" class="Bound">p</a> <a id="16539" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16539" class="Bound">q</a> <a id="16541" class="Symbol">=</a>
  <a id="16545" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#3941" class="Function Operator">begin</a>
    <a id="16555" class="Symbol">(</a><a id="16556" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16533" class="Bound">m</a> <a id="16558" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="16560" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16535" class="Bound">n</a><a id="16561" class="Symbol">)</a> <a id="16563" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="16565" class="Symbol">(</a><a id="16566" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16537" class="Bound">p</a> <a id="16568" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="16570" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16539" class="Bound">q</a><a id="16571" class="Symbol">)</a>
  <a id="16575" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#4058" class="Function Operator">≡⟨</a> <a id="16578" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7706" class="Function">+-assoc</a> <a id="16586" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16533" class="Bound">m</a> <a id="16588" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16535" class="Bound">n</a> <a id="16590" class="Symbol">(</a><a id="16591" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16537" class="Bound">p</a> <a id="16593" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="16595" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16539" class="Bound">q</a><a id="16596" class="Symbol">)</a> <a id="16598" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#4058" class="Function Operator">⟩</a>
    <a id="16604" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16533" class="Bound">m</a> <a id="16606" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="16608" class="Symbol">(</a><a id="16609" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16535" class="Bound">n</a> <a id="16611" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="16613" class="Symbol">(</a><a id="16614" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16537" class="Bound">p</a> <a id="16616" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="16618" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16539" class="Bound">q</a><a id="16619" class="Symbol">))</a>
  <a id="16624" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#4058" class="Function Operator">≡⟨</a> <a id="16627" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#1056" class="Function">cong</a> <a id="16632" class="Symbol">(</a><a id="16633" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16533" class="Bound">m</a> <a id="16635" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+_</a><a id="16637" class="Symbol">)</a> <a id="16639" class="Symbol">(</a><a id="16640" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.Core.html#838" class="Function">sym</a> <a id="16644" class="Symbol">(</a><a id="16645" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7706" class="Function">+-assoc</a> <a id="16653" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16535" class="Bound">n</a> <a id="16655" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16537" class="Bound">p</a> <a id="16657" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16539" class="Bound">q</a><a id="16658" class="Symbol">))</a> <a id="16661" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#4058" class="Function Operator">⟩</a>
    <a id="16667" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16533" class="Bound">m</a> <a id="16669" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="16671" class="Symbol">((</a><a id="16673" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16535" class="Bound">n</a> <a id="16675" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="16677" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16537" class="Bound">p</a><a id="16678" class="Symbol">)</a> <a id="16680" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="16682" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16539" class="Bound">q</a><a id="16683" class="Symbol">)</a>
  <a id="16687" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#4058" class="Function Operator">≡⟨</a> <a id="16690" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.Core.html#838" class="Function">sym</a> <a id="16694" class="Symbol">(</a><a id="16695" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7706" class="Function">+-assoc</a> <a id="16703" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16533" class="Bound">m</a> <a id="16705" class="Symbol">(</a><a id="16706" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16535" class="Bound">n</a> <a id="16708" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="16710" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16537" class="Bound">p</a><a id="16711" class="Symbol">)</a> <a id="16713" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16539" class="Bound">q</a><a id="16714" class="Symbol">)</a> <a id="16716" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#4058" class="Function Operator">⟩</a>
    <a id="16722" class="Symbol">(</a><a id="16723" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16533" class="Bound">m</a> <a id="16725" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="16727" class="Symbol">(</a><a id="16728" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16535" class="Bound">n</a> <a id="16730" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="16732" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16537" class="Bound">p</a><a id="16733" class="Symbol">))</a> <a id="16736" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="16738" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16539" class="Bound">q</a>
  <a id="16742" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#4239" class="Function Operator">∎</a>{% endraw %}</pre>
No induction is required, we simply apply associativity twice.
A few points are worthy of note.

First, addition associates to the left, so `m + (n + p) + q`
stands for `(m + (n + p)) + q`.

Second, we use `sym` to interchange the sides of an equation.
Proposition `+-assoc n p q` shifts parentheses from right to left:

    (n + p) + q ≡ n + (p + q)

To shift them the other way, we use `sym (+-assoc m n p)`:

    n + (p + q) ≡ (n + p) + q

In general, if `e` provides evidence for `x ≡ y` then `sym e` provides
evidence for `y ≡ x`.

Third, Agda supports a variant of the _section_ notation introduced by
Richard Bird.  We write `(x +_)` for the function that applied to `y`
returns `x + y`.  Thus, applying the congruence `cong (m +_)` takes
the above equation into

    m + (n + (p + q)) ≡ m + ((n + p) + q)

Similarly, we write `(_+ x)` for the function that applied to `y`
returns `y + x`; the same works for any infix operator.



## Creation, one last time

Returning to the proof of associativity, it may be helpful to view the inductive
proof (or, equivalently, the recursive definition) as a creation story.  This
time we are concerned with judgments asserting associativity.

     -- in the beginning, we know nothing about associativity

Now, we apply the rules to all the judgments we know about.  The base
case tells us that `(zero + n) + p ≡ zero + (n + p)` for every natural
`n` and `p`.  The inductive case tells us that if `(m + n) + p ≡ m +
(n + p)` (on the day before today) then
`(suc m + n) + p ≡ suc m + (n + p)` (today).
We didn't know any judgments about associativity before today, so that
rule doesn't give us any new judgments.

    -- on the first day, we know about associativity of 0
    (0 + 0) + 0 ≡ 0 + (0 + 0)   ...   (0 + 4) + 5 ≡ 0 + (4 + 5)   ...

Then we repeat the process, so on the next day we know about all the
judgments from the day before, plus any judgments added by the rules.
The base case tells us nothing new, but now the inductive case adds
more judgments.

    -- on the second day, we know about associativity of 0 and 1
    (0 + 0) + 0 ≡ 0 + (0 + 0)   ...   (0 + 4) + 5 ≡ 0 + (4 + 5)   ...
    (1 + 0) + 0 ≡ 1 + (0 + 0)   ...   (1 + 4) + 5 ≡ 1 + (4 + 5)   ...

And we repeat the process again.

    -- on the third day, we know about associativity of 0, 1, and 2
    (0 + 0) + 0 ≡ 0 + (0 + 0)   ...   (0 + 4) + 5 ≡ 0 + (4 + 5)   ...
    (1 + 0) + 0 ≡ 1 + (0 + 0)   ...   (1 + 4) + 5 ≡ 1 + (4 + 5)   ...
    (2 + 0) + 0 ≡ 2 + (0 + 0)   ...   (2 + 4) + 5 ≡ 2 + (4 + 5)   ...

You've got the hang of it by now.

    -- on the fourth day, we know about associativity of 0, 1, 2, and 3
    (0 + 0) + 0 ≡ 0 + (0 + 0)   ...   (0 + 4) + 5 ≡ 0 + (4 + 5)   ...
    (1 + 0) + 0 ≡ 1 + (0 + 0)   ...   (1 + 4) + 5 ≡ 1 + (4 + 5)   ...
    (2 + 0) + 0 ≡ 2 + (0 + 0)   ...   (2 + 4) + 5 ≡ 2 + (4 + 5)   ...
    (3 + 0) + 0 ≡ 3 + (0 + 0)   ...   (3 + 4) + 5 ≡ 3 + (4 + 5)   ...

The process continues.  On the _m_'th day we will know all the
judgments where the first number is less than _m_.

There is also a completely finite approach to generating the same equations,
which is left as an exercise for the reader.

#### Exercise `finite-+-assoc` (stretch) {#finite-plus-assoc}

Write out what is known about associativity of addition on each of the first four
days using a finite story of creation, as
[earlier]({{ site.baseurl }}{% link out/plfa/Naturals.md%}#finite-creation).

## Associativity with rewrite

There is more than one way to skin a cat.  Here is a second proof of
associativity of addition in Agda, using `rewrite` rather than chains of
equations.
<pre class="Agda">{% raw %}<a id="+-assoc′"></a><a id="20339" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#20339" class="Function">+-assoc′</a> <a id="20348" class="Symbol">:</a> <a id="20350" class="Symbol">∀</a> <a id="20352" class="Symbol">(</a><a id="20353" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#20353" class="Bound">m</a> <a id="20355" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#20355" class="Bound">n</a> <a id="20357" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#20357" class="Bound">p</a> <a id="20359" class="Symbol">:</a> <a id="20361" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a><a id="20362" class="Symbol">)</a> <a id="20364" class="Symbol">→</a> <a id="20366" class="Symbol">(</a><a id="20367" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#20353" class="Bound">m</a> <a id="20369" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="20371" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#20355" class="Bound">n</a><a id="20372" class="Symbol">)</a> <a id="20374" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="20376" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#20357" class="Bound">p</a> <a id="20378" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Equality.html#83" class="Datatype Operator">≡</a> <a id="20380" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#20353" class="Bound">m</a> <a id="20382" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="20384" class="Symbol">(</a><a id="20385" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#20355" class="Bound">n</a> <a id="20387" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="20389" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#20357" class="Bound">p</a><a id="20390" class="Symbol">)</a>
<a id="20392" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#20339" class="Function">+-assoc′</a> <a id="20401" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#115" class="InductiveConstructor">zero</a>    <a id="20409" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#20409" class="Bound">n</a> <a id="20411" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#20411" class="Bound">p</a>                          <a id="20438" class="Symbol">=</a>  <a id="20441" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Equality.html#140" class="InductiveConstructor">refl</a>
<a id="20446" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#20339" class="Function">+-assoc′</a> <a id="20455" class="Symbol">(</a><a id="20456" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="20460" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#20460" class="Bound">m</a><a id="20461" class="Symbol">)</a> <a id="20463" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#20463" class="Bound">n</a> <a id="20465" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#20465" class="Bound">p</a>  <a id="20468" class="Keyword">rewrite</a> <a id="20476" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#20339" class="Function">+-assoc′</a> <a id="20485" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#20460" class="Bound">m</a> <a id="20487" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#20463" class="Bound">n</a> <a id="20489" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#20465" class="Bound">p</a>  <a id="20492" class="Symbol">=</a>  <a id="20495" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Equality.html#140" class="InductiveConstructor">refl</a>{% endraw %}</pre>

For the base case, we must show:

    (zero + n) + p ≡ zero + (n + p)

Simplifying both sides with the base case of addition yields the equation:

    n + p ≡ n + p

This holds trivially. The proof that a term is equal to itself is written `refl`.

For the inductive case, we must show:

    (suc m + n) + p ≡ suc m + (n + p)

Simplifying both sides with the inductive case of addition yields the equation:

    suc ((m + n) + p) ≡ suc (m + (n + p))

After rewriting with the inductive hypothesis these two terms are equal, and the
proof is again given by `refl`.  Rewriting by a given equation is indicated by
the keyword `rewrite` followed by a proof of that equation.  Rewriting avoids
not only chains of equations but also the need to invoke `cong`.


## Commutativity with rewrite

Here is a second proof of commutativity of addition, using `rewrite` rather than
chains of equations.
<pre class="Agda">{% raw %}<a id="+-identity′"></a><a id="21414" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21414" class="Function">+-identity′</a> <a id="21426" class="Symbol">:</a> <a id="21428" class="Symbol">∀</a> <a id="21430" class="Symbol">(</a><a id="21431" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21431" class="Bound">n</a> <a id="21433" class="Symbol">:</a> <a id="21435" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a><a id="21436" class="Symbol">)</a> <a id="21438" class="Symbol">→</a> <a id="21440" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21431" class="Bound">n</a> <a id="21442" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="21444" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#115" class="InductiveConstructor">zero</a> <a id="21449" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Equality.html#83" class="Datatype Operator">≡</a> <a id="21451" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21431" class="Bound">n</a>
<a id="21453" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21414" class="Function">+-identity′</a> <a id="21465" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#115" class="InductiveConstructor">zero</a> <a id="21470" class="Symbol">=</a> <a id="21472" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Equality.html#140" class="InductiveConstructor">refl</a>
<a id="21477" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21414" class="Function">+-identity′</a> <a id="21489" class="Symbol">(</a><a id="21490" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="21494" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21494" class="Bound">n</a><a id="21495" class="Symbol">)</a> <a id="21497" class="Keyword">rewrite</a> <a id="21505" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21414" class="Function">+-identity′</a> <a id="21517" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21494" class="Bound">n</a> <a id="21519" class="Symbol">=</a> <a id="21521" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Equality.html#140" class="InductiveConstructor">refl</a>

<a id="+-suc′"></a><a id="21527" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21527" class="Function">+-suc′</a> <a id="21534" class="Symbol">:</a> <a id="21536" class="Symbol">∀</a> <a id="21538" class="Symbol">(</a><a id="21539" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21539" class="Bound">m</a> <a id="21541" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21541" class="Bound">n</a> <a id="21543" class="Symbol">:</a> <a id="21545" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a><a id="21546" class="Symbol">)</a> <a id="21548" class="Symbol">→</a> <a id="21550" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21539" class="Bound">m</a> <a id="21552" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="21554" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="21558" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21541" class="Bound">n</a> <a id="21560" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Equality.html#83" class="Datatype Operator">≡</a> <a id="21562" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="21566" class="Symbol">(</a><a id="21567" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21539" class="Bound">m</a> <a id="21569" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="21571" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21541" class="Bound">n</a><a id="21572" class="Symbol">)</a>
<a id="21574" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21527" class="Function">+-suc′</a> <a id="21581" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#115" class="InductiveConstructor">zero</a> <a id="21586" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21586" class="Bound">n</a> <a id="21588" class="Symbol">=</a> <a id="21590" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Equality.html#140" class="InductiveConstructor">refl</a>
<a id="21595" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21527" class="Function">+-suc′</a> <a id="21602" class="Symbol">(</a><a id="21603" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="21607" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21607" class="Bound">m</a><a id="21608" class="Symbol">)</a> <a id="21610" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21610" class="Bound">n</a> <a id="21612" class="Keyword">rewrite</a> <a id="21620" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21527" class="Function">+-suc′</a> <a id="21627" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21607" class="Bound">m</a> <a id="21629" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21610" class="Bound">n</a> <a id="21631" class="Symbol">=</a> <a id="21633" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Equality.html#140" class="InductiveConstructor">refl</a>

<a id="+-comm′"></a><a id="21639" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21639" class="Function">+-comm′</a> <a id="21647" class="Symbol">:</a> <a id="21649" class="Symbol">∀</a> <a id="21651" class="Symbol">(</a><a id="21652" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21652" class="Bound">m</a> <a id="21654" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21654" class="Bound">n</a> <a id="21656" class="Symbol">:</a> <a id="21658" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a><a id="21659" class="Symbol">)</a> <a id="21661" class="Symbol">→</a> <a id="21663" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21652" class="Bound">m</a> <a id="21665" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="21667" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21654" class="Bound">n</a> <a id="21669" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Equality.html#83" class="Datatype Operator">≡</a> <a id="21671" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21654" class="Bound">n</a> <a id="21673" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="21675" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21652" class="Bound">m</a>
<a id="21677" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21639" class="Function">+-comm′</a> <a id="21685" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21685" class="Bound">m</a> <a id="21687" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#115" class="InductiveConstructor">zero</a> <a id="21692" class="Keyword">rewrite</a> <a id="21700" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21414" class="Function">+-identity′</a> <a id="21712" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21685" class="Bound">m</a> <a id="21714" class="Symbol">=</a> <a id="21716" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Equality.html#140" class="InductiveConstructor">refl</a>
<a id="21721" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21639" class="Function">+-comm′</a> <a id="21729" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21729" class="Bound">m</a> <a id="21731" class="Symbol">(</a><a id="21732" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="21736" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21736" class="Bound">n</a><a id="21737" class="Symbol">)</a> <a id="21739" class="Keyword">rewrite</a> <a id="21747" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21527" class="Function">+-suc′</a> <a id="21754" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21729" class="Bound">m</a> <a id="21756" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21736" class="Bound">n</a> <a id="21758" class="Symbol">|</a> <a id="21760" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21639" class="Function">+-comm′</a> <a id="21768" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21729" class="Bound">m</a> <a id="21770" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21736" class="Bound">n</a> <a id="21772" class="Symbol">=</a> <a id="21774" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Equality.html#140" class="InductiveConstructor">refl</a>{% endraw %}</pre>
In the final line, rewriting with two equations is
indicated by separating the two proofs of the relevant equations by a
vertical bar; the rewrite on the left is performed before that on the
right.


## Building proofs interactively

It is instructive to see how to build the alternative proof of
associativity using the interactive features of Agda in Emacs.
Begin by typing

    +-assoc′ : ∀ (m n p : ℕ) → (m + n) + p ≡ m + (n + p)
    +-assoc′ m n p = ?

The question mark indicates that you would like Agda to help with
filling in that part of the code.  If you type `C-c C-l` (control-c
followed by control-l), the question mark will be replaced.

    +-assoc′ : ∀ (m n p : ℕ) → (m + n) + p ≡ m + (n + p)
    +-assoc′ m n p = { }0

The empty braces are called a _hole_, and 0 is a number used for
referring to the hole.  The hole may display highlighted in green.
Emacs will also create a new window at the bottom of the screen
displaying the text

    ?0 : ((m + n) + p) ≡ (m + (n + p))

This indicates that hole 0 is to be filled in with a proof of
the stated judgment.

We wish to prove the proposition by induction on `m`.  Move
the cursor into the hole and type `C-c C-c`.  You will be given
the prompt:

    pattern variables to case (empty for split on result):

Typing `m` will cause a split on that variable, resulting
in an update to the code.

    +-assoc′ : ∀ (m n p : ℕ) → (m + n) + p ≡ m + (n + p)
    +-assoc′ zero n p = { }0
    +-assoc′ (suc m) n p = { }1

There are now two holes, and the window at the bottom tells you what
each is required to prove:

    ?0 : ((zero + n) + p) ≡ (zero + (n + p))
    ?1 : ((suc m + n) + p) ≡ (suc m + (n + p))

Going into hole 0 and typing `C-c C-,` will display the text:

    Goal: (n + p) ≡ (n + p)
    ————————————————————————————————————————————————————————————
    p : ℕ
    n : ℕ

This indicates that after simplification the goal for hole 0 is as
stated, and that variables `p` and `n` of the stated types are
available to use in the proof.  The proof of the given goal is
trivial, and going into the goal and typing `C-c C-r` will fill it in.
Typing `C-c C-l` renumbers the remaining hole to 0:

    +-assoc′ : ∀ (m n p : ℕ) → (m + n) + p ≡ m + (n + p)
    +-assoc′ zero n p = refl
    +-assoc′ (suc m) n p = { }0

Going into the new hole 0 and typing `C-c C-,` will display the text:

    Goal: suc ((m + n) + p) ≡ suc (m + (n + p))
    ————————————————————————————————————————————————————————————
    p : ℕ
    n : ℕ
    m : ℕ

Again, this gives the simplified goal and the available variables.
In this case, we need to rewrite by the induction
hypothesis, so let's edit the text accordingly:

    +-assoc′ : ∀ (m n p : ℕ) → (m + n) + p ≡ m + (n + p)
    +-assoc′ zero n p = refl
    +-assoc′ (suc m) n p rewrite +-assoc′ m n p = { }0

Going into the remaining hole and typing `C-c C-,` will display the text:

    Goal: suc (m + (n + p)) ≡ suc (m + (n + p))
    ————————————————————————————————————————————————————————————
    p : ℕ
    n : ℕ
    m : ℕ

The proof of the given goal is trivial, and going into the goal and
typing `C-c C-r` will fill it in, completing the proof:

    +-assoc′ : ∀ (m n p : ℕ) → (m + n) + p ≡ m + (n + p)
    +-assoc′ zero n p = refl
    +-assoc′ (suc m) n p rewrite +-assoc′ m n p = refl


#### Exercise `+-swap` (recommended) {#plus-swap} 

Show

    m + (n + p) ≡ n + (m + p)

for all naturals `m`, `n`, and `p`. No induction is needed,
just apply the previous results which show addition
is associative and commutative.

#### Exercise `*-distrib-+` (recommended) {#times-distrib-plus}

Show multiplication distributes over addition, that is,

    (m + n) * p ≡ m * p + n * p

for all naturals `m`, `n`, and `p`.

#### Exercise `*-assoc` (recommended) {#times-assoc}

Show multiplication is associative, that is,

    (m * n) * p ≡ m * (n * p)

for all naturals `m`, `n`, and `p`.

#### Exercise `*-comm` {#times-comm}

Show multiplication is commutative, that is,

    m * n ≡ n * m

for all naturals `m` and `n`.  As with commutativity of addition,
you will need to formulate and prove suitable lemmas.

#### Exercise `0∸n≡0` {#zero-monus}

Show

    zero ∸ n ≡ zero

for all naturals `n`. Did your proof require induction?

#### Exercise `∸-+-assoc` {#monus-plus-assoc}

Show that monus associates with addition, that is,

    m ∸ n ∸ p ≡ m ∸ (n + p)

for all naturals `m`, `n`, and `p`.

#### Exercise `Bin-laws` (stretch) {#Bin-laws}

Recall that 
Exercise [Bin]({{ site.baseurl }}{% link out/plfa/Naturals.md%}#Bin)
defines a datatype of bitstrings representing natural numbers
<pre class="Agda">{% raw %}<a id="26356" class="Keyword">data</a> <a id="Bin"></a><a id="26361" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#26361" class="Datatype">Bin</a> <a id="26365" class="Symbol">:</a> <a id="26367" class="PrimitiveType">Set</a> <a id="26371" class="Keyword">where</a>
  <a id="Bin.nil"></a><a id="26379" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#26379" class="InductiveConstructor">nil</a> <a id="26383" class="Symbol">:</a> <a id="26385" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#26361" class="Datatype">Bin</a>
  <a id="Bin.x0_"></a><a id="26391" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#26391" class="InductiveConstructor Operator">x0_</a> <a id="26395" class="Symbol">:</a> <a id="26397" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#26361" class="Datatype">Bin</a> <a id="26401" class="Symbol">→</a> <a id="26403" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#26361" class="Datatype">Bin</a>
  <a id="Bin.x1_"></a><a id="26409" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#26409" class="InductiveConstructor Operator">x1_</a> <a id="26413" class="Symbol">:</a> <a id="26415" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#26361" class="Datatype">Bin</a> <a id="26419" class="Symbol">→</a> <a id="26421" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#26361" class="Datatype">Bin</a>{% endraw %}</pre>
and asks you to define functions

    inc   : Bin → Bin
    to    : ℕ → Bin
    from  : Bin → ℕ

Consider the following laws, where `n` ranges over naturals and `x`
over bitstrings.

    from (inc x) ≡ suc (from x)
    to (from x) ≡ x
    from (to n) ≡ n

For each law: if it holds, prove; if not, give a counterexample.


## Standard library

Definitions similar to those in this chapter can be found in the standard library.
<pre class="Agda">{% raw %}<a id="26876" class="Keyword">import</a> <a id="26883" href="https://agda.github.io/agda-stdlib/Data.Nat.Properties.html" class="Module">Data.Nat.Properties</a> <a id="26903" class="Keyword">using</a> <a id="26909" class="Symbol">(</a><a id="26910" href="https://agda.github.io/agda-stdlib/Data.Nat.Properties.html#7678" class="Function">+-assoc</a><a id="26917" class="Symbol">;</a> <a id="26919" href="https://agda.github.io/agda-stdlib/Data.Nat.Properties.html#7834" class="Function">+-identityʳ</a><a id="26930" class="Symbol">;</a> <a id="26932" href="https://agda.github.io/agda-stdlib/Data.Nat.Properties.html#7575" class="Function">+-suc</a><a id="26937" class="Symbol">;</a> <a id="26939" href="https://agda.github.io/agda-stdlib/Data.Nat.Properties.html#8011" class="Function">+-comm</a><a id="26945" class="Symbol">)</a>{% endraw %}</pre>

## Unicode

This chapter uses the following unicode.

    ∀  U+2200  FOR ALL (\forall, \all)
    ʳ  U+02B3  MODIFIER LETTER SMALL R (\^r)
    ′  U+2032  PRIME (\')
    ″  U+2033  DOUBLE PRIME (\')
    ‴  U+2034  TRIPLE PRIME (\')
    ⁗  U+2057  QUADRUPLE PRIME (\')

Similar to `\r`, the command `\^r` gives access to a variety of
superscript rightward arrows, and also a superscript letter `r`.
The command `\'` gives access to a range of primes (`′ ″ ‴ ⁗`).
