

.. toctree::
	:maxdepth: 5
	:hidden:

======================
Archived release notes
======================

---------------
sd (14/01/2019)
---------------

**New**: ``Chain.export()`` / ``assign()`` and custom calculations

Expanding on the current ``Chain`` editing features provided via ``cut()``
and ``join()``, it is now possible to calculate additional row and column results using plain ``pandas.dataframe`` methods. Use ``Chain.export()`` to work on a simplified ``Chain.dataframe`` and ``assign()`` to rebuild it properly when finished.

**New**: ``Batch.as_main(keep=True)`` to change ``qp.Batch`` relations

It is now possible to promote an ``.additional`` Batch to a main/regular one. Optionally, the original parent Batch can be erased by setting ``keep=False``. Example:

*Starting from:*

>>> dataset.batches(main=True, add=False)
['batch 2', 'batch 5']

>>> dataset.batches(main=False, add=True)
['batch 4', 'batch 3', 'batch 1']

*We turn* ``batch 3`` *into a normal one:*

>>> b = dataset.get_batch('batch 3')
>>> b.as_main()

>>> dataset.batches(main=True, add=False)
['batch 2', 'batch 5', 'batch 3']

>>> dataset.batches(main=False, add=True)
['batch 4', 'batch 1']

**New**: On-the-fly rebasing via ``Quantity.normalize(on='y', per_cell=False)``

Quantipy's engine will now accept another variable's base for (column) percentage
computations. Furthermore, it is possible to rebase the percentages to the *cell
frequencies of the other variable's cross-tabulation* by setting ``per_cell=True``,
i.e. rebase variables with identical categories to their respective per-category results. The following example shows how ``'A1'`` results are serving as cell bases
for the percentages of ``'A2'``:

>>> l = stack[stack.keys()[0]]['no_filter']['A1']['datasource']
>>> q = qp.Quantity(l)
>>> q.count()
Question        datasource
Values                 All      1       2       3      4       5       6
Question Values
A1       All        6984.0  767.0  1238.0  2126.0  836.0  1012.0  1005.0
         1          1141.0  503.0    78.0   109.0  102.0   155.0   194.0
         2          2716.0  615.0   406.0   499.0  499.0   394.0   303.0
         3          1732.0  603.0    89.0   128.0  101.0   404.0   407.0
         4          5391.0  644.0   798.0  1681.0  655.0   796.0   817.0
         5          4408.0  593.0   177.0  1649.0  321.0   818.0   850.0
         6          3584.0  615.0   834.0   834.0  327.0   507.0   467.0
         7          4250.0  588.0   724.0  1717.0  540.0    55.0   626.0
         8          3729.0  413.0  1014.0   788.0  311.0   539.0   664.0
         9          3575.0  496.0   975.0   270.0  699.0   230.0   905.0
         10         4074.0  582.0   910.0  1148.0  298.0   861.0   275.0
         11         2200.0  446.0   749.0   431.0  177.0   146.0   251.0
         12         5554.0  612.0   987.0  1653.0  551.0   860.0   891.0
         13          544.0   40.0   107.0   232.0   87.0    52.0    26.0

>>> l = stack[stack.keys()[0]]['no_filter']['A2']['datasource']
>>> q = qp.Quantity(l)
>>> q.count()
Question        datasource
Values                 All      1       2       3      4      5      6
Question Values
A2       All        6440.0  727.0  1131.0  1894.0  749.0  960.0  979.0
         1           568.0  306.0    34.0    32.0   48.0   63.0   85.0
         2          1135.0  417.0   107.0    88.0  213.0  175.0  135.0
         3           975.0  426.0    43.0    49.0   49.0  220.0  188.0
         4          2473.0  350.0   267.0   599.0  431.0  404.0  422.0
         5          2013.0  299.0    88.0   573.0  162.0  417.0  474.0
         6          1174.0  342.0   219.0   183.0  127.0  135.0  168.0
         7          1841.0  355.0   161.0   754.0  285.0   21.0  265.0
         8          1740.0  265.0   376.0   327.0  160.0  212.0  400.0
         9          1584.0  181.0   390.0    89.0  398.0   94.0  432.0
         10         1655.0  257.0   356.0   340.0  137.0  443.0  122.0
         11          766.0  201.0   241.0   101.0   76.0   53.0   94.0
         12         2438.0  217.0   528.0   497.0  247.0  459.0  490.0
         13         1532.0   72.0   286.0   685.0  118.0  183.0  188.0

>>> q.normalize(on='A1', per_cell=True)
Question         datasource
Values                  All           1           2           3           4           5           6
Question Values
A2       All      92.210767   94.784876   91.357027   89.087488   89.593301   94.861660   97.412935
         1        49.780894   60.834990   43.589744   29.357798   47.058824   40.645161   43.814433
         2        41.789396   67.804878   26.354680   17.635271   42.685371   44.416244   44.554455
         3        56.293303   70.646766   48.314607   38.281250   48.514851   54.455446   46.191646
         4        45.872751   54.347826   33.458647   35.633551   65.801527   50.753769   51.652387
         5        45.666969   50.421585   49.717514   34.748332   50.467290   50.977995   55.764706
         6        32.756696   55.609756   26.258993   21.942446   38.837920   26.627219   35.974304
         7        43.317647   60.374150   22.237569   43.913803   52.777778   38.181818   42.332268
         8        46.661303   64.164649   37.080868   41.497462   51.446945   39.332096   60.240964
         9        44.307692   36.491935   40.000000   32.962963   56.938484   40.869565   47.734807
         10       40.623466   44.158076   39.120879   29.616725   45.973154   51.451800   44.363636
         11       34.818182   45.067265   32.176235   23.433875   42.937853   36.301370   37.450199
         12       43.896291   35.457516   53.495441   30.066546   44.827586   53.372093   54.994388
         13      281.617647  180.000000  267.289720  295.258621  135.632184  351.923077  723.076923




**New**: ``DataSet.missings(name=None)``

This new method returns the missing data definition for the provided variable or
all missing definitions found in the dataset (if ``name`` is omitted).

>>> dataset.missings()
{u'q10': {u'exclude': [6]},
 u'q11': {u'exclude': [977]},
 u'q17': {u'exclude': [977]},
 u'q23_1_new': {u'exclude': [8]},
 u'q25': {u'exclude': [977]},
 u'q32': {u'exclude': [977]},
 u'q38': {u'exclude': [977]},
 u'q39': {u'exclude': [977]},
 u'q48': {u'exclude': [977]},
 u'q5': {u'exclude': [977]},
 u'q9': {u'exclude': [977]}}

**Update**: ``DataSet.batches(main=True, add=False)``

The collection of ``Batch`` sets can be separated by ``main`` and ``add``\itional
ones (see above) to make analyzing Batch setups and relations easier. The default is still to return all Batch names.

**Bugfix**: Stack, ``other_source`` statistics failing for delimited sets

A bug that prevented ``other_source`` statistics being computed on delimited set type variables has been resolved by adjusting the underlying data type checking mechanic.

---------------
sd (26/10/2018)
---------------

**New**: Filter variables in DataSet and Batch

To avoid complex logics stored in the background and resulting problem with
json serializing, the filter concept in DataSet and Batch has changed.

Now actual variables are added to the data and meta, which have the property
``recoded_filter``. The values of depend on the included logic, and all logics
summarized in the value ``0: keep``. Because of the an easy logic can be used
at several places in qp: ``{'filter_var': 0}``

*DataSet methods*

All included filters of a Dataset can be shown running ``dataset.filters()``.

A filter variable can be easily created::

    dataset.add_filter_var(name, logic, overwrite=False)

*   ``name`` is the name of the new filter-variable.
*   ``logic`` should be (a list of) dictionaries in form of:

    >>> {
    ...     'label': 'reason',
    ...     'logic': {var: keys} / intersection/ ....
    ... }

    or stings (var_name), which are automatically transformed into the following dict

    >>> {
    ...     'label': 'var_name not empty',
    ...     'logic': {var_name: not_count(0)}
    ... }

    If a list is provided, each item results in an own value of the filter
    variable.

An existing filter variable can also be extended::

    dataset.extend_filter_var(name, logic, extend_as=None)

*   ``name`` is the name of the existing filter-variable.
*   ``logic`` should be the same as above, then additional categories are added
    to the filter and the 0 value is recalculated.
