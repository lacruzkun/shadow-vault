NIGER DELTA UNIVERSITY

Department of Computer Science

Course: Artificial Intelligence (CSC 408)

QUESTION 1

1(a). Calculate the entropy for H, A, and W.

Step 1: Define entropy

Entropy is a measure of uncertainty, impurity, or randomness in a dataset. In machine learning, it helps determine how mixed the values or classes in a dataset are.

Entropy is calculated using the formula:
$
[
\boxed{H(S)=-\sum_{i=1}^{n}p_i\log_2 p_i}
]
$
Where:

- H(S) = entropy of the dataset.
- p_i = proportion of observations belonging to category i.
- \log_2 = logarithm to base 2.
- n = number of distinct categories.

For a dataset containing two equally frequent categories, the entropy is 1 bit. If every observation belongs to the same category, the entropy is 0 bits.

Step 2: Calculate the entropy of W

From the table, the values of W are:

[
W={c,c,d,d}
]

There are four observations:

- Category c occurs twice.
- Category d occurs twice.

Therefore,

$
P(c)=\frac{2}{4}=0.5
$

$
P(d)=\frac{2}{4}=0.5
$

Substituting into the entropy formula:

$
H(W)=-[P(c)\log_2P(c)+P(d)\log_2P(d)]
$

$
H(W)=-[0.5\log_2(0.5)+0.5\log_2(0.5)]
$

Since $\log_2(0.5)=-1$,

$
H(W)=-[0.5(-1)+0.5(-1)]
$

$
H(W)=-[-0.5-0.5]
$

$
\boxed{H(W)=1\text{ bit}}
$

Step 3: Calculate the entropy of A

From the table, the values of A are:

[
A={e,f,f,f}
]

There are four observations:

- Category e occurs once.
- Category f occurs three times.

Therefore,

[
P(e)=\frac14=0.25
]

[
P(f)=\frac34=0.75
]

Substituting into the entropy formula:

[
H(A)=-[P(e)\log_2P(e)+P(f)\log_2P(f)]
]

[
H(A)=-[0.25\log_2(0.25)+0.75\log_2(0.75)]
]

Using

[
\log_2(0.25)=-2
]

and

[
\log_2(0.75)\approx-0.415
]

we obtain:

[
H(A)=-[0.25(-2)+0.75(-0.415)]
]

[
H(A)=-[-0.5-0.311]
]

$
\boxed{H(A)\approx0.811\text{ bits}}
$

Step 4: Calculate the entropy of H

From the table, the values of H are:

[
H={f,e,e,f}
]

There are four observations:

- Category e occurs twice.
- Category f occurs twice.

Therefore,

[
P(e)=\frac24=0.5
]

[
P(f)=\frac24=0.5
]

Substituting into the entropy formula:

[
H(H)=-[P(e)\log_2P(e)+P(f)\log_2P(f)]
]

[
H(H)=-[0.5\log_2(0.5)+0.5\log_2(0.5)]
]

[
H(H)=-[0.5(-1)+0.5(-1)]
]

[
\boxed{H(H)=1\text{ bit}}
]

Final answers

Attribute| Distribution| Entropy
W| c=2,\ d=2| 1.000 bits
A| e=1,\ f=3| 0.811 bits
H| e=2,\ f=2| 1.000 bits

Conclusion: W and H have the highest entropy among the three attributes, at 1 bit each. Attribute A has an entropy of approximately 0.811 bits because its values are distributed less evenly.

---

1(b). Discuss the following machine learning concepts.

(i) Overfitting

Overfitting occurs when a machine learning model learns the training data too closely, including noise and irrelevant details, instead of learning patterns that generalize to new data.

An overfitted model usually performs very well on the training data but poorly on unseen data.

Example: A decision tree that memorizes every training example, including unusual exceptions, may make poor predictions when given new examples.

Ways to prevent overfitting:

1. Use more representative training data.
2. Apply regularization.
3. Prune decision trees.
4. Use cross-validation.
5. Reduce unnecessary model complexity.

(ii) Underfitting

Underfitting occurs when a machine learning model is too simple to learn the important patterns in the training data.

An underfitted model performs poorly on both the training data and unseen data.

Example: Using a straight line to model a strongly curved relationship between two variables may produce inaccurate predictions.

Ways to prevent underfitting:

1. Use a more suitable or powerful model.
2. Add relevant features.
3. Reduce excessive regularization.
4. Train the model appropriately.

Difference between overfitting and underfitting:

Overfitting occurs when a model is excessively complex and fails to generalize well. Underfitting occurs when a model is too simple to capture the underlying patterns.

(iii) Decision tree

A decision tree is a supervised machine learning algorithm that makes predictions by following a sequence of decisions based on input features.

It has three main components:

