---
title: What is a TensorFlow Session?
date: 2017-06-12 14:03:10
author: wangduo
cover: /assets/images/VCG21gic13789859.jpg
tags: [TensorFlow, Session]
categories: Deep learning
---

I’ve seen a lot of confusion over the rules of tf.Graph and tf.Session in TensorFlow. It’s simple:

- A graph defines the computation. It doesn’t compute anything, it doesn’t hold any values, it just defines the operations that you specified in your code.
- A session allows to execute graphs or part of graphs. It allocates resources (on one or more machines) for that and holds the actual values of intermediate results and variables.

Let’s look at an example.

### Defining the Graph

We define a graph with a variable and three operations: **variable** always returns the current value of our variable. **initialize** assigns the initial value of 42 to that variable. **assign** assigns the new value of 13 to that variable.

```
graph = tf.Graph()
with graph.as_default():
    variable = tf.Variable(42, name='foo')
    initialize = tf.initialize_all_variables()
    assign = variable.assign(13)
```

*On a side note: TensorFlow creates a default graph for you, so we don’t need the first two lines of the code above. The default graph is also what the sessions in the next section use when not manually specifying a graph.*

### Running Computations in a Session

To run any of the three defined operations, we need to create a session for that graph. The session will also allocate memory to store the current value of the variable.

```
with tf.Session(graph=graph) as sess:
  sess.run(initialize)
  sess.run(assign)
  print(sess.run(variable))
# Output: 13
```

As you can see, the value of our variable is only valid within one session. If we try to query the value afterwards in a second session, TensorFlow will raise an error because the variable is not initialized there.

```
with tf.Session(graph=graph) as sess:
  print(sess.run(variable))
# Error: Attempting to use uninitialized value foo
```

Of course, we can use the graph in more than one session, we just have to initialize the variables again. The values in the new session will be completely independent from the first one:

```
with tf.Session(graph=graph) as sess:
  sess.run(initialize)
  print(sess.run(variable))
# Output: 42
```

Hopefully this short workthrough helped you to better understand tf.Session. Feel free to ask questions in the comments.

From:http://danijar.com/what-is-a-tensorflow-session/