*   ``extend_as`` determines if a new filter var is created or the initial
    variable is modified.
    If ``extend_as=None`` the variable is modified inplace. Otherwise
    ``extend_as`` is used as suffix for the new filter variable.

Known methods like::

    .copy()
    .drop()
    .rename()

can be applied on filter-variables, all others are not valid!

*Batch methods*

``Batch.add_filter(filter_name, filter_logic=None, overwrite=False)``

A filter can still be added to a batch, by adding a ``filter_logic``, but also
it's possible to add only the ``filter_name`` of an existing filter variable.
If ``filter_name`` is an existing filter-variable, a ``filter_logic`` is provided
and ``overwrite`` is turned off, the scripts will return an error.

``Batch.remove_filter()``

This method only removes filters from the Batch definitions, the created
filter-variables still exist in the belonging DataSet object.

Batch methods that use filters::

    .extend_filter()
    .add_y_on_y()
    .add_open_ends()

create new extended filter variables if the used filter differs from the
batch global filter. So it's recommended to add the global filter first, it's
taken over automatically for the mentioned methods.

""""

**New**: Summarizing and rearranging  ``qp.Chain`` elements via ``ChainManager``

* ``cut(values, ci=None, base=False, tests=False)``

* ``join(title='Summary')``

It is now possible to summarize ``View`` aggregation results from existing ``Chain``
items  by restructuring and editing them via their ``ChainManager`` methods. The
general idea behind building a summary ``Chain`` is to unify a set of results into
items  by restructuring and editing them via their ``ChainManager`` methods. The
general idea behind building a summary Chain is to unify a set of results into
one cohesive representation to offer an easy way to look at certain key figures
of interest in comparison to each other. To achieve this, the ``ChainManager`` class
has gained the new ``cut()`` and ``join()`` methods. Summaries are built post-
aggregation and therefore rely on what has been defined (via the ``qp.Batch``
class) and computed (via the ``qp.Stack`` methods) at previous stages.

The intended way of working with this new feature can be outlined as

  1. ``reorder()``
  2. ``cut()``
  3. ``join()``
  4. ``insert()``

In more detail:

*A) Grouping the results for the summary*

Both methods will operate on the *entire set* of ``Chains`` collected in a
``ChainManager``, so building a summary ``Chain`` will normally start with
restricting a copy of an existing ``ChainManager`` to the question variables
that you’re interested in. This can be done via ``clone()`` with
``reorder(..., inplace=True)`` or by assigning back the new instance from
``reorder(..., inplace=False)``.

*B) Selecting View results via* ``cut()``

This method lets you target the kind of results (nets, means, NPS scores,
only the frequencies, etc.) from a given ``qp.Chain.dataframe``. Elements must
be targeted by their underlying regular index values, e.g. ``'net_1'``, ``'net_2'``,
``'mean'``, ``1``, ``'calc'``, ... . Use the ``base`` and ``tests`` parameters
to also carry over the matching base rows and/or significance testing results.
The ``ci`` parameter additionally allows targeting only the ``'counts'`` or
``'c%'`` results if cell items are grouped together.

*C) Unifying the individual results with* ``join()``

Merging all new results into one, the ``join()`` method concatenates vertically
and relabels the x-axis to separate all variable results by their matching
metadata ``text`` that has also been applied while creating the regular set of
and relabels the x-axis to separate all variable results by their matching
metadata ``text`` that has has also been applied while creating the regular set of
``Chain`` items. The new summary can then also be inserted back into its
originating ``ChainManager`` with ``insert()`` if desired.

""""

**Update**: ``Batch.add_variables(varlist)``

A ``qp.Batch`` can now carry a collection of variables that is **explicitly not**
directed towards any table-like builds. Variables from ``varlist`` will solely
be used in non-aggregation based, data transformation and export oriented
applications. To make this distinction more visible in the API, ``add_x()`` and
``add_y()`` have been renamed to ``add_downbreak()`` and ``add_crossbreak()``.
Users are warned and advised to switch to the new method versions via a
``DeprecationWarning``. In a future version of the library ``add_x()`` and
``add_y()`` will be removed.

""""

**Update**: ``Batch.copy()`` -> ``Batch.clone()``

Since ``qp.Batch`` is a subclass of ``qp.DataSet``, the ``copy()`` method is renamed into
``Batch.clone()``.

---------------
sd (01/10/2018)
---------------

**New**: "rewrite" of Rules module (affecting sorting):

**sorting "normal" columns**:

* ``sort_on`` always '@'
* ``fix`` any categories
* ``sort_by_weight`` default is unweighted (None), but each weight (included
in data) can be used

If sort_by_weight and the view-weight differ, a warning is shown.

**sorting "expanded net" columns**:

* ``sort_on`` always '@'
* ``fix`` any categories
* sorting ``within`` or ``between`` net groups is available
* ``sort_by_weight``: as default the weight of the first found
expanded-net-view is taken. Only weights of aggregated net-views are possible

**sorting "array summaries"**:

* ``sort_on`` can be any desc ('median', 'stddev', 'sem', 'max', 'min',
'mean', 'upper_q', 'lower_q') or nets ('net_1', 'net_2', .... enumerated
by the net_def)
* ``sort_by_weight``: as default the weight of the first found desc/net-view
is taken. Only weights of aggregated desc/net-views are possible
* ``sort_on`` can also be any category, here each weight can be used to sort_on

""""

**New**: ``DataSet.min_value_count()``

A new wrapper for ``DataSet.hiding()`` is included. All values are hidden,
that have less counts than the included number ``min``.
The used data can be weighted or filtered using the parameters ``weight`` and
``condition``.

Usage as Batch method:
``Batch.min_value_count()`` without the parameters ``weight`` and
``condition`` automatically grabs ``Batch.weights[0]`` and ``Batch.filter``
to calculate low value counts.

""""

**New**: Prevent weak duplicated in data

As Python is case sensitive it is possible to have two or more variables with
the same name, but in lower- and uppercases. Most other software do not support
that, so a warning is shown if a weak dupe is created. Additionally
``Dataset.write_dimensions()`` performs auto-renaming is weak dupes are detected.

""""

**New**: Prevent single-cat delimited sets

``DataSet.add_meta(..., qtype='delimited set', categories=[...], ...)``
automatically switches ``qtype`` to single if only one category is defined.
``DataSet.convert(name, 'single')`` allows conversion from ``delimited set`` to
``single`` if the variable has only one category.
``DataSet.repair()`` and ``DataSt.remove_values()`` convert delimited sets
automatically to singles if only one category is included.

""""

**Update**: merge warnings + merging delimites sets

Warnings in ``hmerge()`` and ``vmerge()`` are updated. If a column exists in
the left and the right dataset, the type is compared. Some type inconsistencies
are allowed, but return a warning, while others end up in a raise.

delimited sets in ``vmerge()``:

If a column is a delimited set in the left dataset, but a single, int or float
in the right dataset, the data of the right column is converted into a delimited
set.

delimited sets in ``hmerge(...merge_existing=None)``:

For the hmerge a new parameter ``merge_existing`` is included, which can be
``None``, a list of variable-names or ``'all'``.

If delimited sets are included in left and right dataset:

* ``merge_existing=None``: Only meta is adjusted. Data is untouched (left data
is taken).
* ``merge_existing='all'``: Meta and data are merged for all delimited sets,
that are included in both datasets.
* ``merge_existing=[variable-names]``: Meta and data are merged for all
delimited sets, that are listed and included in both datasets.

""""

**Update**: encoding in ``DataSet.get_batch(name)``

The method is not that encoding sensitive anymore. It returns the depending
``Batch``, no matter if ``'...'``, ``u'...'`` or ``'...'.decode('utf8')`` is
included as name.

""""

**Update**: warning in weight engine

Missing codes in the sample are only alerted, if the belonging target is not 0.

""""

**Update**: ``DataSet.to_array(..., variables, ...)``

Duplicated vars in ``variables`` are not allowed anymore, these were causing
problems in the ChainManager class.

""""

**Update**: ``Batch.add_open_ends()``

Method raises an error if no vars are included in ``oe`` and ``break_by``. The
empty dataframe was causing issues in the ChainManager class.

""""

**Update**: ``Batch.extend_x()``