1. Root node: The starting point, representing the first decision or feature.
2. Branches: The paths representing possible outcomes of decisions.
3. Leaf nodes: The final outcomes or predictions.

Example: A decision tree used to determine whether a student passes an examination might ask:

- Did the student attend classes regularly?
- Did the student complete the required assignments?
- Did the student score above the pass mark?

The answers determine the path through the tree until a prediction is reached.

Decision trees can be used for both classification and regression. Algorithms may use entropy and information gain to select the features used for splitting.

(iv) Entropy

Entropy measures the uncertainty or impurity of a dataset.

The formula is:

[
H(S)=-\sum_{i=1}^{n}p_i\log_2p_i
]

A high entropy value indicates that observations are distributed among different classes in a relatively uncertain way. A low entropy value indicates that one class dominates.

For binary classification:

- If all observations belong to one class, entropy is 0.
- If both classes occur equally often, entropy is 1 bit.

Importance: Entropy helps decision tree algorithms identify useful features for separating classes.

(v) Information gain

Information gain measures the reduction in entropy achieved by splitting a dataset according to a particular feature.

It is calculated using:

[
\boxed{IG(S,A)=H(S)-\sum_{v\in Values(A)}
\frac{|S_v|}{|S|}H(S_v)}
]

Where:

- IG(S,A) = information gain from feature A.
- H(S) = entropy before splitting.
- S_v = subset containing observations with value v of feature A.
- |S_v|/|S| = proportion of observations in that subset.
- H(S_v) = entropy of that subset.

Example: Suppose a dataset has an initial entropy of 1 bit. Splitting the dataset produces subsets whose weighted average entropy is 0.3 bits.

Then:

[
IG=1-0.3
]

[
\boxed{IG=0.7\text{ bits}}
]

The split provides 0.7 bits of information.

Importance: In a decision tree, a feature with greater information gain is generally preferred because it produces a larger reduction in uncertainty.

---

QUESTION 2

2(a). Express the propositions in natural English and use truth tables to determine their satisfiability.

Let:

- p: I bought a lottery ticket.
- q: I won the jackpot.

(i) \neg p\rightarrow\neg q

Natural English translation:

"If I did not buy a lottery ticket, then I did not win the jackpot."

Here:

- \neg p means I did not buy a lottery ticket.
- \neg q means I did not win the jackpot.
- \rightarrow represents implication.

(ii) \neg p\lor(p\land q)

Natural English translation:

"Either I did not buy a lottery ticket, or I bought a lottery ticket and won the jackpot."

Here:

- \neg means NOT.
- \lor means OR.
- \land means AND.

Truth table

To evaluate both statements, we consider every possible combination of truth values for p and q.

p| q| \neg p| \neg q| \neg p\rightarrow\neg q| \neg p\lor(p\land q)
T| T| F| F| T| T
T| F| F| T| T| F
F| T| T| F| F| T
F| F| T| T| T| T

Explanation of satisfiability:

A logical statement is satisfiable if there is at least one possible assignment of truth values that makes the statement true.

For statement (i):

[
\neg p\rightarrow\neg q
]

The statement is true in three of the four possible cases.

Therefore, it is satisfiable.

For statement (ii):

[
\neg p\lor(p\land q)
]

The statement is also true in three of the four possible cases.

Therefore, it is satisfiable.

Conclusion

Both logical statements are satisfiable because each has at least one true result in its truth table.

Neither statement is a tautology because each has at least one false result.

---

2(b). Write the statements in predicate logic form and explain the logical relations.

Let:

- L(x,y): Person x loves person y.
- H(x,y): Person x is happy with person y.
- m: Mark.
- j: Jessica.

(i) Mark loves Jessica; then he is happy with her.

The statement means that if Mark loves Jessica, then he is happy with her.

Its logical expression is:

$
\boxed{L(m,j)\rightarrow H(m,j)}
$

(ii) Mark loves Jessica and he is happy with her.

The logical expression is:

[
\boxed{L(m,j)\land H(m,j)}
]

The statement is true only when both component statements are true.

(iii) Mark loves Jessica or he is happy with her.

The logical expression is:

[
\boxed{L(m,j)\lor H(m,j)}
]

Under the standard inclusive interpretation of OR, the statement is true when at least one of the component statements is true.

Logical relations

Let p and q represent any two propositions.

(i) Conjunctive relation

Conjunction combines two propositions using AND.

[
\boxed{p\land q}
]

The result is true only when both p and q are true.

Example: Mark studies AND Mark passes the examination.

(ii) Disjunctive relation

Disjunction combines two propositions using OR.

[
\boxed{p\lor q}
]

The result is true when at least one proposition is true.

Example: Mark studies OR Mark passes the examination.

(iii) Implication

Implication expresses a relationship in which one proposition implies another.

