# recall-eval

An agent decides what to remember with a handful of numbers: a cosine threshold, a top-k, a recency half-life. Those numbers are rarely revisited, because revisiting one needs a record of what it did, and there usually isn't one. recall-eval keeps that record. Every retrieval writes the component scores of its top candidates to a log, surfaced or not. Afterwards you can ask what a different threshold would have surfaced on traffic that already happened, and once a sample is judged, which threshold was actually better.

The retrieval half is ordinary: a lexical channel and an optional semantic one, and a candidate surfaces when it clears either. The half that matters is the log and the replay. Most retrieval systems keep only the final answer, so the thresholds that shaped it can only be guessed at afterward. Keeping the rejected candidates next to the admitted ones is what turns "what would a lower threshold have admitted" into a question with an answer.

On the bundled corpus of twelve synthetic notes, the gate peaks at F1 0.900 on a plateau running from about 0.53 to 0.5575. Precision climbs as the gate tightens and recall collapses faster, which is the trade a single hit rate hides.

## What the record caught

I convinced myself the lexical score should be normalized by the best score a query could reach, since raw scores scale with query length and a threshold that does not transfer is not worth tuning. The first sweep agreed loudly: normalizing cost 16 F1 points.

That was wrong. Normalized scores compress toward zero, so their optimum sits near a lexical gate of 0.015, and the 0.025 grid step I had reused from the unnormalized run stepped clean over it. I was comparing a measured peak against a missed one. On a finer 0.005 grid, each configuration at its own optimum, the real difference was 0.900 against 0.885. A point and a half. Close enough to be a coin flip on this corpus.

Normalization ships off, for a weak reason honestly stated instead of a strong reason that turned out to be an artifact. The general lesson is worth more than the feature. A sweep is only as good as its resolution. An optimum at the edge of a grid, or on a grid too coarse for the scale it searches, describes the grid, not the system.

## What it does not claim

The bundled numbers come from twelve synthetic notes. They demonstrate the machinery, not anyone's real corpus, and because scores are unnormalized their scale depends on query length and corpus size, so the default threshold will not transfer. The semantic axis is inert in the demo because nothing supplies an embedder, and the tool refuses to name a winning cosine threshold it never measured rather than print a number that only looks like a result. Recall is measured over the logged pool, not the whole corpus, so a memory that never entered the pool cannot count as a miss.

Zero runtime dependencies, ninety-two tests, MIT. The source is at [github.com/apban/recall-eval](https://github.com/apban/recall-eval).
