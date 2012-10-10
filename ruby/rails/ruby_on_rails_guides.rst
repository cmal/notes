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
''''''''''''''''''''''''''''''''''''''''''''

============  ==================================
使用多个主键  Model.find(array_of_primary_key)
              ``client = Client.find([1, 10])``
              or ``client = Client.find(1, 10)`` 一样的意思
              找不到raise ActiveRecord::RecordNotFound
============  ==================================

1.3 使用批处理法抽取多个对象 Retrieving Multiple Objects in Batches
'''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''

平常的方法

::
    User.all.each do |user|
        NewsLetter.weekly_deliver(user)
    end

Ruby提供两种方法使大量的记录分成内存容量允许的小部分来处理:
如果只是几千条记录的话还是使用平常的find方法吧

+---------------------+------------------------------------------+
| find_each           | ::                                       |
|                     |                                          |
|                     |     User.find_each do |user|             |
|                     |         NewsLetter.weekly_deliver(user)  |
|                     |     end                                  |
|                     |                                          |
+---------------------+------------------------------------------+
| find_in_batches     |    ...                                   |
+---------------------+------------------------------------------+

``:batch_size``
``:start``

2 条件 Conditions
^^^^^^^^^^^^^^^^^
条件可以是:String,Array,Hash
2.1 纯String条件 Pure String Conditions
''''''''''''''''''''''''''
>>> Client.where("orders_count = '2'")
潜在的SQL注入风险！
2.2 Array条件 Array Conditions
''''''''''''''''''''
>>> Client.where("orders_count = ? AND locked = ?", params[:orders], false)
用后面参数替换前面引号里对应位置的?
Placeholder条件
>>> Client.where("created_at >= :start_date AND created_at <= :end_date",
    {:start_date => params[:start_date], :end_date => params[:end_date]}
Range条件
>>> Client.where(:created_at => (params[:start_date].to_date)..(params[:end_date].to_date))
2.3 Hash Conditions
'''''''''''''''''''
Hash条件只可以使用: equality (checking), range (checking), and subset checking。
1. Equality Conditions
   >>> Client.where(:locked => true)
   >>> Client.where('locked' => true)
2. Range Conditions
   >>> CLient.where(:created_at => (Time.now.midnight - 1.day)..(Time.now.midnight))
3. Subset Conditions
   >>> Client.where(:orders_count => [1,3,5])
3 Ordering
^^^^^^^^^^
>>> Client.order("orders_count ASC, created_at DESC")
4 Selecting Specific Fields
^^^^^^^^^^^^^^^^^^^^^^^^^^^
如果select方法被使用，则所有返回的对象都是只读的
>>> Client.select("viewable_by, locked")
如果Client不存在指定的field，则会产生:
``ActiveModel::MissingAttributeError: Missing attribute: <attribute>``
同样的值只取一个:
>>> Client.select(:name).uniq
or removed after:
>>> query = CLient.select(:name).uniq
>>> query.uniq(false)
5 Limit and Offset
^^^^^^^^^^^^^^^^^^
>>> CLient.limit(5)
从第31条记录开始
>>> CLient.limit(5).offset(30)
6 Group
^^^^^^^
即GROUP BY
>>> Order.select("date(created_at) as ordered_date, sum(price) as total_price").group("date(created_at)")
7 Having
^^^^^^^^
对由sum或其它集合函数运算结果的输出进行限制。
SQL语言中设定集合函数的查询条件时使用HAVING从句而不是WHERE从句。通常情况下，HAVING从句被放置在SQL命令的结尾处。
>>> Order.select("date(created_at) as ordered_date, sum(price) as total_price").group("date(created_at)").having("sum(price) > ?", 100)
8 覆盖（重写）条件 Overriding Conditions
^^^^^^^^^^^^^^^^^^^^^^^
8.1 except
''''''''''
>>> Post.where('id > 10').limit(20).order('id asc').except(:order)
8.2 only
''''''''
>>> Post.where('id > 10').limit(20).order('id desc').only(:order, :where)
8.3 reorder
'''''''''''
::

    class Post < ActiveRecord::Base
      ..
      ..
      has_many :comments, :order => 'posted_at DESC'
    end
     
    Post.find(10).comments.reorder('name')

8.4 reverse order
'''''''''''''''''
>>> Client.where("orders_count > 10").order(:name).reverse_order
如果之前没指定顺序，那么reverse_order之后以id的逆序排列
>>> Client.where("orders_count > 10").reverse_order
9 只读对象 Readonly Objects
^^^^^^^^^^^^^^^^^^
::
    client = Client.readonly.first
    client.visits += 1
    client.save
will raise ActiveRecord::ReadOnlyRecord exception