[
\boxed{p\rightarrow q}
]

It is read as "If p, then q."

An implication is false only when p is true and q is false.

Example: If Mark studies, then Mark passes the examination.

(iv) Conditional relation

A conditional statement expresses a condition and its consequence.

[
\boxed{\text{If }p\text{, then }q}
]

Its symbolic representation is:

[
\boxed{p\rightarrow q}
]

Example: If it rains, then the ground becomes wet.

In standard propositional logic, a conditional statement and a material implication use the same connective, \rightarrow.

---

2(c). State Modus Ponens and Modus Tollens, giving an example of each.

(i) Modus Ponens

Modus Ponens is a deductive reasoning rule that allows us to conclude q when p implies q and p is known to be true.

Its logical form is:

[
p\rightarrow q
]

[
p
]

[
\therefore q
]

Example:

Premise 1: If it rains, the ground becomes wet.

Premise 2: It is raining.

Conclusion: Therefore, the ground becomes wet.

Symbolically:

[
R\rightarrow W
]

[
R
]

[
\therefore W
]

(ii) Modus Tollens

Modus Tollens is a deductive reasoning rule that allows us to conclude \neg p when p implies q and q is known to be false.

Its logical form is:

[
p\rightarrow q
]

[
\neg q
]

[
\therefore\neg p
]

Example:

Premise 1: If a number is divisible by 4, it is even.

Premise 2: The number is not even.

Conclusion: Therefore, the number is not divisible by 4.

Symbolically:

[
D\rightarrow E
]

[
\neg E
]

[
\therefore\neg D
]

Difference: Modus Ponens affirms the antecedent to establish the consequent, whereas Modus Tollens denies the consequent to establish the negation of the antecedent.

---

QUESTION 3

3(a). Differentiate between an intelligent agent and a rational agent. Explain the functions of the sensors and effectors of an agent.

Intelligent agent

An intelligent agent is an entity that perceives its environment, processes information, and performs actions to achieve objectives. Depending on its design, it may also learn from experience and adapt to changes in its environment.

Example: A chess-playing computer that analyses the board and chooses moves.

Rational agent

A rational agent is an agent that selects the action expected to maximize its performance measure, given its percept history, available knowledge, and possible actions.

Rationality means choosing the most appropriate action based on the information available, rather than necessarily achieving a perfect result.

Example: A self-driving car that chooses a safe route based on traffic conditions, road obstacles, and its destination.

Difference between an intelligent agent and a rational agent

An intelligent agent is a broad concept describing an agent that perceives, reasons, acts, or learns. A rational agent is specifically concerned with selecting actions expected to produce the best outcome according to a defined performance measure.

The concepts overlap: an intelligent agent can be designed to act rationally, and a rational agent need not use sophisticated learning techniques.

Functions of sensors

Sensors are components that allow an agent to perceive its environment and obtain information about its current state.

Functions include:

1. Detecting changes in the environment.
2. Collecting information about objects, conditions, and events.
3. Providing percepts that help the agent decide what action to take.

Examples:

- A camera enables a robot to detect objects.
- A temperature sensor measures environmental temperature.
- A keyboard provides input to a computer system.

Functions of effectors

Effectors are components that enable an agent to act upon its environment. They are also commonly described as actuators or action-producing mechanisms.

Functions include:

1. Executing decisions made by the agent.
2. Producing changes in the environment.
3. Carrying out actions intended to achieve the agent's objectives.

Examples:

- A robot uses motors to move its arms.
- A self-driving car uses steering and braking mechanisms to control its movement.
- A computer agent may use output devices or software commands to perform actions.

Summary: Sensors enable an agent to perceive its environment, while effectors enable it to act upon that environment.

---

3(b). Define data mining, explain data preparation and modelling, and distinguish between classification and clustering.

Meaning of data mining

Data mining is the process of discovering useful patterns, relationships, trends, and knowledge from large amounts of data using statistical methods, machine learning, and other computational techniques.

It helps organizations make predictions, identify relationships, discover unusual events, and support decision-making.

Examples of data mining include:

- Finding products frequently purchased together.
- Detecting fraudulent financial transactions.
- Predicting customer behaviour.
- Identifying patterns in medical data.

Data preparation

Data preparation is the process of collecting, cleaning, organizing, and transforming raw data into a suitable form for analysis and modelling.

It is important because inaccurate, incomplete, or inconsistent data can produce misleading results.

Main activities include:

1. Data collection: Obtaining information from databases, files, surveys, or other sources.
2. Data cleaning: Identifying and handling missing values, duplicate records, errors, and inconsistent entries.
3. Data integration: Combining data from different sources.
4. Data transformation: Converting data into an appropriate format, such as normalizing numerical values or encoding categorical values.
5. Feature selection: Choosing relevant variables for the task.

