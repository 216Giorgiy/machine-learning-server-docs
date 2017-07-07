--- 
 
# required metadata 
title: "Fast Linear Model – Stochastic Dual Coordinate Ascent" 
description: "A Stochastic Dual Coordinate Ascent (SDCA) optimization trainer" 
keywords: "models, linear, SDCA, stochastic, classification, regression" 
author: "HeidiSteen" 
manager: "" 
ms.date: "" 
ms.topic: "reference" 
ms.prod: "microsoft-r" 
ms.service: "" 
ms.assetid: "" 
 
# optional metadata 
ROBOTS: "" 
audience: "" 
ms.devlang: "" 
ms.reviewer: "" 
ms.suite: "" 
ms.tgt_pltfrm: "" 
ms.technology: "r-server" 
ms.custom: "" 
 
---

## ``rx_fast_linear``: Fast Linear Model – Stochastic Dual Coordinate Ascent


*Applies to:* SQL Server 2017, Machine Learning Services 9.3


### Usage



```
microsoftml.rx_fast_linear(formula: str, data: [<class ‘revoscalepy.datasource.RxDataSource.RxDataSource’>, <class ‘pandas.core.frame.DataFrame’>], method: [‘binary’, ‘regression’] = ‘binary’, loss_function: {‘regression’: [<function squared_loss at 0x000001F5FD740510>], ‘binary’: [<function hinge_loss at 0x000001F5FD740620>, <function log_loss at 0x000001F5FD740268>, <function smoothed_hinge_loss at 0x000001F5FD7406A8>]} = None, l2_weight: float = None, l1_weight: float = None, train_threads: int = None, convergence_tolerance: float = 0.1, max_iterations: int = None, shuffle: bool = True, check_frequency: int = None, normalize: [‘No’, ‘Warn’, ‘Auto’, ‘Yes’] = ‘Auto’, ml_transforms: list = None, ml_transform_vars: list = None, row_selection: str = None, transforms: dict = None, transform_objects: dict = None, transform_function: str = None, transform_variables: list = None, transform_packages: list = None, transform_environment: dict = None, blocks_per_read: int = None, report_progress: int = None, verbose: int = 1, ensemble: dict = None, compute_context: revoscalepy.computecontext.RxComputeContext.RxComputeContext = None)
```




### Description

A Stochastic Dual Coordinate Ascent (SDCA) optimization trainer
for linear binary classification and regression.


### Details

``rx_fast_linear`` is a trainer based on the Stochastic Dual Coordinate Ascent
(SDCA) method, a state-of-the-art optimization technique for convex objective
functions. The algorithm can be scaled for use on large out-of-memory data
sets due to a semi-asynchronized implementation that supports multi-threading.
Convergence is underwritten by periodically enforcing synchronization between
primal and dual updates in a separate thread. Several choices of loss
functions are also provided. The SDCA method combines several of the
best properties and capabilities of logistic regression and SVM algorithms.
For more information on SDCA, see the citations in the reference section.

Traditional optimization algorithms, such as stochastic gradient descent
(SGD), optimize the empirical loss function directly. The SDCA chooses a
different approach that optimizes the dual problem instead. The dual loss
function is parametrized by per-example weights. In each iteration, when a
training example from the training data set is read, the corresponding
example weight is adjusted so that the dual loss function is optimized with
respect to the current example. No learning rate is needed by SDCA to
determine step size as is required by various gradient descent methods.

``rx_fast_linear`` supports binary classification with three types of
loss functions currently: Log loss, hinge loss, and smoothed hinge loss.
Linear regression also supports with squared loss function. Elastic net
regularization can be specified by the ``l2_weight`` and ``l1_weight``
parameters. Note that the ``l2_weight`` has an effect on the rate of
convergence. In general, the larger the ``l2_weight``, the faster
SDCA converges.