The method automatically checks if the included variables are arrays and adds
them to ``Batch.summaries`` if they are included yet.


---------------
sd (04/06/2018)
---------------


**New**: Additional variable (names) "getter"-like and resolver methods

* ``DataSet.created()``
* ``DataSet.find(str_tags=None, suffixed=False)``
* ``DataSet.names()``
* ``DataSet.resolve_name()``

A bunch of new methods enhancing the options of finding and testing for variable
names have been added. ``created()`` will list all variables that have been added
to a dataset using core functions, i.e. ``add_meta()`` and ``derive()``, resp.
all helper methods that use them internally (as ``band()`` or ``categorize()`` do
for instance).

The ``find()`` method is returning all variable names that contain any of the
provided substrings in ``str_tags``. To only consider names that end with these
strings, set ``suffixed=True``. If no ``str_tags`` are passed, the method will
use a default list of tags including ``['_rc', '_net', ' (categories', ' (NET', '_rec']``.

Sometimes a dataset might contain "semi-duplicated" names, variables that differ
in respect to case sensitivity but have otherwise identical names. Calling
``names()`` will report such cases in a ``pd.DataFrame`` that lists all name
variants under the respective ``str.lower()`` version. If no semi-duplicates
are found, ``names()`` will simply return ``DataSet.variables()``.

Lastly, ``resolve_name()`` can be used to return the "proper", existing representation(s) of a given variable name's spelling.

""""

**New**: ``Batch.remove()``

Not needed batches can be removed from ``meta``, so they are not aggregated
anymore.

""""

**New**: ``Batch.rename(new_name)``

Sometimes standard batches have long/ complex names. They can now be changed
into a custom name. Please take into account, that for most hubs the name of
omnibus batches should look like 'client ~ topic'.

""""

**Update**: Handling verbatims in ``qp.Batch``

Instead of holding the well prepared open-end dataframe in ``batch.verbatims``,
the attribute is now filled by ``batch.add_open_ends()`` with instructions to
create the open-end dataframe. It is easier to to modify/ overwrite existing
verbatims. Therefore also a new parameter is included ``overwrite=True``.

""""

**Update**: ``Batch.copy(..., b_filter=None, as_addition=False)``

It is now possible to define an additional filter for a copied batch and also
to set it as addition to the master batch.

""""

**Update**: Regrouping the variable list using ``DataSet.order(..., regroup=True)``

A new parameter called ``regroup`` will instruct reordering all newly created
variables into their logical position of the dataset's main variable order, i.e.
attempting to place *derived* variables after the *originating* ones.

""""

**Bugfix**: ``add_meta()`` and duplicated categorical ``values`` codes

Providing duplicated numerical codes while attempting to create new metadata
using ``add_meta()`` will now correctly raise a ``ValueError`` to prevent
corrupting the ``DataSet``.

>>> cats = [(1, 'A'), (2, 'B'), (3, 'C'), (3, 'D'), (2, 'AA')]
>>> dataset.add_meta('test_var', 'single', 'test label', cats)
ValueError: Cannot resolve category definition due to code duplicates: [2, 3]



---------------
sd (04/04/2018)
---------------


**New**: Emptiness handlers in ``DataSet`` and ``Batch`` classes

* ``DataSet.empty(name, condition=None)``
* ``DataSet.empty_items(name, condition=None, by_name=True)``
* ``DataSet.hide_empty_items(condition=None, arrays=None)``
* ``Batch.hide_empty(xks=True, summaries=True)``

``empty()`` is used to test if regular variables are completely empty,
``empty_items()`` checks the same for the items of an array mask definition.
Both can be run on lists of variables. If a single variable is tested, the former
returns simply boolean, the latter will list all empty items. If lists are checked,
``empty()`` returns the sublist of empty variables, ``empty_items()`` is mapping
the list of empty items per array name. The ``condition`` parameter of these
methods takes a ``Quantipy logic`` expression to restrict the test to a subset
of the data, i.e. to check if variables will be empty if the dataset is filtered
a certain way. A very simple example:

>>> dataset.add_meta('test_var', 'int', 'Variable is empty')
>>> dataset.empty('test_var')
True

>>> dataset[dataset.take({'gender': 1}), 'test_var'] = 1
>>> dataset.empty('test_var')
False

>>> dataset.empty('test_var', {'gender': 2})
True


The ``DataSet`` method ``hide_empty_items()`` uses the emptiness tests to
automatically apply a **hiding rule** on all empty items found in the dataset.
To restrict this to specific arrays only, their names can be provided via the
``arrays`` argument. ``Batch.hide_empty()`` takes into account the current
``Batch.filter`` setup and by drops/hides *all* relevant empty variables from the
``xks`` list and summary aggregations by default. Summaries that would end up without valid
items because of this are automatically removed from the ``summaries`` collection
and the user is warned.

""""

**New**: ``qp.set_option('fast_stack_filters', True)``

A new option to enable a more efficient test for already existing filters
inside the ``qp.Stack`` object has been added. Set the ``'fast_stack_filters'``
option to ``True`` to use it, the default is ``False`` to ensure compatibility
in different versions of production DP template workspaces.

""""

**Update**: ``Stack.add_stats(..., factor_labels=True, ...)``

The parameter ``factor_labels`` is now also able to take the string ``'()'``,
then factors are written in the normal brackets next to the label (instead
of ``[]``).

In the new version factor_labels are also just added if there are none included
before, except new scales are used.

""""

**Bugfix**: ``DataSet`` ``np.NaN`` insertion to ``delimited_set`` variables

``np.NaN`` was incorrectly transformed when inserted into ``delimited_set``
before, leading to either ``numpy`` type conflicts or type casting exceptions.
This is now fixed.



---------------
sd (27/02/2018)
---------------

**New**: ``DataSet._dimensions_suffix``

``DataSet`` has a new attribute ``_dimensions_suffix``, which is used as mask
suffix while running ``DataSet.dimensionize()``. The default is ``_grid`` and
it can be modified with ``DataSet.set_dim_suffix()``.

""""

**Update**: ``Stack._get_chain()`` (old chain)

The method is speeded-up. If a filter is already included in the Stack, it is
not calculated from scratch anymore. Additionally the method has a new parameter
``described``, which takes a describing dataframe of the Stack, so it no longer
needs to be calculated in each loop.

""""
**Update**: ``Stack.add_nets()`` (recoded ``Views``)

Nets that are applied on array variables will now also create a new recoded
array that reflects the net definitions if ``recoded`` is used. The
method has been creating only the item versions before.

""""

**Update**: ``Stack.add_stats()``

The method will now create a new metadata property called ``'factor'`` for each
variable it is applied on. You can only have one factor assigned to one
categorical value, so for multiple statistic definitions (exclusions, etc.)
it will get overwritten.

""""

**Update**: ``DataSet.from_batch()`` (``additions`` parameter)

The ``additions`` parameter has been updated to also be able to create recoded
variables from existing "additional" Batches that are attached to a parent one.
Filter variables will get the new meta ``'properties'`` tag ``'recoded_filter'``
and only have one category (``1``, ``'active'``). They are named simply
``'filter_1'``, ``'filter_2'`` and so on. The new possible values of the
parameters are now:

  * ``None``: ``as_addition()``-Batches are not considered.
  * ``'variables'``: Only cross- and downbreak variables are considered.
  * ``'filters'``: Only filters are recoded.
  * ``'full'``: ``'variables'`` + ``'filters'``

""""

**Bugfix**: ``ViewManager._request_views()``

Cumulative sums are only requested if they are included in the belonging
``Stack``. Additionally the correct related sig-tests are now taken for
cumulative sums.

---------------
sd (12/01/2018)
---------------

**New**: ``Audit``

``Audit`` is a new class which takes ``DataSet`` instances, compares and aligns
them.

The class compares/ reports/ aligns the following aspects:

  * datasets are valid (``DataSet.validate()``)
  * mismatches (variables are not included in all datasets)
  * different types (variables are in more than one dataset, but have different types)
  * labels (variables are in more than one dataset, but have different labels for the same text_key)
  * value codes (variables are in more than one dataset, but have different value codes)
  * value texts (variables are in more than one dataset, but have different value texts)
  * array items (arrays are in more than one dataset, but have different items)
  * item labels (arrays are in more than one dataset, but their items have different labels)