Example: Before predicting student performance, a researcher may correct invalid examination scores, handle missing attendance records, and organize the data into a consistent format.

Modelling

Modelling is the process of selecting and applying an appropriate statistical or machine learning algorithm to prepared data to identify patterns or make predictions.

Main activities include:

1. Selecting a suitable algorithm.
2. Dividing the data into training and testing sets where appropriate.
3. Training the model using the available data.
4. Adjusting model parameters to improve performance.
5. Evaluating the model using suitable performance measures.

Example: A researcher may train a decision tree using students' attendance, assignment scores, and study hours to predict whether a student will pass an examination.

Difference between classification and clustering

Classification

Classification is a supervised machine learning technique that assigns observations to predefined classes using examples with known labels.

Example: Classifying an email as spam or not spam.

The model learns from labelled training data and then predicts the class of new observations.

Clustering

Clustering is an unsupervised machine learning technique that groups similar observations together without requiring predefined class labels.

Example: Grouping customers into different segments according to their purchasing behaviour.

The algorithm attempts to discover natural groupings in the data.

Comparison table

Feature| Classification| Clustering
Learning type| Supervised| Unsupervised
Training labels| Required| Not required
Main objective| Predict predefined classes| Discover natural groups
Output| Class labels| Clusters or groups
Example| Spam detection| Customer segmentation

Conclusion: Classification predicts known categories using labelled data, while clustering discovers groups of similar observations in unlabelled data.

---

QUESTION 4

4(a). Explain forward chaining and backward chaining using the given facts and rule. Show the process step by step.

Given facts:

[
BrotherOf(John,James)
]

[
FatherOf(James,Trevor)
]

Given rule:

[
BrotherOf(x,y)\land FatherOf(y,z)
\rightarrow UncleOf(x,z)
]

The rule means that if x is the brother of y, and y is the father of z, then x is the uncle of z.

(i) Forward chaining

Forward chaining is a data-driven reasoning technique that starts with known facts and repeatedly applies inference rules to derive new facts until a conclusion is reached or no further rules can be applied.

Step 1: Identify the available facts.

We know:

[
BrotherOf(John,James)
]

and

[
FatherOf(James,Trevor)
]

Step 2: Identify the rule.

The rule is:

[
BrotherOf(x,y)\land FatherOf(y,z)
\rightarrow UncleOf(x,z)
]

For the rule to apply, both conditions must be true.

Step 3: Match the variables with the known facts.

Comparing the first fact with BrotherOf(x,y), we obtain:

[
x=John,\quad y=James
]

Comparing the second fact with FatherOf(y,z), we obtain:

[
y=James,\quad z=Trevor
]

The value of y agrees in both facts, so the rule can be applied.

Step 4: Apply the rule.

Substitute:

[
x=John,\quad y=James,\quad z=Trevor
]

into the conclusion:

[
UncleOf(x,z)
]

This gives:

[
\boxed{UncleOf(John,Trevor)}
]

Step 5: State the conclusion.

Therefore, the system infers that John is Trevor's uncle.

The complete forward-chaining process is:

[
BrotherOf(John,James)
]

[
FatherOf(James,Trevor)
]

[
\Downarrow
]

[
\boxed{UncleOf(John,Trevor)}
]

(ii) Backward chaining

Backward chaining is a goal-driven reasoning technique that starts with a desired conclusion and works backwards to determine whether the available facts and rules support it.

Suppose the goal is to establish:

[
UncleOf(John,Trevor)
]

Step 1: State the goal.

[
Goal=UncleOf(John,Trevor)
]

Step 2: Find a rule that can establish the goal.

The available rule is:

[
BrotherOf(x,y)\land FatherOf(y,z)
\rightarrow UncleOf(x,z)
]

The rule's conclusion matches the goal when:

[
x=John,\quad z=Trevor
]

Therefore, the goal can be established if we can prove both:

[
BrotherOf(John,y)
]

and

[
FatherOf(y,Trevor)
]

Step 3: Establish the first subgoal.

We need to prove:

[
BrotherOf(John,y)
]

The given fact is:

[
BrotherOf(John,James)
]

Therefore:

[
y=James
]

The first subgoal is proved.

Step 4: Establish the second subgoal.

We now need to prove:

[
FatherOf(James,Trevor)
]

This is already a given fact.

Therefore, the second subgoal is also proved.

Step 5: Establish the original goal.

Since both required subgoals are true, the rule can establish the original goal:

[
\boxed{UncleOf(John,Trevor)}
]

Difference between forward chaining and backward chaining

Forward chaining| Backward chaining
Starts with known facts.| Starts with a goal or query.
Applies rules to derive new facts.| Searches for facts and rules that can prove the goal.
Data-driven approach.| Goal-driven approach.
Useful when many possible conclusions must be derived from 