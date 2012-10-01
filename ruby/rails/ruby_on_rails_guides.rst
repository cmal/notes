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
Active Record Associations
~~~~~~~~~~~~~~~~~~~~~~~~~~
3 Tips, Tricks and Warnings
^^^^^^^^^^^^^^^^^^^^^^^^^^^
3.5 Bi-directional Associations 
'''''''''''''''''''''''''''''''
<http://guides.rubyonrails.org/association_basics.html#bi-directional-associations>

:inverse_of .. 在关系中，把关系的一方赋值给另一方，由于默认不会同步两者，所以在需要同步时应加入``:inverse_of``帮助ruby知道应该同步。但在某些关系中不能使用。

4 Detailed Associations Reference
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
TODO:       .. 各种关系的详细参考<http://guides.rubyonrails.org/association_basics.html#detailed-association-reference>有时间或用到的时候再看。

ActiveRecord查询接口 Active Record Query Interface
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
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


