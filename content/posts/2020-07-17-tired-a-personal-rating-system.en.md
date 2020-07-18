+++
title = "TIReD: A Personal Rating System"
date = 2020-07-17T21:30:00-05:00
slug = "tired-a-personal-rating-system"
draft = false
+++

As the pandemic gives me a chance to look through my backlog of movies, shows, and books (read: anime and manga), I started to consider establishing a personal rating system to ease up writing (hypothetical) reviews.


## Guiding Principles {#guiding-principles}

Typical rating scales feature 10 or more levels, which is in my opinion way too wide a range to choose from, not to mention those featuring a 100-point-scales. Even the most common 5-star system gets cumbersome fast as soon as we take half-stars into consideration. What exactly differentiates a 6 from a 7 or a 4.6 from a 5.1? Higher granularity could be useful in aggregated ratings, but not so much from an individual reviewer's perspective. I much prefer the approach [s1vote](http://s1vote.com/) took: give the users fewer but more distinctive levels to pick from.

My anecdotal evidences show that most online ratings converge around the 70% mark, a rating just as safe and useless as [predicting a 40% success rate for anything](https://statmodeling.stat.columbia.edu/2020/06/29/this-one-quick-trick-will-allow-you-to-become-a-star-forecaster/). In other words, the lower half of most rating scales are underutilized: how often would you rate something one-and-a-half-star instead of just one? Besides, more often than not, I read ratings and reviews to find out about good shows, not the bad ones. It should be sufficient to only focus on "the better half": why would I sit through the entirety of a bad show and take the effort to give it a rating anyways? There is no -1 star in Michelin Guide, is there?

Summarizing the quality of anything with a single metric seems unfair. I want the rating system to be more expressive, capable of conveying the different aspects of a show that I find enjoyable. At the very minimum, an opinionated pick should be distinct from something with a more general appeal.


## Rating Methodology {#rating-methodology}

Enter the TIReD scale! The following uses anime/tv shows as the example here, but much of this methodology also applies to other art forms. A show is scored in the following categories, with sum of points forming the final rating:

| Category                         | Range |
|----------------------------------|-------|
| <strong>T</strong>angible        | 0-2   |
| <strong>I</strong>ntangible      | 0-2   |
| <strong>Re</strong>visit-ability | 0-1   |
| <strong>D</strong>iscretionary   | 0-1   |

Tangible aspects of a show include visual style, animation, soundtrack, CG quality, special effect, etc. To put it simply, how physically well-made a show is. Starting from a score of 0, a show would be scored a

-   +1 if the show is overall attractive to watch and either has consistent high quality with very few shortcoming (perfection) or utilizes unique ideas/techniques to great effects (ingenious);
-   +2 if its physical quality/way of expression alone would be sufficient reason to watch the show, even if it gets a 0 in all other categories.

Intangible aspects include story, character building, plot pacing, cultural reference, etc. This quality should be relatively medium independent, i.e. I would enjoy a faithful recreation of the story in other art forms at least just as much. Criteria for scoring is similar except for remakes/adaptations with an clear intent to follow the original and when I have seen/read the source material: scoring would be based on the source material's intangible score adjusted downwards by 1 point, with at most extra 1 point adjustment based on quality/difficulty/effect of the remake/adaptation with in the range of 0-2. For instance, a mediocre retelling of a +2 story should only be awarded at most a +1. Remakes and adaptations probably have an easier starting point than original contents, so I wanted to adjust for "how good the show could have been", provide an answer to "should I still see this if I've seen the original", and pick out the "watch this instead of the original" or "transcended and elevated the original story" shows.

Revisit-ability, as the name indicates, represents whether I would want to revisit/rewatch the show later. This correlates more with my own taste or nostalgia: is this something that I would gladly jump into in an leisure afternoon. Longer shows tend to suffer a bit by this metric, so I would take into account of especially memorable segments/episodes. However, in event of remakes and adaptations, this point should generally only be rewarded to the best version of the work in my point of view.

Discretionary point should be awarded sparingly and only when a show doesn't already achieve full scores in all other categories, making the possible maximum score 5 instead of 6. This is used as an adjustment for shows that I feel the current rating system doesn't do it justice. Common situations where this applies include but are not limited to:

-   categorical superiority: best of its kind;
-   a tight coupling between tangible and intangible aspects of the work: it simply won't be the same without one another;
-   quality in spite of objective limitations, especially for older shows or those with a tight budget.


## Format {#format}

A TIReD rating is recorded as `X=T/I/Re[+D]`. For instance:

-   a show scoring 1 in tangible, 2 in intangible, 0 in revisit-ability, and 0 in discretionary would be recorded as `3=1/2/0`;
-   a show scoring 1 in tangible, 0 in intangible, 0 in revisit-ability, and 1 in discretionary would be recorded as `2=1/0/0+1`.

Shows that I abandoned halfway, meaning I won't be able to give a rating, will be marked as `DNF` (did not finish).


## Self Q&A {#self-q-and-a}

Some fragments of thoughts that I came across when designing TIReD.

Q: How should tangible points for books be awarded?

A: I'd say it's how good the writing is at face value, i.e. is it "literature" worthy. While I not really confident in my ability of identifying great works, but a +2 should at least be something better than _Harry Potter_.

Q: How should world settings built up in previous/related works affect the rating?

A: World building actually fits into both revisit-ability (if the system/world is interesting and makes me want to read more about it) and intangible quality (whether the character actions are justified).

Q: How was the rule for discretionary point determined?

A: The best shows should always get full score regardless of the exact scale, so awarding them discretionary points is meaningless. However, there are seemingly not-so-impressive works that really show the passion/devotion/love/good faith of the production team/author and shows whose existence alone is a boon for its fans. I want to express my enjoyment in a way that still allows me to assess the tangible and intangible aspects of a show on an absolute scale, as any further complication can be taken account of as discretionary point.

Q: What happens to ratings for a remake before and after you watch the original?

A: I'll adjust score for the remake now that I have experienced the original.

Q: A lot of details could be lost in translation. How to deal with translated works?

A: For now I will treat these the same way as remakes: adjust the rating if someday I came across the original.

Q: How did you come up with the name "TIReD" (and name for the categories)?

A: The first category to have a concrete name is revisit-ability. From there on it's mostly just playing around with words and initials. I almost settled on "TIRD" thanks to [Urban Dictionary](https://www.urbandictionary.com/define.php?term=tird). Well, not everything is sh\*t. 😜
