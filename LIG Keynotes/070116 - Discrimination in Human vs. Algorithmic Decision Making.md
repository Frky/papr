
# Discrimination in Human vs. Algorithmic Decision Making

By Krishna P. Gummadi

January 7th, 2016

## Intro

Disclaimer: not an expert in this topic. Interested in it. Presenting preliminary results, that seems to have relevance. 

### Algorithm decision making

To assist or replace human decision making.

Increasingly being used in several domains:

* recruiting - screening job applications
* banking - credit ratings / loan approvals
* judiciary - profiling criminals, spammers (offline & online)
* journalism - news recommended systems

Algorithms are taking decisions about important things that affect our daily lives.

#### Example 

Between an accusation and a trial, judge decide if you need to be assigned to residence, 
go to jail by prevention, etc. This was a decision made on the intuition of judges. Now, there
exists a risk assessment tool to produce a decision regarding the background of the accusee.

### How algorithmic decisions are made

By learning from data on past human decisions

```
[data about user] ---> [human decisions]        # training set of data
```

A lot of learning algorithms can be used. 

#### Learning linear classifiers

Compute a weight vector for all entries in the data table and make a decision based 
on the sign of the result (data) * (weight) [matricial product].
Weight vector is computed from the training vector such that it minimizes the loss over all 
training examples (according to a particular loss function).


## Problem: Discrimination in decision making

* When differentiating users based on their features, all features are not equal!
* Laws prohibit unjust & prejudicial treatment of people based on sensitive features (e.g. race or gender).
* Independent of the feature's utility or relevance in decision making.

### What makes a feature sensitive?

* Laws
* Alignment with structure of social interactions 
    * eg socially salient features: race, gender, religion, age
    * eg non-socially salient features: toe-length, beer drinkers
* Varies from one society to another
    * eg beef-eaters in India is a socially salient group

### Discrimination in decision making

Sensitive features must be handled differently. 

## Three motivating questions

### Can algorithmic decisions be discriminatory?

Yes, of course. 

* Discrimination refers to how sensitive features are used
* Not who is using them
* A classifier can indeed be racist or sexist
    * If it is trained on biased data (eg historical data on job hiring with race or gender discrimination)

### Detour: understanding discrimination in human decision making

Walk through some sources of discrimination in human decision making and ways to detect and control it.

#### Terminology

* Direct discrimination 
    * Conscious: explicit bias against women in hiring (eg advertise positions as only for men)
    * Unconscious: implicit bias against women when hiring (eg subconsciously prefer men over women)

**Detection:** situational testing (eg comparison of two CVs with no gender info).

**Prevention:** for conscious: easy (fire the sexists and change the policy) ; for unconscious: hard (anonymise CVs).

* Indirect discrimination
    * Conscious: intentionally manipulate weights on correlated non-sensitive attributes (eg not offer parental leaves to discourage women applicants)
    * Unconscious: accidentally over-weight correlated non-sensitive attributes (eg tendency to hire people like themselves - cs equals programming)

**Detection:** very hard to detect! Sensitive features are not directly used in decisions. A solution: proportionality test over outcomes (are outcomes for men and women disproportionate?). Obviously, a disproportion can have different sources, so if not respected, in the US law the company must justify it by a business need or any other legitimate reason.

**Prevention:** Assuming detection problem is solved (no structural discrimination or business necessity), still no easy fixes to make outcomes proportional. Resort to affirmative action/quota-based system. Problem: reintroduces direct discrimination.

#### Summary
Fundamentally hard to detect or prevent indirect discrimination in human decision making. 

### Can we do better with algorithmic decision making?

#### Controlling for biases in training data

Traditional classifier training optimizes for accuracy (in matching past decisions).

#### Controlling direct discrimination

**Idea:** train classifiers only on non-sensitive features => maximize accuracy in prediction but without sensitive features.

**Good:** Eliminates both types of direct discrimination.

**Bad:** Training often introduces indirect discrimination. Exception: when sensitive and non-sensitive features are totally uncorrelated, but it is unlikely with big data with lots of features.

#### Controlling indirect discrimination

Optimize for accuracy under fairness constraints. Fairness constraints bound disproportionality in outcomes.

*How to fair constrain hinge loss classifier?* Limit the cross-covariance between items( sensitive feature values and their distance from classifiers's decision boundary to less than a threshold.

### Putting it all together

* Training a fair classifier
    * Has no direct discrimination
    * Bounds indirect discrimination
* Optimize for accuracy in prediction
    * Without using sensitive features to avoid direct discrimination
    * With fairness constraints to bound indirect discrimination
    * We can also optimize for fairness under accuracy constraints

### Evaluating fairness constraints

Tested it over UCI census income dataset:
* 45K users
* 14 features
* Non-sensitive: education level, etc.
* Sensitive: gender and race

## Summary and perspectives

*Can algorithmic decisions be discriminating?* **- Yes.**

*Can we make algorithm decisions more fair?* **- Yes, but only certain circumstances.**

### Future work

* Hybrid-decision making to gain insights ; analyze adjusted weights under fairness constraints
* Robust analysis techniques to detecting specific types of discrimination
* Accounting for complexities of real-world scenarios

## Conclusion

The big picture. Three issues:

* *Fairness:* How to ensure that users being treated fairly?
* *Transparency:* How to help users understand why and how the decisions were made?
* *Evolvability:* How to ensure that the system eventually achieves its long-term goal?