10 Locking Records for Update
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
两种机制：更新数据库时防止竞态保证原子操作
Java Hibernate中的乐观锁和悲观锁<http://www.cnblogs.com/guyufei/archive/2011/01/10/1931632.html>
10.1 乐观锁 Optimistic Locking
'''''''''''''''''''''''
在数据库中使用锁版本列保证不发生冲突，如果保存时发现版本不一致则raise ActiveRecord::StableObjectError
重写锁列::

    class Client < ActiveRecord::Base
        set_locking_column :lock_client_column
    end
10.2 悲观锁 Pessimistic Locking
''''''''''''''''''''''''
直接使用数据库提供的锁功能
11 Joining Tables
^^^^^^^^^^^^^^^^^
11.1 Using a String SQL Fragment
''''''''''''''''''''''''''''''''
>>> Client.joins('LEFT OUTER JOIN addresses ON addresses.client_id = clients.id')
11.2 Using Array/Hash of Named Associations
'''''''''''''''''''''''''''''''''''''''''''
假设这样的关系::

    class Category < ActiveRecord::Base
      has_many :posts
    end
     
    class Post < ActiveRecord::Base
      belongs_to :category
      has_many :comments
      has_many :tags
    end
     
    class Comment < ActiveRecord::Base
      belongs_to :post
      has_one :guest
    end
     
    class Guest < ActiveRecord::Base
      belongs_to :comment
    end
     
    class Tag < ActiveRecord::Base
      belongs_to :post
    end
下列方法产生INNER JOIN:
单个关系:
>>> Category.joins(:posts)
相当于:
>>> SELECT categories.* FROM categories INNER JOIN posts ON posts.category_id = categories.id
多个关系:
>>> Post.joins(:category, :comments)
单层嵌套关系:
>>> Post.joins(:comments => :guest)
多层嵌套关系:
>>> Category.joins(:posts => [{:comments => :guest}, :tags])
11.3 Specifying Conditions on the Joined Tables
'''''''''''''''''''''''''''''''''''''''''''''''
在joined table上可以正常使用Array和String条件，Hash条件这样来用::

    time_range = (Time.now.midnight - 1.day)..Time.now.midnight
    Client.joins(:orders).where('orders.created_at' => time_range)

或者这样来嵌套Hash条件::

    time_range = (Time.now.midnight - 1.day)..Time.now.midnight
    Client.joins(:orders).where(:orders => {:created_at => time_range})

12 贪婪加载Eager Loading Associations
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
防止重复性查询数据库::

    clients = Client.includes(:address).limit(10)

    client.each do |client|
        puts client.address.postcode
    end

12.1 Eager Loading Multiple Associations
''''''''''''''''''''''''''''''''''''''''
多个:
>>> Post.includes(:category, :comments)
嵌套:
>>> Category.includes(:posts => [{:comments => :guest}, :tags).find(1)
12.2 给贪婪加载的关联指定条件Specifying Conditions on Eager Loaded Associations
''''''''''''''''''''''''''''''''''''''''''
建议用joins连接表代替，如果非要用where也可以
>>> Post.includes(:comments).where("comments.visible", true)
这里如果在 includes 的查询时， post 没有任何 comment ，那依然会加载所有的 post 记录。如果使用 joins （使用 INNER JOIN ），那就不会返回任何记录。
13 作用域Scopes
^^^^^^^^^
最关键的:会返回ActiveRecord::Relation对象，以便进一步被其他方法调用
简单定义::
    class Post < ActiveRecord::Base
      scope :published, where(:published => true)
    end
级连定义::
    class Post < ActiveRecord::Base
      scope :publiced, where(:published => true).joins(:category)
    end
在Scopes之间级连::
    class Post < ActiveRecord::Base
      scope :published, where(:published => true)
      scope :published_and_commented, published.and(self.arel_table[:comments_count].gt(0))
调用:类调用
>>> Post.published
对象调用
>>> category = Category.first
>>> category.posts.published
13.1 处理时间Working with times
'''''''''''''''''''''''
如果在作用域里面包含日期或者时间，你必需使用 lambda 来保证它们每次都被重新计算::
    class Post < ActiveRecord::Base
      scope :created_before_now, lambda { where("created_at < ?", Time.zone.now) }
    end
如果不使用 lambda ， Time.zone.now 将只调用一次。
13.2 传递参数Passing in arguments
'''''''''''''''''''''''''
当在 scope 里面使用 lambda 时，它可以带参数::
    class Post < ActiveRecord::Base
      scope :1_week_before, lambda { |time| where("created_at < ?", time) }
    end 
调用:
>>> Post.1_week_before(Time.zone.now)
这只是相当于实现了一个类方法的功能，类方法是给作用域传递参数的一个更好的方式::
    class Post < ActiveRecord::Base
      def self.1_week_before(time)
        where("created_at < ?", time)
      end
    end
