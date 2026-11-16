---
layout: post
title: "The Hardest Logic Puzzle Ever: Three Gods, Two Unknown Words, and Why Three Questions Are Exactly Enough"
excerpt: "Working through George Boolos's puzzle about True, False and Random, using arithmetic mod 2 to build a question whose answer doesn't depend on who's lying or what ja means, and a counting argument showing that three questions is the fewest that can work and that the first one has to be asked a particular way."
date: 2026-11-16
categories: [puzzles]
image: /assets/images/hardest_logic_puzzle_tree.png
---

George Boolos called this one the hardest logic puzzle ever. There are three gods, A, B and C. One of them is True, who always tells the truth, one is False, who always lies, and one is Random, whose answers come out true or false at random. You don't know which is which. You get three yes/no questions, each one put to a single god of your choosing, and the goal is to work out which god is which. The catch is that the gods understand your questions but answer in their own language, with the words "ja" and "da", and you don't know which of the two means yes. I wanted to work out why three questions are enough and why two never are. The whole thing comes down to arithmetic mod 2 and a counting argument, and the counting argument turns out to say more than the lower bound: it forces almost every choice in the strategy. At the end I check the strategy by computer against every arrangement of the gods, both meanings of "ja", and every way Random could answer.

I'll state the rules precisely first, since some of the answer depends on them. The assumptions are:

1. The roles are a bijection \\(\sigma\\) from the gods \\(\\{A, B, C\\}\\) to the roles \\(\\{T, F, R\\}\\), and nothing is known about \\(\sigma\\) at the start. There are \\(3! = 6\\) such bijections, and I'll write them as strings giving the roles of A, B and C in order, so \\(TRF\\) means A is True, B is Random and C is False.
2. Every question \\(Q\\) is a yes/no question with a definite truth value once \\(\sigma\\) and the language are fixed. I'll write \\(q \in \\{0, 1\\}\\) for that truth value, with 1 for true.
3. True answers every question according to its truth value, and False answers according to its negation.
4. Random can answer each question either way, and what it says doesn't depend on the truth of anything. A strategy only counts as working if it gets the right \\(\sigma\\) for every possible sequence of answers from Random.
5. Either "ja" means yes for every god or "ja" means no for every god, and this doesn't change during the puzzle. I'll write \\(j = 1\\) if "ja" means yes and \\(j = 0\\) if it means no.
6. The questions are asked one at a time, and each question, including which god it goes to, can depend on the answers so far.
7. A question can ask a god what it would say to some other question, and True and False answer that kind of question by assumption 3 like any other.

Assumption 4 is stronger than saying Random flips a fair coin. It treats Random as an opponent who picks each answer to make me fail, and a strategy that beats every choice the opponent could make also beats every sequence of coin flips. So "works" never involves probability in this post: every possible run has to end at the right answer.

The first problem is that a plain question tells you nothing. I'll do the arithmetic in \\(\mathbb{F}\_2\\), the integers mod 2, where \\(\oplus\\) is addition, \\(x \oplus x = 0\\) for every \\(x\\), and \\(\oplus\\) is associative and commutative. Let \\(t = 1\\) if the god being asked is True and \\(t = 0\\) if it's False. The answer the god means to give, as a bit \\(i\\) with 1 for yes, is \\(q\\) for True and \\(q \oplus 1\\) for False. Both cases fit one formula:

\\[i = q \oplus t \oplus 1\\]

since \\(t = 1\\) gives \\(q \oplus 1 \oplus 1 = q\\) and \\(t = 0\\) gives \\(q \oplus 1\\). The god says "ja" when the meaning of "ja" matches what it means to say, that is when \\(i = j\\). For two bits, \\(x = y\\) exactly when \\(x \oplus y = 0\\), so the bit \\([x = y]\\) is \\(x \oplus y \oplus 1\\). Writing \\(s(Q) = 1\\) when the god says "ja" to \\(Q\\),

\\[s(Q) = i \oplus j \oplus 1 = (q \oplus t \oplus 1) \oplus j \oplus 1 = q \oplus t \oplus j \oplus (1 \oplus 1) = q \oplus t \oplus j\\]

The word you hear is the truth value of \\(Q\\) shifted by \\(t \oplus j\\), and you know neither \\(t\\) nor \\(j\\). For any \\(q\\), the case \\(t = j = 1\\) gives \\(s = q\\) and the case \\(t = 1, j = 0\\) gives \\(s = q \oplus 1\\), so either word is consistent with either truth value. The fourth column of Figure 1 shows this: "ja" shows up in exactly half the rows where \\(Q\\) is true and half the rows where it's false.

The fix is to ask a question about the answer instead. Define the embedded question

\\[E(Q) = \text{If I asked you } Q \text{, would you say ja?}\\]

By assumption 7, the truth value of \\(E(Q)\\) for a given god is whether that god would say "ja" to \\(Q\\), which is \\(e = s(Q) = q \oplus t \oplus j\\). Asking \\(E(Q)\\) out loud is just asking a question whose truth value is \\(e\\), so by the formula above the god says "ja" to it when