This is the first draft of the class, so it will need some testing and probably
adjustments.

""""

**New**: ``DataSet.reorder_items(name, new_order)``

The new method reorders the items of the included array. The ints in the
``new_order`` list match up to the number of the items
(``DataSet.item_no('item_name')``), not to the position.

""""

**New**: ``DataSet.valid_tks``, Arabic

Arabic (``ar-AR``) is included as default valid text-key.

""""

**New**: ``DataSet.extend_items(name, ext_items, text_key=None)``

The new method extends the items of an existing array.

""""

**Update**: ``DataSet.set_missings()``

The method is now limited to ``DataSet``, ``Batch`` does not inherit it.

""""

**Update**: ``DataSet``

The whole class is reordered and cleaned up. Some new deprecation warnings
will appear.

""""

**Update**: ``DataSet.add_meta()`` / ``DataSet.derive()``

Both methods will now raise a ``ValueError: Duplicated codes provided. Value codes must be unique!``
if categorical ``values`` definitions try to apply duplicated codes.

""""

---------------
sd (18/12/2017)
---------------


**New**: ``Batch.remove_filter()``

Removes all defined (global + extended) filters from a Batch instance.

""""

**Update**: ``Batch.add_filter()``

It's now possible to extend the global filter of a Batch instance. These options
are possible.

Add first filter::

  >>> batch.filter, batch.filter_names
  'no_filter', ['no_filter']
  >>> batch.add_filter('filter1', logic1)
  >>> batch.filter, batch.filter_names
  {'filter1': logic1}, ['filter1']

Extend filter::

  >>> batch.filter, batch.filter_names
  {'filter1': logic}, ['filter1']
  >>> batch.add_filter('filter2', logic2)
  >>> batch.filter, batch.filter_names
  {'filter1' + 'filter2': intersection([logic1, logic2])}, ['filter1' + 'filter2']

Replace filter::

  >>> batch.filter, batch.filter_names
  {'filter1': logic}, ['filter1']
  >>> batch.add_filter('filter1', logic2)
  >>> batch.filter, batch.filter_names
  {'filter1': logic2}, ['filter1']

""""

**Update**: ``Stack.add_stats(..., recode)``

The new parameter ``recode`` defines if a new numerical variable is created which
satisfies the stat definitions.

""""

**Update**: ``DataSet.populate()``

A progress tracker is added to this method.

""""

**Bugfix**: ``Batch.add_open_ends()``

``=`` is removed from all responsess in the included variables, as it causes
errors in the Excel-Painter.

""""

**Bugfix**: ``Batch.extend_x()`` and ``Batch.extend_y()``

Check if included variables exist and unroll included masks.

""""

**Bugfix**: ``Stack.add_nets(..., calc)``

If the operator in calc is ``div``/ ``/``, the calculation is now performed
correctly.

""""

---------------
sd (28/11/2017)
---------------

**New** ``DataSet.from_batch()``

Creates a new ``DataSet`` instance out of ``Batch`` definitions (xks, yks,
filter, weight, language, additions, edits).

""""

**New**: ``Batch.add_total()``

Defines if total column ``@`` should be included in the downbreaks (yks).

""""

**New**: ``Batch.set_unwgt_counts()``

If cellitems are ``cp`` and a weight is provided, it is possible to request
unweighted count views (percentages are still weighted).

""""

**Update**: ``Batch.add_y_on_y(name, y_filter=None, main_filter='extend')``

Multiple ``y_on_y`` aggregations can now be added to a ``Batch`` instance
and each can have an own filter. The y_on_y-filter can ``extend`` or ``replace``
the main_filter of the ``Batch``.

""""

**Update**: ``Stack.add_nets(..., recode)``

The new parameter ``recode`` defines if a new variable is created which
satisfies the net definitions. Different options for ``recode`` are:

   * ``'extend_codes'``: The new variable contains all codes of the original
     variable and all nets as new categories.
   * ``'drop_codes'``: The new variable contains only all nets as new categories.
   * ``'collect_codes'`` or ``'collect_codes@cat_name'``: The new variable contains
     all nets as new categories and another new category which sums all cases that
     are not in any net. The new category text can be defined by adding ``@cat_name``
     to ``collect_codes``. If none is provided ``Other`` is used as default.

""""

**Update**: ``Stack.add_nets()``

If a variable in the ``Stack`` already has a net_view, it gets overwritten
if a new net is added.

""""

**Update**: ``DataSet.set_missings(..., missing_map)``

The parameter ``missing_map`` can also handle lists now. All included
codes are be flagged as ``'exclude'``.

""""

**Update**: ``request_views(..., sums='mid')`` (``ViewManager``/``query.py``)

Allow different positions for sums in the view-order. They can be placed in
the middle (``'mid'``) between the basics/ nets and the stats or at the
``'bottom'`` after the stats.

""""

**Update/ New**: ``write_dimensions()``

Converting qp data to mdd and ddf files by using ``write_dimensions()`` is
updated now. A bug regarding encoding texts is fixed and additionally all
included ``text_keys`` in the meta are transferred into the mdd. Therefore
two new classes are included: ``DimLabels`` and ``DimLabel``.

---------------
sd (13/11/2017)
---------------

**New** ``DataSet.to_delimited_set(name, label, variables,
                           from_dichotomous=True, codes_from_name=True)``

Creates a new delimited set variable out of other variables. If the input-
variables are dichotomous (``from_dichotomous``), the new value-codes can be
taken from the variable-names or from the order of the variables
(``codes_from_name``).

""""

**Update** ``Stack.aggregate(..., bases={})``

A dictionary in form of::

   bases = {
      'cbase': {
         'wgt': True,
         'unwgt': False},
      'cbase_gross': {
         'wgt': True,
         'unwgt': True},
      'ebase': {
         'wgt': False,
         'unwgt': False}
         }

defines what kind of bases will be aggregated. If ``bases`` is provided the
old parameter ``unweighted_base`` and any bases in the parameter ``views``
will be ignored. If bases is not provided and any base is included in ``views``,
a dictionary is automatically created out of ``views`` and ``unweighted_base``.

---------------
sd (17/10/2017)
---------------


**New**: ``del DataSet['var_name']`` and ``'var_name' in DataSet`` syntax support

It is now possible to test membership of a variable name simply using the ``in``
operator instead of ``DataSet.var_exists('var_name')`` and delete a variable definition
from ``DataSet`` using the ``del`` keyword inplace of the ``drop('var_name')``
method.

""""

**New**: ``DataSet.is_single(name)``, ``.is_delimited_set(name)``, ``.is_int(name)``, ``.is_float(name)``, ``.is_string(name)``, ``.is_date(name)``, ``.is_array(name)``

These new methods make testing a variable's type easy.

""""

**Update**: ``DataSet.singles(array_items=True)`` and all other non-``array`` type iterators

It is now possible to exclude ``array`` items from ``singles()``, ``delimited_sets()``,
``ints()`` and ``floats()`` variable lists by setting the new ``array_items``
parameter to ``False``.

""""

**Update**: ``Batch.set_sigtests(..., flags=None, test_total=None)``, ``Batch.sigproperties``

The significancetest-settings for flagging and testing against total, can now
be modified by the two parameters ``flags`` and ``test_total``. The ``Batch``
attribute ``siglevels`` is removed, instead all sig-settings are stored
in ``Batch.sigproperties``.

""""

**Update**: ``Batch.make_summaries(..., exclusive=False)``, ``Batch.skip_items``

The new parameter ``exclusive`` can take a list of arrays or a boolean. If a list
is included, these arrays are added to ``Batch.skip_items``, if it is True all
variables from ``Batch.summaries`` are added to ``Batch.skip_items``

""""

**Update**: ``quantipy.sandbox.sandbox.Chain.paint(..., totalize=True)``

If ``totalize`` is ``True``, ``@``-Total columns of a (x-oriented) ``Chain.dataframe``
will be painted as ``'Total'`` instead of showing the corresponsing ``x``-variables
question text.

""""

**Update**: ``quantipy.core.weights.Rim.Rake``

The weighting algorithm's ``generate_report()`` method can be caught up in a
``MemoryError`` for complex weight schemes run on very large sample sizes. This
is now prevented to ensure the weight factors are computed with priority and
the algorithm is able to terminate correctly. A warning is raised::

   UserWarning: OOM: Could not finish writing report...

""""