Note that ``rx_fast_linear`` is a stochastic and streaming optimization
algorithm. The results depends on the order of the training data. For
reproducible results, it is recommended that one sets ``shuffle`` to
``False`` and ``train_threads`` to ``1``.


### Arguments


##### formula

The formula described in ``rx_formula``.
Interaction terms and ``F()`` are not currently supported in the
.


##### data

A data source object or a character string specifying a
*.xdf* file or a data frame object.


##### method

Specifies the model type with a character string: ``"binary"``
for the default binary classification or ``"regression"`` for linear
regression.


##### loss_function

Specifies the empirical loss function to optimize.
For binary classification, the following choices are available:

* [``log_loss``](log_loss.md): The log-loss. This is the default. 

* ``hinge_loss()``: The SVM hinge loss. Its parameter represents the margin size. 

* ``smooth_hinge_loss()``: The smoothed hinge loss. Its parameter represents the smoothing constant. 

For linear regression, squared loss [``squared_loss``](squared_loss.md) is
currently supported. When this parameter is set to *None*, its
default value depends on the type of learning:

* [``log_loss``](log_loss.md) for binary classification. 

* [``squared_loss``](squared_loss.md) for linear regression. 


##### l1_weight

Specifies the L1 regularization weight. The value must be
either non-negative or *None*. If *None* is specified, the
actual value is automatically computed based on data set. *None*
is the default value.


##### l2_weight

Specifies the L2 regularization weight. The value must be
either non-negative or *None*. If *None* is specified, the
actual value is automatically computed based on data set. *None*
is the default value.


##### train_threads

Specifies how many concurrent threads can be used to run
the algorithm. When this parameter is set to *None*, the number of
threads used is determined based on the number of logical processors
available to the process as well as the sparsity of data. Set it to ``1``
to run the algorithm in a single thread.


##### convergence_tolerance

Specifies the tolerance threshold used as a
convergence criterion. It must be between 0 and 1. The default value is
``0.1``. The algorithm is considered to have converged if the relative
duality gap, which is the ratio between the duality gap and the primal loss,
falls below the specified convergence tolerance.


##### max_iterations

Specifies an upper bound on the number of training
iterations. This parameter must be positive or *None*. If *None*
is specified, the actual value is automatically computed based on data set.
Each iteration requires a complete pass over the training data. Training
terminates after the total number of iterations reaches the specified
upper bound or when the loss function converges, whichever happens earlier.


##### shuffle

Specifies whether to shuffle the training data. Set ``True``
to shuffle the data; ``False`` not to shuffle. The default
value is ``True``. SDCA is a stochastic optimization algorithm.  If
shuffling is turned on, the training data is shuffled on each iteration.


##### check_frequency

The number of iterations after which the loss function
is computed and checked to determine whether it has converged. The value
specified must be a positive integer or *None*. If *None*,
the actual value is automatically computed based on data set. Otherwise,
for example, if ``checkFrequency = 5`` is specified, then the loss
function is computed and convergence is checked every 5 iterations. The
computation of the loss function requires a separate complete pass over
the training data.


##### normalize

Specifies the type of automatic normalization used:

* ``"Auto"``: if normalization is needed, it is performed automatically. This is the default choice. 

* ``"No"``: no normalization is performed. 

* ``"Yes"``: normalization is performed. 

* ``"Warn"``: if normalization is needed, a warning message is displayed, but normalization is not performed. 

Normalization rescales disparate data ranges to a standard scale. Feature
scaling insures the distances between data points are proportional and
enables various optimization methods such as gradient descent to converge
much faster. If normalization is performed, a ``MaxMin`` normalizer is
used. It normalizes values in an interval [a, b] where ``-1 <= a <= 0``
and ``0 <= b <= 1`` and ``b - a = 1``. This normalizer preserves
sparsity by mapping zero to zero.


##### ml_transforms