\\[s(E(Q)) = e \oplus t \oplus j = (q \oplus t \oplus j) \oplus t \oplus j\\]

Regrouping with associativity and commutativity,

\\[s(E(Q)) = q \oplus (t \oplus t) \oplus (j \oplus j) = q \oplus 0 \oplus 0 = q\\]

So True and False both say "ja" to \\(E(Q)\\) exactly when \\(Q\\) is true, whatever "ja" means. The liar's lie and the unknown language each show up twice and cancel. The last column of Figure 1 checks all eight combinations of \\(q\\), \\(t\\) and \\(j\\) directly.

![A table with eight rows covering every combination of whether Q is true, whether the god is True, and whether ja means yes. The fourth column, the answer to Q, has ja in four rows with no pattern. The fifth column, the answer to E(Q), is ja in exactly the four rows where Q is true.](/assets/images/hardest_logic_puzzle_embedded.png)
*Figure 1. The word True or False says to \\(Q\\) and to the embedded question \\(E(Q)\\), for every combination of \\(q\\), \\(t\\) and \\(j\\). Orange cells are "ja". The answer to \\(E(Q)\\) matches the first column in every row.*

Two things about this identity matter for what follows. It only applies to True and False, since by assumption 4 Random's answer to \\(E(Q)\\) is as arbitrary as its answer to anything else. And \\(j\\) drops out completely, which means a strategy built from embedded questions never learns what "ja" means. I'll come back to that at the end.

Before building a strategy, here's why two questions can't work. Any strategy can be drawn as a binary tree. Each internal node is a question to a particular god, its two edges are labeled "ja" and "da", and each leaf is labeled with the \\(\sigma\\) that the strategy announces when it ends up there. By assumption 6 the tree can branch any way it likes, but a strategy with \\(k\\) questions has depth at most \\(k\\), and a binary tree of depth at most \\(k\\) has at most \\(2^k\\) leaves: a tree of depth 0 has one leaf, and adding a level at most doubles the count.