**Update**: ``Batch.replace_y()``

Conditional replacements of y-variables of a ``Batch`` will now always also
automatically add the ``@``-Total indicator if not provided.

""""

**Bugfix**: ``DataSet.force_texts(...,  overwrite=True)``

Forced overwriting of existing ``text_key`` meta data was failing for ``array``
``mask`` objects. This is now solved.

""""

---------------
sd (15/09/2017)
---------------

**New**: ``DataSet.meta_to_json(key=None, collection=None)``

The new method allows saving parts of the metadata as a json file. The parameters
``key`` and ``collection`` define the metaobject which will be saved.

""""

**New**: ``DataSet.save()`` and ``DataSet.revert()``

These two new methods are useful in interactive sessions like **Ipython** or
**Jupyter** notebooks. ``save()`` will make a temporary (only im memory, not
written to disk) copy of the ``DataSet`` and store its current state. You can
then use ``revert()`` to rollback to that snapshot of the data at a later
stage (e.g. a complex recode operation went wrong, reloading from the physical files takes
too long...).

""""

**New**: ``DataSet.by_type(types=None)``

The ``by_type()`` method is replacing the soon to be deprecated implementation
of ``variables()`` (see below). It provides the same functionality
(``pd.DataFrame`` summary of variable types) as the latter.

""""

**Update**: ``DataSet.variables()`` absorbs ``list_variables()`` and ``variables_from_set()``

In conjunction with the addition of ``by_type()``, ``variables()`` is
replacing the related ``list_variables()`` and ``variables_from_set()`` methods in order to offer a unified solution for querying the ``DataSet``\'s (main) variable collection.

""""

**Update**: ``Batch.as_addition()``

The possibility to add multiple cell item iterations of one ``Batch`` definition
via that method has been reintroduced (it was working by accident in previous
versions with subtle side effects and then removed). Have fun!

""""

**Update**: ``Batch.add_open_ends()``

The method will now raise an ``Exception`` if called on a ``Batch`` that has
been added to a parent one via ``as_addition()`` to warn the user and prevent
errors at the build stage::

   NotImplementedError: Cannot add open end DataFrames to as_addition()-Batches!

---------------
sd (31/08/2017)
---------------

**New**: ``DataSet.code_from_label(..., exact=True)``

The new parameter ``exact`` is implemented. If ``exact=True`` codes are returned
whose belonging label is equal the included ``text_label``. Otherwise the
method checks if the labels contain the included ``text_label``.

""""

**New**: ``DataSet.order(new_order=None, reposition=None)``

This new method can be used to change the global order of the ``DataSet``
variables. You can either pass a complete ``new_order`` list of variable names to
set the order or provide a list of dictionaries to move (multiple) variables
before a reference variable name. The order is reflected in the case data
``pd.DataFrame.columns`` order and the meta ``'data file'`` ``set`` object's items.

""""

**New**: ``DataSet.dichotomize(name, value_texts=None, keep_variable_text=True, ignore=None, replace=False, text_key=None)``

Use this to convert a ``'delimited set'`` variable into a set of binary coded
``'single'`` variables. Variables will have the values 1/0 and by default use
``'Yes'`` / ``'No'`` as the corresponding labels. Use the ``value_texts``
parameter to apply custom labels.

""""

**New**: ``Batch.extend_x(ext_xks)``

The new method enables an easy extension of ``Batch.xks``. In ``ext_xks``
included ``str`` are added at the end of ``Batch.xks``. Values of included
``dict``\s are positioned in front of the related key.

""""

**Update**: ``Batch.extend_y(ext_yks, ...)``

The parameter ``ext_yks`` now also takes ``dict``\s, which define the position
of the additional ``yks``.

""""

**Update**: ``Batch.add_open_ends(..., replacements)``

The new parameter ``replacements`` is implemented. The method loops over the
whole pd.DataFrame and replaces all keys of the included ``dict``
with the belonging value.

""""

**Update**: ``Stack.add_stats(..., other_source)``

Statistic views can now be added to delimited sets if ``other_source`` is used.
In this case ``other_source`` must be a single or numerical variable.

""""

**Update**: ``DataSet.validate(..., spss_limits=False)``

The new parameter ``spss_limits`` is implemented. If ``spss_limits=True``, the
validate output dataframe is extended by 3 columns which show if the SPSS label
limitations are satisfied.

""""

**Bugfix**: ``DataSet.convert()``

A bug that prevented conversions from ``single`` to numeric types has been fixed.

""""

**Bugfix**: ``DataSet.add_meta()``

A bug that prevented the creation of numerical arrays outside of ``to.array()``
has been fixed. It is now possible to create ``array`` metadata without providing
category references.

""""

**Bugfix**: ``Stack.add_stats()``

Checking the statistic views is skipped now if no single typed variables are
included even if a checking cluster is provided.

""""

**Bugfix**: ``Batch.copy()``

Instead of using a deepcopy of the ``Batch`` instance, a new instance is created
and filled with the attributes of the initial one. Then the copied instance can
be used as additional ``Batch``.

""""

**Bugfix**: ``qp.core.builds.powerpoint``

Access to bar-chart series and colour-filling is now working for
different Powerpoint versions. Also a bug is fixed which came up in
``PowerPointpainter()`` for variables which have fixed categories and whose
values are located in ``lib``.

---------------
sd (24/07/2017)
---------------

**New**: ``qp.set_option()``

It is now possible to set library-wide settings registered in ``qp.OPTIONS``
by providing the setting's name (key) and the desired value. Currently supported
are::

	OPTIONS = {
		'new_rules': False,
		'new_chains': False,
		'short_item_texts': False
	}

So for example, to work with the currently refactored ``Chain`` interim class
we can use ``qp.set_options('new_chains', True)``.

""""

**New**: ``qp.Batch()``

This is a new object aimed at defining and structuring aggregation and build
setups. Please see an :doc:`extensive overview here <../lib_doc/batch/00_overview>`.

""""

**New**: ``Stack.aggregate()`` / ``add_nets()`` / ``add_stats()`` / ``add_tests()`` / ...

Connected to the new ``Batch`` class, some new ``Stack`` methods to ease up
view creation have been added. You can :doc:`find the docs here <../lib_doc/engine/00_overview>`.

""""

**New**: ``DataSet.populate()``

Use this to create a ``qp.Stack`` from ``Batch`` definitions. This connects the
``Batch`` and ``Stack`` objects; check out the  :doc:`Batch <../lib_doc/batch/00_overview>`
and :doc:`Analysis & aggregation <../lib_doc/engine/00_overview>` docs.

""""

**New**: ``DataSet.write_dimensions(path_mdd=None, path_ddf=None, text_key=None, mdm_lang='ENG', run=True, clean_up=True)``

It is now possible to directly convert a ``DataSet`` into a Dimensions .ddf/.mdd
file pair (given SPSS Data Collection Base Professional is installed on your
machine). By default, files will be saved to the same location in that the
``DataSet`` resides and keep its ``text_key``.

""""

**New**: ``DataSet.repair()``

This new method can be used to try to fix common ``DataSet`` metadata problems
stemming from outdated versions, incorrect manual editing of the meta dictionary
or other inconsistencies. The method is checking and repairing following issues:

	* ``'name'`` is present for all variable metadata
	* ``'source'`` and ``'subtype'`` references for array variables
	* correct ``'lib'``-based ``'values'`` object for array variables
	* ``text key``-dependent ``'x edits'`` / ``'y edits'`` meta data
	* ``['data file']['items']`` set entries exist in ``'columns'`` / ``'masks'``

""""

**New**: ``DataSet.subset(variables=None, from_set=None, inplace=False)``

As a counterpart to ``filter()``, ``subset()`` can be used to create a new
``DataSet`` that contains only a selection of variables. The new variables
collection can be provided either as a list of names or by naming an already
existing set containing the desired variables.

""""

**New**: ``DataSet.variables_from_set(setname)``

Get the list of variables belonging to the passed set indicated by
``setname``.

""""

**New**: ``DataSet.is_like_numeric(name)``

A new method to test if all of a ``string`` variable's values can be converted
to a numerical (``int`` / ``float``) type. Returns a boolean ``True`` / ``False``.

""""

**Update**: ``DataSet.convert()``