Specifies a list of microsoftml transforms to be
performed on the data before training or *None* if no transforms are
to be performed. See [``featurize_text``](featurize_text.md),
[``categorical``](categorical.md),
and [``categorical_hash``](categorical_hash.md), for transformations that are supported.
These transformations are performed after any specified Python transformations.
The default avlue is *None*.


##### ml_transform_vars

Specifies a character vector of variable names
to be used in ``ml_transforms`` or *None* if none are to be used.
The default value is *None*.


##### row_selection

NOT SUPPORTED. Specifies the rows (observations) from the data set that
are to be used by the model with the name of a logical variable from the
data set (in quotes) or with a logical expression using variables in the
data set. For example, ``row_selection = "old"`` will only use
observations in which the value of the variable ``old`` is ``True``.
``row_selection = (age > 20) & (age < 65) & (log(income) > 10)`` only uses
observations in which the value of the ``age`` variable is between
20 and 65 and the value of the ``log`` of the ``income`` variable is
greater than 10. The row selection is performed after processing any data
transformations (see the arguments ``transforms`` or
``transform_function``). As with all expressions, ``row_selection`` can be
defined outside of the function call using the ``expression``
function.


##### transforms

NOT SUPPORTED. An expression of the form that represents
the first round of variable transformations. As with
all expressions, ``transforms`` (or ``row_selection``) can be defined
outside of the function call using the ``expression`` function.


##### transform_objects

NOT SUPPORTED. A named list that contains objects that can be
referenced by ``transforms``, ``transform_function``, and
``row_selection``.


##### transform_function

The variable transformation function.


##### transform_variables

A character vector of input data set variables needed for
the transformation function.


##### transform_packages

NOT SUPPORTED. A character vector specifying additional Python packages
(outside of those specified in ``RxOptions.get_option("transform_packages")``) to
be made available and preloaded for use in variable transformation functions.
For exmple, those explicitly defined in  functions via
their ``transforms`` and ``transform_function`` arguments or those defined
implicitly via their ``formula`` or ``row_selection`` arguments.  The
``transform_packages`` argument may also be *None*, indicating that
no packages outside ``RxOptions.get_option("transform_packages")`` are preloaded.


##### transform_environment

NOT SUPPORTED. A user-defined environment to serve as a parent to all
environments developed internally and used for variable data transformation.
If ``transform_environment = None``, a new “hash” environment with parent
``baseenv`` is used instead.


##### blocks_per_read

Specifies the number of blocks to read for each chunk
of data read from the data source.


##### report_progress

An integer value that specifies the level of reporting
on the row processing progress:

* ``0``: no progress is reported. 

* ``1``: the number of processed rows is printed and updated. 

* ``2``: rows processed and timings are reported. 

* ``3``: rows processed and all timings are reported. 


##### verbose

An integer value that specifies the amount of output wanted.
If ``0``, no verbose output is printed during calculations. Integer
values from ``1`` to ``4`` provide increasing amounts of information.


##### compute_context

Sets the context in which computations are executed,
specified with a valid ``RxComputeContext``.
Currently local and ``RxInSqlServer`` compute contexts
are supported.


##### ensemble

NOT SUPPORTED. Control parameters for ensembling.


### Returns

A [``FastLinear``](learners_object.md) object with the trained model.


### Note

This algorithm is multi-threaded and will not attempt to load the entire dataset into memory.


### See also

[``hinge_loss``](hinge_loss.md),
[``log_loss``](log_loss.md),
[``smoothed_hinge_loss``](smoothed_hinge_loss.md),
[``squared_loss``](squared_loss.md),
[``rx_predict``](rx_predict.md)


### References