For each \\(\sigma\\), let \\(L(\sigma)\\) be the set of leaves that some run of the strategy reaches when the true roles are \\(\sigma\\), over both values of \\(j\\) and every choice Random can make. Each \\(L(\sigma)\\) is nonempty, since some run always happens. The strategy works exactly when every leaf in \\(L(\sigma)\\) is labeled \\(\sigma\\). A leaf has only one label, so if the strategy works, \\(L(\sigma) \cap L(\sigma\') = \varnothing\\) for \\(\sigma \neq \sigma\'\\), and

\\[6 \leq \sum\_{\sigma} \lvert L(\sigma) \rvert \leq 2^k\\]

That needs \\(k \geq \log\_2 6 \approx 2.585\\), so \\(k \geq 3\\). This part of the argument has nothing to do with Random or the unknown language. Even with three honest gods who answer in English, two questions only have four possible outcomes, and four outcomes can't separate six arrangements.

Random makes the count tighter. Say the first question goes to god \\(X\\). There are two arrangements in which \\(X\\) is Random, and by assumption 4, in each of them \\(X\\) can answer either "ja" or "da". So each of those two arrangements reaches leaves in both subtrees below the first question, and \\(\lvert L(\sigma) \rvert \geq 2\\) for both. The sum above becomes

\\[\sum\_{\sigma} \lvert L(\sigma) \rvert \geq 2 \cdot 2 + 4 \cdot 1 = 8 = 2^3\\]

With \\(k = 3\\) both sides equal 8, so there's no slack at all, and every inequality along the way has to be an equality. Each subtree below the first question has depth 2 and so at most 4 leaves. Each one has to hold a leaf for both arrangements where \\(X\\) is Random, which leaves room for exactly 2 of the other 4 arrangements. So the first question has to split the four arrangements where \\(X\\) isn't Random into two pairs, one pair for each answer.

The same counting rules out asking Random anything after the first question. Inside one subtree, the 4 leaves have to cover 4 arrangements, one leaf each. If the second question went to a god \\(Y\\) who is Random in one of those 4 arrangements, that arrangement could reach both children of the second question and would need 2 leaves, for a total of at least 5. The third question works the same way inside its subtree. So once the first answer is in, every later question has to go to a god who is certainly not Random, given that answer. Most of the strategy is forced by this point. What's left is finding a first question that does the 2 and 2 split and also identifies a god who isn't Random.

The first question is \\(E(\text{Is A Random?})\\), asked to B. There are two cases.

If B is Random, the arrangement is \\(TRF\\) or \\(FRT\\), and B's answer is arbitrary. Either way, A and C are both not Random.

If B isn't Random, B is True or False, and by the identity above B says "ja" exactly when A is Random. When A is Random the arrangement is \\(RTF\\) or \\(RFT\\), and B says "ja". When C is Random it's \\(TFR\\) or \\(FTR\\), and B says "da".

Putting the cases together, a "ja" leaves \\(\sigma \in \\{RTF, RFT, TRF, FRT\\}\\), and C isn't Random in any of them. A "da" leaves \\(\sigma \in \\{TRF, FRT, TFR, FTR\\}\\), and A isn't Random in any of them. Each answer leaves exactly 4 arrangements, the two where B is Random plus one of the pairs, which is the split the counting argument requires. Figure 2 lays this out.

![A table of the six arrangements of the gods, with the Random god in each row highlighted, and two columns for B's answer. The two arrangements with A Random can only produce ja, the two with C Random can only produce da, and the two with B Random can produce either. Each column ends up with four arrangements.](/assets/images/hardest_logic_puzzle_split.png)
*Figure 2. Which answers to the first question, \\(E(\text{Is A Random?})\\) asked to B, are possible under each arrangement. The two arrangements where B is Random appear under both answers, and each answer leaves 4 arrangements.*

Let \\(X\\) be the god who is now known not to be Random, C after a "ja" and A after a "da". The second question is \\(E(\text{Are you True?})\\), asked to \\(X\\). Since \\(X\\) isn't Random, the identity applies, and \\(X\\) says "ja" exactly when it's True. That splits the 4 remaining arrangements into two pairs. After a "ja" to the first question, for example, C saying "ja" leaves \\(\\{RFT, FRT\\}\\), and C saying "da" leaves \\(\\{RTF, TRF\\}\\).

The third question is \\(E(\text{Is B Random?})\\), again asked to \\(X\\). In each pair from the second question, Random is B in one arrangement and not in the other, so this question separates them. Checking all four pairs: after "ja, ja" the pair is \\(\\{FRT, RFT\\}\\), after "ja, da" it's \\(\\{TRF, RTF\\}\\), after "da, ja" it's \\(\\{TRF, TFR\\}\\), and after "da, da" it's \\(\\{FRT, FTR\\}\\). In each pair exactly one arrangement has B as Random. Once the roles of \\(X\\) and Random are known, the third god's role is forced, since \\(\sigma\\) is a bijection. Figure 3 shows the whole tree. It has 8 leaves for 6 arrangements, and the two arrangements where B is Random, \\(TRF\\) and \\(FRT\\), each appear once in each half, as the counting argument said they would.

![A decision tree. The root asks B whether, if asked "Is A Random?", it would say ja. The ja branch then asks C two embedded questions, "Are you True?" and "Is B Random?", and the da branch asks A the same two questions. The eight leaves give a full assignment of roles. The arrangements A True, B Random, C False and A False, B Random, C True each appear twice, once in each half of the tree.](/assets/images/hardest_logic_puzzle_tree.png)
*Figure 3. The full three-question strategy. Every question after the first goes to a god who can't be Random given the first answer. Each leaf shows the roles the strategy announces.*

To check all of this I wrote out the model in code, with each god's answer computed from assumptions 3 to 5 and each embedded question evaluated by working out what the god would say to the inner question, so the check doesn't assume the identity. Then I ran the strategy on every combination of the 6 arrangements, both values of \\(j\\), and every sequence of answers Random could give. Random is only ever asked the first question, and only when B is Random, so there are \\(4 \cdot 2 = 8\\) runs from the arrangements where B isn't Random and \\(2 \cdot 2 \cdot 2 = 8\\) from the two where it is (2 arrangements, 2 languages, 2 answers from Random), for 16 runs in all. All 16 ended at the right arrangement, and together they reached all 8 leaves of Figure 3, matching the count of \\(\sum\_\sigma \lvert L(\sigma) \rvert = 8\\).

The strategy never finds out what "ja" means, and no strategy built this way can. Every answer from True or False to an embedded question is \\(q\\), with no \\(j\\) in it, and Random's words are arbitrary. So for every run with \\(j = 1\\) there's a run with \\(j = 0\\) that hears exactly the same words and reaches the same leaf. The puzzle only asks for the roles, so this doesn't matter, but it means the unknown language adds nothing to the difficulty once the embedded question is available. Everything that makes the puzzle hard comes from Random.

A few things here rest on choices I made. Treating Random as an opponent is the strictest reading of assumption 4, and it's what forces the two arrangements where the first god is Random into both subtrees. Assumption 2 says every question has a definite truth value, and assumption 7 extends that to questions about what a god would say. A question about what a god is about to say to that same question might not have a consistent answer for True or False. If a god could respond to a question like that with something other than "ja" or "da", each question would have three possible outcomes, two questions would have \\(3^2 = 9 \geq 6\\) outcomes, and the lower bound of three questions would no longer follow from the counting. I've also fixed the number of gods at three, and the count \\(2 \cdot 2 + 4 \cdot 1 = 2^3\\) only comes out exact at that size.

I'd like to look next at the same puzzle with \\(n\\) gods, where one is Random and the rest are split between truth-tellers and liars. The counting argument still gives a lower bound, but Random's ability to land in both subtrees of every question it's asked grows with \\(n\\), and I'd like to work out how many questions a strategy needs before it can trust anyone.