同样可以被对象调用：
>>> category.posts.1_week_before(time)
13.3 什么情况下需要作用域Working with scopes
''''''''''''''''''''''''
结果需要进一步被（其他方法）调用时
13.4 默认执行的Scope Applying a default scope
'''''''''''''''''''''''''''''
如果我们希望定义一个 model 里面所有的查询都会执行的作用域，那我们可以在 model 里面使用 default_scope 方法::
    class Client < ActiveRecord::Base
      default_scope where("removed_at is NULL")
    end
13.5 Removing all scoping
'''''''''''''''''''''''''
unscoped尤其在移除default_scope时有用
>>> Client.unscoped.all
会移除所有scoping包括default_scope
14 动态查找方法Dynamic Finders
^^^^^^^^^^^^^^^^^^
ActiveRecord为每个字段都提供了 find_by_* 和 find_all_by_* 方法
find_last_by_* 来找到最后一条匹配的记录
find_by_*! 保证查不到记录时抛出ActiveRecord::RecordNotFound异常
中间and串联多个动态查找方法 find_by_name_and_sex
15 Find or build a new object
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
查找一条记录并在它不存在时创建它
15.1 first or create
''''''''''''''''''''
>>> Client.where(:first_name => 'Andy').first_or_create(:locked => false)
>>> Client.find_or_create_by_first_name(:first_name => "Andy", :locked => false)
我们鼓励使用前者，因为它能明确指定哪些参数传给 find 方法，哪些参数传给 create 方法，这样能避免很多混乱。
15.2 first or create!
'''''''''''''''''''''
当新记录不合法时，就会抛出一个异常
15.3 first or initialize
''''''''''''''''''''''''
first_or_initialize 方法和 first_or_create 方法差不多，只是它最后调用 new 而不是create 方法。那就是说在内存中创建了一个新 model 实例，而没有保存到数据库中
保存请调用save方法
16 Finding by SQL
^^^^^^^^^^^^^^^^^
直接使用 SQL 语句查找记录
find_by_sql
17 select all
^^^^^^^^^^^^^
find_by_sql 有一个近似方法叫 connection#select_all 。像 find_by_sql 一样， select_all 方法直接用 SQL 语句从数据库里面查找数据，但是并不实例化它们。它会返回一个包含映射表的数组，每个映射表代表了一条记录。
18 pluck
^^^^^^^^
pluck 用于从一个 model 相关的数据表中查询一个单独的列。它接受一个列名作为参数并以相应的数据类型返回一组值。
19 Existence of Objects
^^^^^^^^^^^^^^^^^^^^^^^
>>> Client.exists?(1)

多个id作为参数，只要其中之一满足即返回true
>>> Client.exists?(1,2,3)
或者
>>> Client.exists?([1,2,3]}

>>> Client.where(:first_name => 'Ryan').exists?

>>> Client.exists?

其他方法::
    # via a model
    Post.any?
    Post.many?
     
    # via a named scope
    Post.recent.any?
    Post.recent.many?
     
    # via a relation
    Post.where(:published => true).any?
    Post.where(:published => true).many?
     
    # via an association
    Post.first.categories.any?
    Post.first.categories.many?
20 Calculations
^^^^^^^^^^^^^^^
所有的计算方法都直接在 model 上，或者 relation 实例对象上使用
count, average, minimum, maximum, sum
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
Active Record 可以在慢查询中自动执行 EXPLAIN 并写入日志。

这个功能需要加上配置参数来开启
>>> config.active_record.auto_explain_threshold_in_seconds

如果设置为一个数字，那一个超过这些时间（以秒为单位）的查询就会自动触发一个 EXPLAIN 并写入日志。在 relation 的计算中，这个阈值是与获取记录的总时间相比较的。所以，一个 relation 的计算就像是一个工作单位，不管它是不是实现预先加载而涉及了多个查询。
阈值为 nil 则关闭了自动执行 EXPLAIN 。
默认情况下，开发模式下的阈值为 0.5 秒，测试和生产模式下为 nil 。
21.2 Interpreting EXPLAIN
'''''''''''''''''''''''''
Layouts and Rendering in Rails
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
1 Overview: How the Pieces Fit Together
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
2 Creating Responses
^^^^^^^^^^^^^^^^^^^^
1. Call render to create a full response to send back to the browser
* render nothing
* render an action's view
* render another template within the controller
* render a template within another controller
* render an arbitrary file on the file system
* :inline, :inline with :type => :builder
* render :text 常用于 *AJAX* 和非HTML的web服务请求。:text默认关闭:layout，可以手动打开
* render :json 自动调用to_json
* render :xml
* render vanilla JavaScript, :js
render的可用选项
:content-type, :layout, :status, :location
layout:
* 在controller中指定layout，存储layout为app/views/layouts/***.html.erb
* 项目主layout在ApplicationController中指定
* 可在运行时选择layout(为特定的情况选择特定的layout)
* inline layout
* 有条件的layout
* layout继承
避免双重render: 提示Can only render or redirect once per action错误
2. Call redirect_to to send an HTTP redirect status code to the browser
3. Call head to create a response consisting solely of HTTP headers to send back to the browser