It is now possible to convert inplace from ``string`` to ``int`` / ``float`` if
the respective internal ``is_like_numeric()`` check identifies numeric-like values.

""""

**Update**: ``DataSet.from_components(..., reset=True)``, ``DataSet.read_quantipy(..., reset=True)``

Loaded ``.json`` metadata dictionaries will get cleaned now by default from any
user-defined, non-native objects inside the ``'lib'`` and ``'sets'``
collections. Set ``reset=False`` to keep any extra entires (restoring the old
behaviour).

""""

**Update**: ``DataSet.from_components(data_df, meta_dict=None, ...)``

It is now possible to create a ``DataSet`` instance by providing a ``pd.DataFrame``
alone, without any accompanying meta data. While reading in the case data, the meta
component will be created by inferring the proper ``Quantipy`` variable types
from the ``pandas`` ``dtype`` information.

""""

**Update**: ``Quantity.swap(var, ..., update_axis_def=True)``

It is now possible to ``swap()`` the ``'x'`` variable of an array based ``Quantity``,
as long as the length oh the constructing ``'items'`` collection is identical.
In addition, the new parameter ``update_axis_def`` is now by default enforcing
an update of the axis defintions (``pd.DataFrame`` column names, etc) while
previously the method was keeping the original index and column names. The old
behaviour can be restored by setting the parameter to ``False``.

*Array example*:

>>> link = stack[name_data]['no_filter']['q5']['@']
>>> q = qp.Quantity(link)
>>> q.summarize()
Array                     q5
Questions               q5_1         q5_2         q5_3         q5_4         q5_5         q5_6
Question Values
q5       All     8255.000000  8255.000000  8255.000000  8255.000000  8255.000000  8255.000000
         mean      26.410297    22.260569    25.181466    39.842883    24.399758    28.972017
         stddev    40.415559    38.060583    40.018463    46.012205    40.537497    41.903322
         min        1.000000     1.000000     1.000000     1.000000     1.000000     1.000000
         25%        3.000000     3.000000     3.000000     3.000000     1.000000     3.000000
         median     5.000000     3.000000     3.000000     5.000000     3.000000     5.000000
         75%        5.000000     5.000000     5.000000    98.000000     5.000000    97.000000
         max       98.000000    98.000000    98.000000    98.000000    98.000000    98.000000

*Updated axis definiton*:

>>> q.swap('q7', update_axis_def=True)
>>> q.summarize()
Array                     q7
Questions               q7_1         q7_2         q7_3       q7_4       q7_5       q7_6
Question Values
q7       All     1195.000000  1413.000000  3378.000000  35.000000  43.000000  36.000000
         mean       5.782427     5.423213     5.795145   4.228571   4.558140   5.333333
         stddev     2.277894     2.157226     2.366247   2.073442   2.322789   2.552310
         min        1.000000     1.000000     1.000000   1.000000   1.000000   1.000000
         25%        4.000000     4.000000     4.000000   3.000000   3.000000   3.000000
         median     6.000000     6.000000     6.000000   4.000000   4.000000   6.000000
         75%        8.000000     7.000000     8.000000   6.000000   6.000000   7.750000
         max        9.000000     9.000000     9.000000   8.000000   9.000000   9.000000

*Original axis definiton*:

>>> q = qp.Quantity(link)
>>> q.swap('q7', update_axis_def=False)
>>> q.summarize()
Array                     q5
Questions               q5_1         q5_2         q5_3       q5_4       q5_5       q5_6
Question Values
q5       All     1195.000000  1413.000000  3378.000000  35.000000  43.000000  36.000000
         mean       5.782427     5.423213     5.795145   4.228571   4.558140   5.333333
         stddev     2.277894     2.157226     2.366247   2.073442   2.322789   2.552310
         min        1.000000     1.000000     1.000000   1.000000   1.000000   1.000000
         25%        4.000000     4.000000     4.000000   3.000000   3.000000   3.000000
         median     6.000000     6.000000     6.000000   4.000000   4.000000   6.000000
         75%        8.000000     7.000000     8.000000   6.000000   6.000000   7.750000
         max        9.000000     9.000000     9.000000   8.000000   9.000000   9.000000


""""

**Update**: ``DataSet.merge_texts()``

The method will now always overwrite existing ``text_key`` meta, which makes it
possible to merge ``text``\s from meta of the same ``text_key`` as the master
``DataSet``.

""""

**Bugfix**: ``DataSet.band()``

``band(new_name=None)``\'s automatic name generation was incorrectly creating
new variables with the name ``None_banded``. This is now fixed.

""""

**Bugfix**: ``DataSet.copy()``

The method will now check if the name of the copy already exists in the
``DataSet`` and drop the referenced variable if found to prevent
inconsistencies. Additionally, it is not longer possible to copy isolated
``array`` items:

>>> dataset.copy('q5_1')
NotImplementedError: Cannot make isolated copy of array item 'q5_1'. Please copy array variable 'q5' instead!

---------------
sd (08/06/2017)
---------------



**New**: ``DataSet.extend_valid_tks()``, ``DataSet.valid_tks``

``DataSet`` has a new attribute ``valid_tks`` that contains a list of all valid
textkeys. All methods that take a textkey as parameter are checked against that
list.

If a datafile contains a special/ unusual textkey (for example ``'id-ID'`` or
``'zh-TW'``), the list can be extended with ``DataSet.extend_valid_tks()``.
This extension can also be used to create a textkey for special conditions,
for example to create texts only for powerpoint outputs::

	>>> dataset.extend_valid_tks('pptx')
	>>> dataset.force_texts('pptx', 'en-GB')
	>>> dataset.set_variable_text('gender','Gender label for pptx', text_key='pptx')

""""

**New**: Equal error messages

All methods that use the parameters ``name``/``var``, ``text_key`` or
``axis_edit``/ ``axis`` now have a decorator that checks the provided values.
The following shows a few examples for the new error messages:

``name`` & ``var``::

	'name' argument for meta() must be in ['columns', 'masks'].
	q1 is not in ['columns', 'masks'].

``text_key``::

	'en-gb' is not a valid text_key! Supported are: ['en-GB', 'da-DK', 'fi-FI', 'nb-NO', 'sv-SE', 'de-DE']

``axis_edit`` & ``axis``::

	'xs' is not a valid axis! Supported are: ['x', 'y']

""""

**New**: ``DataSet.repair_text_edits(text_key)``

This new method can be used in trackers, that were drawn up in an older ``Quantipy``
version. Text objects can be repaired if are not well prepared, for example if
it looks like this::

	{'en-GB': 'some English text',
	 'sv_SE': 'some Swedish text',
	 'x edits': 'new text'}

