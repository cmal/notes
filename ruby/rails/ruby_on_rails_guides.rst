Ruby on Rails Guides
====================
Start Here
----------
Getting Started with Rails
~~~~~~~~~~~~~~~~~~~~~~~~~~
Model
-----
Rails Database Migration
~~~~~~~~~~~~~~~~~~~~~~~~
Active Record Validations and Callbacks
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
1 对象生命周期 The Object Life Cycle
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

1. Validations
2. Callbacks

2 Validations概览
^^^^^^^^^^^^^^^^^

2.1 Why
'''''''

1. 数据库约束
2. 客户端验证
3. 控制层验证
4. 模型层验证 *the best way*

2.2 验证什么时候进行 When Does Validation Happen?
'''''''''''''''''''''''''''''''''''''''''''''''''

两种ActiveRecord对象:
    + 在数据库中有对应记录的
    + 没有对应记录的

Active Record Associations
~~~~~~~~~~~~~~~~~~~~~~~~~~
3 Tips, Tricks and Warnings
^^^^^^^^^^^^^^^^^^^^^^^^^^^
3.5 Bi-directional Associations 
'''''''''''''''''''''''''''''''
<http://guides.rubyonrails.org/association_basics.html#bi-directional-associations>

:inverse_of 在关系中，把关系的一方赋值给另一方，由于默认不会同步两者，所以在需要同步时应加入``:inverse_of``帮助ruby知道应该同步。但在某些关系中不能使用。

4 Detailed Associations Reference
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
TODO: 各种关系的详细参考<http://guides.rubyonrails.org/association_basics.html#detailed-association-reference>有时间或用到的时候再看。

ActiveRecord查询接口 Active Record Query Interface
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
本教程可以让你学会:
* 用多种方法和条件查找记录 Find records using a variety of methods and conditions
* 对找到的对象细化: 顺序、抽出的字段（属性）、分组、以及其他特性 Specify the order, retrieved attributes, grouping, and other properties of the found records
* 用EagerLoading技术减少数据库查询次数 Use eager loading to reduce the number of database queries needed for data retrieval
* 使用动态查找方法 Use dynamic finders methods
* 验证特定记录是否存在 Check for the existence of particular records
* 在ActiveRecord模型上执行多种计算 Perform various calculations on Active Record models
* Run EXPLAIN on relations

1 从数据库抽取对象 Retrieving Objects from the Database
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
ActiveRecord提供的方法有:

* where
* select
* group
* order
* reorder
* reverse_order
* limit
* offset
* joins
* includes
* lock
* readonly
* from
* having

所有方法返回一个ActiveRecord::Relation实例

Model.find(options)的基本操作总结如下:

* 把options转换为SQL
* 执行SQL，抽取结果
* 对每个结果行，实例化同等的Ruby对象
* 执行``after_find``回调函数，如果有

1.1 抽取单个对象 Retrieving a Single Objects
''''''''''''''''''''''''''''''''''''''''''''

=========  =========================
使用主键   ``client = Client.find(10)``
first      ``client = Client.first``
           如果找不到返回nil，不引发Exception
last
first!     如果找不到raise RecordNotFound
last!
=========  =========================

1.2 抽取多个对象 Retrieving Multiple Objects
'''''''''''''''''''''''''''''''

===============  ==================================
使用多个主键     Model.find(array_of_primary_key)
                 ``client = Client.find([1, 10])``
                 or ``client = Client.find(1, 10)`` 一样的意思
                 找不到raise ActiveRecord::RecordNotFound
===============  ==================================

1.3 使用批处理法抽取多个对象 Retrieving Multiple Objects in Batches
''''''''''''''''''''''''''''''''''''''''''
2 Conditions
^^^^^^^^^^^^
2.1 Pure String Conditions
''''''''''''''''''''''''''
2.2 Array Conditions
''''''''''''''''''''
2.3 Hash Conditions
'''''''''''''''''''
3 Ordering
^^^^^^^^^^
4 Selecting Specific Fields
^^^^^^^^^^^^^^^^^^^^^^^^^^^
5 Limit and Offset
^^^^^^^^^^^^^^^^^^
6 Group
^^^^^^^
7 Having
^^^^^^^^
8 Overriding Conditions
^^^^^^^^^^^^^^^^^^^^^^^
8.1 except
''''''''''
8.2 only
''''''''
8.3 reorder
'''''''''''
8.4 reverse order
'''''''''''''''''
9 Readonly Objects
^^^^^^^^^^^^^^^^^^
10 Locking Records for Update
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
10.1 Optimistic Locking
'''''''''''''''''''''''
10.2 Pessimistic Locking
''''''''''''''''''''''''
11 Joining Tables
^^^^^^^^^^^^^^^^^
11.1 Using a String SQL Fragment
''''''''''''''''''''''''''''''''
11.2 Using Array/Hash of Named Associations
'''''''''''''''''''''''''''''''''''''''''''
11.3 Specifying Conditions on the Joined Tables
'''''''''''''''''''''''''''''''''''''''''''''''
12 Eager Loading Associations
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
12.1 Eager Loading Multiple Associations
''''''''''''''''''''''''''''''''''''''''
12.2 Specifying Conditions on Eager Loaded
''''''''''''''''''''''''''''''''''''''''''
13 Scopes
^^^^^^^^^
13.1 Working with times
'''''''''''''''''''''''
13.2 Passing in arguments
'''''''''''''''''''''''''
13.3 Working with scopes
''''''''''''''''''''''''
13.4 Applying a default scope
'''''''''''''''''''''''''''''
13.5 Removing all scoping
'''''''''''''''''''''''''
14 Dynamic Finders
^^^^^^^^^^^^^^^^^^
15 Find or build a new object
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
15.1 first or create
''''''''''''''''''''
15.2 first or create!
'''''''''''''''''''''
15.3 first or initialize
''''''''''''''''''''''''
16 Finding by SQL
^^^^^^^^^^^^^^^^^
17 select all
^^^^^^^^^^^^^
18 pluck
^^^^^^^^
19 Existence of Objects
^^^^^^^^^^^^^^^^^^^^^^^
20 Calculations
^^^^^^^^^^^^^^^
20.1 Count
''''''''''
20.2 Average
''''''''''''
20.3 Minimum
''''''''''''
20.4 Maximum
''''''''''''
20.5 Sum
''''''''
21 Running EXPLAIN
^^^^^^^^^^^^^^^^^^
21.1 Automatic EXPLAIN
''''''''''''''''''''''
21.2 Interpreting EXPLAIN
'''''''''''''''''''''''''
