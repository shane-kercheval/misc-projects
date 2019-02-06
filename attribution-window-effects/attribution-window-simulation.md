What affects does using (or not using) an attribution window have on A/B Testing?
=================================================================================

-   Create 20K trials in experiment, roughly 10K per randomized
    variation.
-   We'll randomly assign people into an `A` group or `B` group based on
    a random number generator 0/1

![](attribution-window-simulation_files/figure-markdown_strict/create_traffic-1.png)

Now, we'll give everyone a baseline conversion rate of `20.0%`.

Let's simulate the conversion for each person and see the overall
conversion rates for each variation.

![](attribution-window-simulation_files/figure-markdown_strict/create_conversion_rate2-1.png)

P-value of above conversions: `0.4886171`

What we've done so far has simulated an experiment in which there was
*no* change in the `A` group vs the `B` group. It was an experiment that
might as well been an `A`/`A` test.

Now, let's simulate everyone in the `B` having a `5.00%` increase (due
from the affects of the new thing we are testing out.)

![](attribution-window-simulation_files/figure-markdown_strict/unnamed-chunk-2-1.png)

Due to random varaiation (i.e. random sampling from binomial
distribution), `B`'s conversion rate of `21.4%` is a `6.46%` increase
over the *true* baseline conversion rate of `20.0%` (that, because we
are simulating, we know to be the true conversion rate), and a `6.13%`
increase over `A`'s conversion rate of `20.1%`.

The P-value is now: `0.0233485`

But, here's the problem.
------------------------

These conversion rates assume that everyone that has converted, have
been given any amount of time to convert, and regardless of how long
after they converted, they had the same lift from the experiment.

This doesn't take into account:

-   1.  the fact that people take different lengths of time to convert
        (relative to when they entered into the experiment) and

-   1.  *the effects that that the A/B test has on a person, or group of
        people, diminishes over time.* It cannot be assumed that the
        effect of seeing a certain button, or certain design, or a
        certain experience, will have the same affect in that instant
        vs. 1 hour later vs. 1 day later vs. 30 days later. The effect
        of that "thing" on a person's decision or action will almost
        always, for almost everyone, diminish over time.

If we can assume this is a safe assumption, the question is: *What are
the effects of including conversion events from people who saw the
experiment "a long time ago"?*

Will these effects not matter since it is present in both variations?

We have to do 2 things.

First, we have to *simulate people converting at different lengths of
time, relative to when they saw the experiment*. (We'll assume everyone
in the experiment has not already converted; it won't affect the
outcome.)

Second, we have to *simulate the effect of the experiment diminishing
over time*.

![](attribution-window-simulation_files/figure-markdown_strict/unnamed-chunk-3-1.png)

Now, let's simulate a diminishing effect fromt the A/B test over time.

We'll try it with *linear* diminishing, although in most cases, my
assumption is it is more of an exponential dimishing effect, but we'll
stick with linear to be conservative.

Specifically, for everyone in the `B` group, I'll d

![](attribution-window-simulation_files/figure-markdown_strict/unnamed-chunk-4-1.png)

This is the key point. The longer you give people in the experiment to
convert, the less effect the *lift* from experiment has, and the closer
the conversion rates become.

No such effect exists in the `A` group because they maintain the
baseline conversion rate (there is nothing to diminish.)

But letting people convert e.g. 25-30 days after the experiment, you
will definitely capture the people who take longer to convert, but the
majority of people who convert are no longer affected, *so they are now
converting at a similar rate as the `A` group*, which removes the

Another way of saying this is it's likely that the people who converted
after 30 days were going to do so anyway, with or without the thing you
tested (or probably the majority of nauances and brain hacks in the
website).

So let's look at the results of the A/B test with the updated conversion
rates.

![](attribution-window-simulation_files/figure-markdown_strict/unnamed-chunk-5-1.png)

Due to random varaiation (i.e. random sampling from binomial
distribution), `B`'s conversion rate of `21.1%` is a `5.00%` increase
over the *true* baseline conversion rate of `20.0%` (that, because we
are simulating, we know to be the true conversion rate), and a `4.66%`
increase over `A`'s conversion rate of `20.1%`.

The P-value is now: `0.0892394`

But this doesn't answer the question of the affects of attribution
windows.

Thoughts
========

-   Who is an A/B test inherently affecting? Are A/B tests affecting
    people that are more susceptible to certain psychological
    manipulations? Do the effects of A/B tests affect people that take
    longer to make a decision?