``DataSet.repair_text_edits()`` loops over all text objects in the dataset and
matches the ``x edits`` and ``y edits`` texts to all included textkeys::

	>>> dataset.repair_text_edits(['en-GB', 'sv-SE'])
	{'en-GB': 'some English text',
	 'sv_SE': 'some Swedish text',
	 'x edits': {'en-GB': new text', 'sv-SE': 'new text'}}

""""

**Update**: ``DataSet.meta()``/ ``.text()``/ ``.values()``/ ``.value_texts()``/ ``.items()``/ ``.item_texts()``

All these methods now can take the parameters ``text_key`` and ``axis_edit``.
The related text is taken from the meta information and shown in the output.
If a text key or axis edit is not included the text is returned as None.

""""

**Update**: ``DataSet.compare(dataset, variables=None, strict=False, text_key=None)``

The method is totally updated, works more precise and contains a few new
features. Generally variables included in ``dataset`` are compared with
eponymous variables in the main ``DataSet`` instance. You can specify witch
``variables`` should be compared, if question/ value texts should be compared
``strict`` or not and for which ``text_key``.

""""

**Update**: ``DataSet.validate(verbose=True)``

A few new features are tested now and the output has changed. Set ``verbose=True``
to see the definitions of the different error columns::

    name: column/mask name and meta[collection][var]['name'] are not identical

    q_label: text object is badly formated or has empty text mapping

    values: categorical var does not contain values, value text is badly
    formated or has empty text mapping

    textkeys: dataset.text_key is not included or existing tks are not
    consistent (also for parents)

    source: parents or items do not exist

    codes: codes in .data are not included in .meta

""""

**Update**: ``DataSet.sorting()`` / ``.slicing()`` / ``.hiding()``

These methods will now also work on lists of variable names.

""""

**Update**: ``DataSet.set_variable_text()``, ``Dataset.set_item_texts()``

If these methods are applied to an array item, the new variable text is also
included in the meta information of the parent array. The same works also the
other way around, if an array text is set, then the array item texts are modified.

""""

**Update**: ``DataSet.__init__(self, name, dimensions_comp=True)``

A few new features are included to handle data coming from Crunch. While
initializing a new ``DataSet`` instance dimensions compatibility can be set to
False. In the custom template use ``t.get_qp_dataset(name, dim_comp=False)``
in the load cells.

""""

**Bugfix**: ``DataSet.hmerge()``

If ``right_on`` and ``left_on`` are used and ``right_on`` is also included in
the main file, it is not overwritten any more.

---------------
sd (17/05/2017)
---------------

**Update**: ``DataSet.set_variable_text(..., axis_edit=None)``, ``DataSet.set_value_texts(..., axis_edit=False)``

The new ``axis_edit`` argument can be used with one of ``'x'``, ``'y'`` or ``['x', 'y']`` to instruct a text metadata change that will only be visible in build exports.

.. warning::
	In a future version ``set_col_text_edit()`` and ``set_val_text_text()`` will
	be removed! The identical functionality is provided via this ``axis_edit`` parameter.

""""

**Update**: ``DataSet.replace_texts(..., text_key=None)``

The method loops over all meta text objects and replaces unwanted strings.
It is now possible to perform the replacement only for specified ``text_key``\s.
If ``text_key=None`` the method replaces the strings for all ``text_key``\s.

""""

**Update**: ``DataSet.force_texts(copy_to=None, copy_from=None, update_existing=False)``

The method is now only able to force texts for all meta text objects (for
single variables use the methods ``set_variable_text()`` and
``set_value_texts()``).

""""

**Bugfix**: ``DataSet.copy()``

Copied variables get the tag ``created`` and can be listed with
``t.list_variables(dataset, 'created')``.

""""

**Bugfix**: ``DataSet.hmerge()``, ``DataSet.vmerge()``

Array meta information in merged datafiles is now updated correctly.

---------------
sd (04/05/2017)
---------------

**New**: ``DataSet.var_exists()``

Returns True if the input variable/ list of variables are included in the
``DataSet`` instance, otherwise False.

""""

**New**: ``DataSet.remove_html()``, ``DataSet.replace_texts(replace)``

The ``DataSet`` method ``clean_texts()`` has been removed and split into two
methods to make usage more clear: ``remove_html()`` will strip all ``text``
metadata objects from any html and formatting tags. ``replace_texts()`` will
use a ``dict`` mapping of old to new ``str`` terms to change the matching
``text`` throughout the ``DataSet`` metadata.

""""

**New**: ``DataSet.item_no(name)``

This method will return the positional index number of an array item, e.g.:

>>> dataset.item_no('Q4A[{q4a_1}].Q4A_grid')
1

""""

**New**: ``QuantipyViews``: ``counts_cumsum``, ``c%_cumsum``

These two new views contain frequencies with cumulative sums which are computed
over the x-axis.

""""

**Update**: ``DataSet.text(name, shorten=True)``

The new parameter ``shorten`` is now controlling if the variable ``text`` metadata
of array masks will be reported in short format, i.e. without the corresponding
mask label text. This is now also the default behaviour.

""""

**Update**: ``DataSet.to_array()``

Created mask meta information now also contains keys ``parent`` and ``subtype``.
Variable names are compatible with crunch and dimensions meta:

Example in Dimensions modus:

>>> dataset.to_array('Q11', ['Q1', 'Q2', 'Q3', 'Q4', 'Q5'], 'label')

The new grid is named ``'Q11.Q11_grid'`` and the source/column variables are
``'Q11[{Q1}].-Q11_grid'`` - ``'Q11[{Q5}].-Q11_grid'``.

""""

**Bugfix**: ``DataSet.derotate()``

Meta is now Crunch and Dimensions compatible. Also mask meta information are updated.

---------------
sd (24/04/2017)
---------------

**Update**: ``DataSet.hiding(..., hide_values=True)``

The new parameter ``hide_values`` is only necessary if the input variable is a
mask. If ``False``, mask items are hidden, if ``True`` mask values are hidden
for all mask items and for array summary sheets.

""""

**Bugfix**: ``DataSet.set_col_text_edit(name)``

If the input variable is an array item, the new column text is also added to
``meta['mask'][name]['items]``.


""""

**Bugfix**: ``DataSet.drop(name, ignore_items=False)``

If a mask is dropped, but the items are kept, all items are handled now as
individual variables and their meta information is not stored in ``meta['lib']``
anymore.

---------------
sd (06/04/2017)
---------------

Only small adjustments.

---------------
sd (29/03/2017)
---------------

**New**: ``DataSet.codes_in_data(name)``

This method returns a list of codes that exist in the data of a variable. This
information can be used for more complex recodes, for example copying a variable,
but keeping only all categories with more than 50 ratings, e.g.:

>>> valid_code = dataset.codes_in_data('varname')
>>> keep_code = [x for x in valid_code if dataset['varname'].value_counts()[x] > 49]
>>> dataset.copy('varname', 'rc', copy_only=keep_code)

""""

**Update**: ``DataSet.copy(..., copy_not=None)``

The new parameter ``copy_not`` takes a list of codes that should be ignored
for the copied version of the provided variable. The metadata of the copy will
be reduced as well.

""""

**Update**: ``DataSet.code_count()``

This method is now alligned with ``any()`` and ``all()`` in that it can be used
on ``'array'`` variables as well. In such a case, the resulting ``pandas.Series``
is reporting the number of answer codes found across all items per case data
row, i.e.:

>>> code_count = dataset.code_count('Q4A.Q4A_grid', count_only=[3, 4])
>>> check = pd.concat([dataset['Q4A.Q4A_grid'], code_count], axis=1)
>>> check.head(10)
   Q4A[{q4a_1}].Q4A_grid  Q4A[{q4a_2}].Q4A_grid  Q4A[{q4a_3}].Q4A_grid  0
0                    3.0                    3.0                    NaN  2
1                    NaN                    NaN                    NaN  0
2                    3.0                    3.0                    4.0  3
3                    5.0                    4.0                    2.0  1
4                    4.0                    4.0                    4.0  3
5                    4.0                    5.0                    4.0  2
6                    3.0                    3.0                    3.0  3
7                    4.0                    4.0                    4.0  3
8                    6.0                    6.0                    6.0  0
9                    4.0                    5.0                    5.0  1

""""

---------------
sd (20/03/2017)
---------------

**New**: ``qp.DataSet(dimensions_comp=True)``

The ``DataSet`` class can now be explicitly run in a Dimensions compatibility
mode to control the naming conventions of ``array`` variables ("grids"). This
is also the default behaviour for now. This comes with a few changes related to
meta creation and variable access using ``DataSet`` methods. Please see a brief
case study on this topic :doc:`here <how_to_snippets/dimensions_comp>`.

""""

**New**: enriched ``items`` / ``masks`` meta data

``masks`` will now also store the ``subtype`` (``single``, ``delimited set``, etc.)
while ``items`` elements will now contain a reference to the defining ``masks``
entrie(s) in a new ``parent`` object.

""""

**Update**: ``DataSet.weight(..., subset=None)``

Filters the dataset by giving a Quantipy complex logic expression and weights
only the remaining subset.

""""

**Update**: Defining categorical ``values`` meta and ``array`` items

Both ``values`` and ``items`` can now be created in three different ways when
working with the ``DataSet`` methods ``add_meta()``, ``extend_values()`` and
``derive()``: (1) Tuples that map element code to label, (2) only labels or (3)
only element codes. Please see quick guide on that :doc:`here <how_to_snippets/create_categorical_meta>`

---------------
sd (07/03/2017)
---------------

**Update**: ``DataSet.code_count(..., count_not=None)``

The new parameter ``count_not`` can be used to restrict the set of codes feeding
into the resulting ``pd.Series`` by exclusion (while ``count_only`` restricts
by inclusion).

""""

**Update**: ``DataSet.copy(..., copy_only=None)``

The new parameter ``copy_only`` takes a list of codes that should be included
for the copied version of the provided variable, all others will be ignored
and the metadata of the copy will be reduced as well.

""""

**Bugfix**: ``DataSet.band()``

There was a bug that was causing the method to crash for negative values. It is
now possible to create negative single value bands, while negative ranges
(lower and/or upper bound < 0) will raise a ``ValueError``.

""""

---------------
sd (24/02/2017)
---------------

*	Some minor bugfixes and updates. Please use latest version.

""""

---------------
sd (16/02/2017)
---------------

**New:** ``DataSet.derotate(levels, mapper, other=None, unique_key='identity', dropna=True)``

Create a derotated ("levelled", responses-to-cases) ``DataSet`` instance by
defining level variables, looped variables and other (simple) variables that
should be added.

View more information on the topic :doc:`here <how_to_snippets/derotate>`.

""""

**New:** ``DataSet.to_array(name, variables, label)``

Combine ``column`` variables with identical ``values`` objects to an ``array``
incl. all required ``meta['masks']`` information.

""""

**Update:** ``DataSet.interlock(..., variables)``

It is now possible to add ``dict``\s to ``variables``. In these ``dict``\s a
``derive()``-like mapper can be included which will then create a temporary
variable for the interlocked result. Example:

>>> variables = ['gender',
...              {'agegrp': [(1, '18-34', {'age': frange('18-34')}),
...                          (2, '35-54', {'age': frange('35-54')}),
...                          (3, '55+', {'age': is_ge(55)})]},
...              'region']
>>> dataset.interlock('new_var', 'label', variables)

""""

---------------
sd (04/01/2017)
---------------

**New:** ``DataSet.flatten(name, codes, new_name=None, text_key=None)``

Creates a new ``delimited set`` variable that groups ``grid item`` answers to
categories. The ``items`` become ``values`` of the new variable. If an
``item`` contains one of the ``codes`` it will be counted towards the categorical
case data of the new variable.

""""

**New:** ``DataSet.uncode(target, mapper, default=None, intersect=None, inplace=True)``

Remove codes from the ``target`` variable's data component if a logical
condition is satisfied.

""""

**New:** ``DataSet.text(var, text_key=None)``

Returns the question text label (per ``text_key``) of a variable.

""""

**New:** ``DataSet.unroll(varlist, keep=None, both=None)``

Replaces ``masks`` names inside ``varlist`` with their ``items``. Optionally,
individual ``masks`` can be excluded or kept inside the list.

""""

**New:** ``DataSet.from_stack(stack, datakey=None)``

Create a ``quantipy.DataSet`` from the ``meta``, ``data``, ``data_key`` and
``filter`` definition of a ``quantipy.Stack`` instance.

""""

---------------
sd (8/12/2016)
---------------

**New:**

``DataSet.from_excel(path_xlsx, merge=True, unique_key='identity')``

Returns a new ``DataSet`` instance with ``data`` from ``excel``. The ``meta``
for all variables contains ``type='int'``.

Example: ``new_ds = dataset.from_excel(path, True, 'identity')``

The function is able to modify ``dataset`` inplace by merging ``new_ds`` on
``identity``.

""""

**Update:**

``DataSet.copy(..., slicer=None)``

It is now possible to filter the data that statisfies the logical condition
provided in the ``slicer``.
Example:

>>> dataset.copy('q1', 'rec', True, {'q1': not_any([99])})

""""

---------------
sd (23/11/2016)
---------------

**Update:**

``DataSet.rename(name, new_name=None, array_item=None)``

The function is able to rename ``columns``, ``masks`` or ``mask items``.
``maks items`` are changed by position.

""""

**Update:**

``DataSet.categorize(..., categorized_name=None)``

Provide a custom name string for ``categorized_name`` will change the default
name of the categorized variable from ``OLD_NAME#`` to the passed string.


""""

---------------
sd (16/11/2016)
---------------

**New:**

``DataSet.check_dupe(name='identity')``

Returns a list with duplicated values for the variable provided via ``name``.
Identifies for example duplicated identities.

""""

**New:**

``DataSet.start_meta(text_key=None)``

Creates an empty QP meta data document blueprint to add variable definitions to.

""""

**Update:**

.. code-block:: python

	DataSet.create_set(setname='new_set', based_on='data file', included=None,
	...		  excluded=None, strings='keep', arrays='both', replace=None,
	...		  overwrite=False)

Add a new ``set`` to the ``meta['sets']`` object. Variables from an existing
``set`` (``based_on``) can be ``included`` to ``new_set`` or varibles can be
``excluded`` from ``based_on`` with customized lists of variables.
Control ``string`` variables and ``masks`` with the ``kwargs`` ``strings`` and
``arrays``. ``replace`` single variables in ``new_set`` with a ``dict`` .

""""

**Update:**

``DataSet.from_components(..., text_key=None)``

Will now accept a ``text_key`` in the method call. If querying a ``text_key``
from the meta component fails, the method will no longer crash, but raise a
``warning`` and set the ``text_key`` to ``None``.

""""

**Update:**

.. line-block::

	``DataSet.as_float()``
	``DataSet.as_int()``
	``DataSet.as_single()``
	``DataSet.as_delimited_set()``
	``DataSet.as_string()``
	``DataSet.band_numerical()``
	``DataSet.derive_categorical()``
	``DataSet.set_mask_text()``
	``DataSet.set_column_text()``

These methods will now print a ``UserWarning`` to prepare for the soon to
come removal of them.

""""

**Bugfix:**

``DataSet.__setitem__()``

Trying to set ``np.NaN`` was failing the test against meta data for categorical
variables and was raising a ``ValueError`` then. This is fixed now.

""""

---------------
sd (11/11/2016)
---------------

**New:**

.. line-block::

	``DataSet.columns``
	``DataSet.masks``
	``DataSet.sets``
	``DataSet.singles``
	``DataSet.delimited_sets``
	``DataSet.ints``
	``DataSet.floats``
	``DataSet.dates``
	``DataSet.strings``

New ``DataSet`` instance attributes to quickly return the list of ``columns``,
``masks`` and ``sets`` objects from the meta or query the variables by
``type``. Use this to check for variables, iteration, inspection, ect.

""""

**New:**

``DataSet.categorize(name)``

Create a categorized version of ``int/string/date`` variables. New variables
will be named as per ``OLD_NAME#``

""""

**New:**

``DataSet.convert(name, to)``

Wraps the individual ``as_TYPE()`` conversion methods. ``to`` must be one of
``'int', 'float', 'string', 'single', 'delimited set'``.

""""

**New:**

``DataSet.as_string(name)``

Only for completeness: Use ``DataSet.convert(name, to='string')`` instead.

Converts ``int/float/single/date`` typed variables into a ``string`` and
removes all categorical metadata.

""""

**Update:**

``DataSet.add_meta()``

Can now add ``date`` and ``text`` type meta data.

""""

**Bugfix:**

``DataSet.vmerge()``

If ``masks`` in the right ``dataset``, that also exist in the left ``dataset``,
have new ``items`` or ``values``, they are added to ``meta['masks']``,
``meta['lib']`` and ``meta['sets']``.

""""

---------------
sd (09/11/2016)
---------------

**New:**

``DataSet.as_float(name)``

Converts ``int/single`` typed variables into a ``float`` and removes
all categorical metadata.

""""

**New:**

``DataSet.as_int(name)``

Converts ``single`` typed variables into a ``int`` and removes
all categorical metadata.

""""

**New:**

``DataSet.as_single(name)``

Converts ``int`` typed variables into a ``single`` and adds numeric values as
categorical metadata.

""""

**New:**

``DataSet.create_set(name, variables, blacklist=None)``

Adds a new ``set`` to ``meta['sets']`` object. Create easily ``sets`` from
other ``sets`` while using customised ``blacklist``.

""""

**New:**

``DataSet.drop(name, ignore_items=False)``

Removes all metadata and data referenced to the variable. When passing an
``array mask``, ``ignore_items`` can be ste to ``True`` to keep the ``item
columns`` incl. their metadata.

""""

**New:**

``DataSet.compare(dataset=None, variables=None)``

Compare the metadata definition between the current and another ``dataset``,
optionally restricting to a pair of variables.

""""

**Update:**

``DataSet.__setitem__()``

``[..]``-Indexer now checks scalars against categorical meta.