[Scaling Up Stochastic Dual Coordinate Ascent](http://research.microsoft.com/en-us/um/people/mbilenko/papers/15-sasdca.pdf.md)

[Stochastic Dual Coordinate Ascent Methods for Regularized Loss Minimization](http://www.jmlr.org/papers/volume14/shalev-shwartz13a/shalev-shwartz13a.pdf.md)


### Example



```
'''
Binary Classification.
'''
import numpy
import pandas
from microsoftml import rx_fast_linear, rx_predict
from revoscalepy.etl.RxDataStep import rx_data_step
from microsoftml.datasets.datasets import infert
import sklearn
if sklearn.__version__ < "0.18":
    from sklearn.cross_validation import train_test_split
else:
    from sklearn.model_selection import train_test_split

infertdf = infert.as_df()
infertdf["isCase"] = infertdf.case == 1
data_train, data_test, y_train, y_test = train_test_split(infertdf, infertdf.isCase)

forest_model = rx_fast_linear(
    formula=" isCase ~ age + parity + education + spontaneous + induced ",
    data=data_train)
    
# RuntimeError: The type (RxTextData) for file is not supported.
score_ds = rx_predict(forest_model, data=data_test,
                     extra_vars_to_write=["isCase", "Score"])
                     
# Print the first five rows
print(rx_data_step(score_ds, number_rows_read=5))
```


Output:



```
Automatically adding a MinMax normalization transform, use 'norm=Warn' or 'norm=No' to turn this behavior off.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Using 2 threads to train.
Automatically choosing a check frequency of 2.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Auto-tuning parameters: maxIterations = 8064.
Auto-tuning parameters: L2 = 2.666837E-05.
Auto-tuning parameters: L1Threshold (L1/L2) = 0.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: Rows Read: 18866, Read Time: , Read Time: 00, Transform Time: 00
Rows Read: Rows Read: 18866, Read Time: , Read Time: 00, Transform Time: 00
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: Rows Read: 1186, Read Time: , Read Time: 0.0010.001, Transform Time: , Transform Time: 00
Beginning processing data.
Beginning processing data.
Rows Read: Rows Read: 1186, Read Time: , Read Time: 0.0010.001, Transform Time: , Transform Time: 00
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: Rows Read: 18866, Read Time: Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: Rows Read: 18866, Read Time: Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0.001
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 11886, Read Time:  Transform Ti0.001, Transform Time: 86, Read Time: 0.000
Rows Read: 11886, Read Time:  Transform Ti0.001, Transform Time: 86, Read Time: 0.000
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: Rows Read: 11866, Read Time: , Read Time: 00, Transform Time: , Transform Time: 00
Beginning processing data.
Rows Read: Rows Read: 11866, Read Time: , Read Time: 00, Transform Time: , Transform Time: 00
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 118686, Read Time: Transform Tim00.001, Transform Time: ad Time: 0.001, Tr00
Beginning processing data.
Rows Read: 118686, Read Time: Transform Tim00.001, Transform Time: ad Time: 0.001, Tr00
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: Rows Read: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: Rows Read: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: Rows Read: 1186, Read Time: , Read Time: 0.0010.001, Transform Time: , Transform Time: 00
Rows Read: Rows Read: 1186, Read Time: , Read Time: 0.0010.001, Transform Time: , Transform Time: 00
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: Rows Read: 0
Rows Read: 186, Read Time: 0.001, Transform Time: Rows Read: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0.001
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: Rows Read: 186, Read Time: 186, Read Time: 0..0101, Transform Time:  0.001, Transform 00
Rows Read: Rows Read: 186, Read Time: 186, Read Time: 0..0101, Transform Time:  0.001, Transform 00
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: Rows Read: 1186, Read Time:  Time: 0
Rows Read: Rows Read: 1186, Read Time:  Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: Rows Read: 186, Read Time: 0.001, Transform Time: 0, Transform Time: 00
Rows Read: 186, Read Time: Rows Read: 186, Read Time: 0.001, Transform Time: 0, Transform Time: 00
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
0
0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
186, Read Time: 186, Read Time: Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: Rows Read: 0, Transform Time: 0186, Read Time: 
Rows Read: 186, Read Time: Rows Read: 0, Transform Time: 0186, Read Time: 
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: Rows Read: 0186, Read Time: 
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: Rows Read: 0186, Read Time: 
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: Rows Read: 0, Transform Time: 186, Read Time: 0
Beginning processing data.
Rows Read: 186, Read Time: Rows Read: 0, Transform Time: 186, Read Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Rows Read: 186, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 186, Read Time: 0, Transform Time: 0
Beginning processing data.
Using best model from iteration 590.
Not training a calibrator because it is not needed.
Elapsed time: 00:00:01.9054775
Elapsed time: 00:00:00.0280732
Beginning processing data.
Rows Read: 62, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Elapsed time: 00:00:00.1037457
Finished writing 62 rows.
Writing completed.
Data will be written to C:\Users\xadupre\AppData\Local\Temp\rre1477613.xdf File will be overwritten if it exists.

Rows Processed: 5 
  isCase PredictedLabel     Score  Probability
0   True          False -0.034090     0.491478
1   True          False -0.093061     0.476752
2   True          False -2.579119     0.070494
3  False          False -3.242319     0.037604
4  False          False -0.194355     0.451564
```



### Example



```
'''
Regression.
'''
import numpy
import pandas
from microsoftml import rx_fast_linear, rx_predict
from revoscalepy.etl.RxDataStep import rx_data_step
from microsoftml.datasets.datasets import attitude

import sklearn
if sklearn.__version__ < "0.18":
    from sklearn.cross_validation import train_test_split
else:
    from sklearn.model_selection import train_test_split

attitudedf = attitude.as_df()
data_train, data_test = train_test_split(attitudedf)

model = rx_fast_linear(
    formula="rating ~ complaints + privileges + learning + raises + critical + advance",
    method="regression",
    data=data_train)
    
# RuntimeError: The type (RxTextData) for file is not supported.
score_ds = rx_predict(model, data=data_test,
                     extra_vars_to_write=["rating"])
                     
# Print the first five rows
print(rx_data_step(score_ds, number_rows_read=5))
```


Output:



```
Automatically adding a MinMax normalization transform, use 'norm=Warn' or 'norm=No' to turn this behavior off.
Beginning processing data.
Rows Read: 22, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Using 2 threads to train.
Automatically choosing a check frequency of 2.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Auto-tuning parameters: maxIterations = 68180.
Auto-tuning parameters: L2 = 0.01.
Auto-tuning parameters: L1Threshold (L1/L2) = 0.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Rows Read: 22, Read Time: 0.001, Transform Time: 0Beginning processing data.

Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0.001, Transform Time: 0
0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: Beginning processing data.
0, Transform Time: 0
Rows Read: 22, Read Time: Beginning processing data.
0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: Beginning processing data.
22, Read Time: 0, Transform Time: 0
Rows Read: 22, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.0, Transfor
22, Read Time: 0, Transform Time: 0
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: Beginning processing data.
22, Read Time: 0, Transform Time: 0
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0.001, Transform Time: 0
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: Rows Read: 0, Transform Time: 22, Read Time: 0
Rows Read: 22, Read Time: Rows Read: 0, Transform Time: 22, Read Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Rows Read: 22, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Rows Read: 22, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
0, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0.001, Transform Time: 0
Beginning processing data.
Beginning processing data.
Beginning processing data.
Rows Read: 22, Read Time: 0, Transform Time: 0
Beginning processing data.
Using best model from iteration 74.
Not training a calibrator because it is not needed.
Elapsed time: 00:00:00.3664557
Elapsed time: 00:00:00.0261831
Beginning processing data.
Rows Read: 8, Read Time: 0, Transform Time: 0
Beginning processing data.
Elapsed time: 00:00:00.1032676
Finished writing 8 rows.
Writing completed.
Data will be written to C:\Users\xadupre\AppData\Local\Temp\rre1477624.xdf File will be overwritten if it exists.

Rows Processed: 5 
   rating      Score
0    64.0  62.099472
1    72.0  82.282715
2    65.0  64.172119
3    82.0  69.057617
4    50.0  64.574013
```

